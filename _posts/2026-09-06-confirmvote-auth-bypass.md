---
layout: post
title: confirmVote returns the Vote Cast Return Code without verifying the authentication challenge on replayed requests
categories: [vulnerabilidades, evoting, java]
permalink: "blog/confirmvote-auth-bypass"
published: yes
---

[Swiss Post - E-Voting](https://yeswehack.com/programs/swiss-post-evoting)
Submitted by **rebcesp** on Sun, 6 Sep 2026

## Description

The `confirmVote` endpoint is wrapped in an idempotency mechanism. On the first request, the server verifies the authentication challenge, asks the control components for the short Vote Cast Return Code, and stores the result. On repeated requests, it serves the cached code through a second code path that never verifies the authentication challenge.

The payload hash used to match repeated requests excludes the `authenticationChallenge` field by design (so that legitimate retries with a fresh nonce are still recognized as the same request). The consequence is that a request carrying the same `contextIds`, `encryptionGroup` and `confirmationKey` as the original, but a completely forged `authenticationChallenge`, produces an identical hash, hits the idempotent path, and receives the cached Vote Cast Return Code without any authentication taking place.

An attacker who obtains a single legitimate `confirmVote` request (traffic interception, malware or a browser extension on the voter's device, XSS, or access to the voter's printed voting materials) can replay it with a fake challenge and get the voter's Vote Cast Return Code. The vote must already be confirmed, and the replay must happen while the ballot box is open (including the grace period).

## Root cause

Three pieces of code combine here.

**1. The hash excludes the auth challenge** — `ConfirmVotePayload.java`:

```java
/**
 * Intentionally ignore the authenticationChallenge in order to allow the
 * idempotency service to ignore the nonce part which changes at each new
 * request (outside of time window).
 */
@Override
public ImmutableList<Hashable> toHashableForm() {
    return ImmutableList.of(
        contextIds,
        encryptionGroup,
        confirmationKey
        // authenticationChallenge is excluded
    );
}
```

Two payloads with different auth challenges hash identically.

**2. The execution key does not change after a successful confirmation** — `ConfirmVoteController.java`:

```java
final int attemptId = verificationCardService.getNextConfirmationAttemptId(verificationCardId);
final String executionKey = String.format("%s-%s-%s-%s-%s",
    contextIds.electionEventId(), contextIds.verificationCardSetId(),
    contextIds.verificationCardId(), credentialId, attemptId);
```

`getNextConfirmationAttemptId()` reads the attempt counter but does not increment it on success (the counter only moves on failed confirmations), so a replayed request produces the same `executionKey` as the original.

**3. The idempotent getter path skips authentication** — `ConfirmVoteController.java`:

```java
final Supplier<CompletableFuture<String>> execution = () -> {
    verifyAuthenticationChallengeService.verifyAuthenticationChallenge(
        electionEventId, AuthenticationStep.CONFIRM_VOTE, authenticationChallenge);
    return confirmVoteService.retrieveShortVoteCastCode(contextIds, confirmVotePayload.confirmationKey());
};

final Supplier<CompletableFuture<String>> getter = () ->
    // Here we don't need to verify the challenge again because we will bypass the execution.
    confirmVoteService.getShortVoteCastReturnCode(contextIds, credentialId);
```

And `IdempotenceService.execute()`:

```java
if (!exists(idempotentExecutionId)) {
    final U result = execution.get();   // first request: auth verified
    save(idempotentExecutionId, payloadHash);
    return result;
} else {
    ...
    return getter.get();                // replay: auth never verified
}
```

The comment in the getter is the developers' own: they knowingly skip challenge verification on this path, assuming the caller is always the same voter retrying. Nothing distinguishes a legitimate retry from a third party replaying the request with a forged challenge.

## Attack flow

Victim votes:

1. Voter POSTs `confirmVote` with a valid auth challenge.
2. Server: `attemptId = 0`, `executionKey = "ee-vcs-vc-cred-0"`, no prior entry in `IDEMPOTENT_EXECUTION` → execution path.
3. `verifyAuthenticationChallenge()` passes, the short Vote Cast Return Code is computed and returned.
4. The hash of (`contextIds` + `encryptionGroup` + `confirmationKey`) is saved.

Attacker replays:

1. Attacker POSTs `confirmVote` with the same `contextIds`/`confirmationKey` but a forged `authenticationChallenge`. Only the `derivedVoterIdentifier` field of the challenge has to match the victim's `credentialId` from the URL path (checked in the controller before idempotency); the challenge value and nonce are never verified on this path.
2. Server: `attemptId` is still 0 (never incremented on success), same `executionKey` → entry exists.
3. Payload hash matches (auth challenge excluded from the hash).
4. Getter path runs: `getShortVoteCastReturnCode()` reads the code straight from the database and returns it. `verifyAuthenticationChallenge()` is never called.
5. Attacker receives the voter's Vote Cast Return Code.

## Proof of concept

I wrote two tests against the unmodified source of tag `e-voting-1.6.1.0`. Both pass.

### Integration test (real PostgreSQL + real IdempotenceService)

File: `voting-server/src/test/java/.../confirmvote/ConfirmVoteIdempotencyIntegrationPoC.java`

```bash
mvn test -pl voting-server -Dtest=ConfirmVoteIdempotencyIntegrationPoC -DskipHashGeneration -Dsurefire.failIfNoSpecifiedTests=false
```

Output:

```text
========================================
INTEGRATION PoC: ConfirmVote Auth Bypass
========================================
Database: PostgreSQL (real, via Testcontainers)
IdempotenceService: REAL (no mock)
Hash: REAL (HashFactory.createHash)
ConfirmVotePayload: REAL (with real GqGroup, GqElement)

Legitimate auth challenge: AQIDBAUGBwgJCgsMDQ4PEBESExQVFhcYGRobHB0eHyA=
Replay auth challenge:     ISIjJCUmJygpKissLS4vMDEyMzQ1Njc4OTo7PD0+P0A=
Legitimate nonce: 123456789
Replay nonce:     999999999

--- STEP 1: Legitimate request ---
[execution] verifyAuthenticationChallenge CALLED (call #1)
Result: 789012
verifyAuth calls so far: 1

--- STEP 2: Replay with FAKE auth challenge ---
[IdempotenceService] WARN: Execution bypassed. [context: CONFIRM_VOTE, executionKey: ee-vcs-vc-cred-0]
[getter] verifyAuthenticationChallenge NOT CALLED (bypassed!)
Result: 789012
verifyAuth calls so far: 1

=== VULNERABILITY CONFIRMED ===
1. Both requests returned the same Vote Cast Return Code: 789012
2. verifyAuthenticationChallenge was called only 1 time(s) (NOT on replay)
3. The replay used a COMPLETELY FAKE authentication challenge
4. This was done with REAL PostgreSQL, REAL IdempotenceService, REAL Hash
========================================

Tests run: 2, Failures: 0, Errors: 0, Skipped: 0 — BUILD SUCCESS
```

The `IdempotenceService`, the `Hash` (`HashFactory.createHash`) and the `ConfirmVotePayload` (real `GqGroup`, `GqElement`, `AuthenticationChallenge`) are the project's real classes. The only stub is `verifyAuthenticationChallenge`, replaced by a counter, because the real implementation needs the control-component message broker infrastructure (4 nodes) to run. What is being tested is whether it gets called on the replay path, not what it does. It does not: the counter stays at 1 after the replay.

A second test in the same file verifies the `IDEMPOTENT_EXECUTION` table in PostgreSQL contains the stored hash after the first request (the table schema is taken from the project's own migration `V1.001__voting_server_baseline_postgres.sql`).

Environment: JDK Eclipse Temurin 25.0.3, Maven 3.9.11, PostgreSQL 17.9 via Testcontainers, Windows 11.

### Hash collision test

File: `voting-server/src/test/java/.../confirmvote/ConfirmVoteHashCollisionPoC.java`

```bash
mvn test -pl voting-server -Dtest=ConfirmVoteHashCollisionPoC -DskipHashGeneration -DfailIfNoTests=false
```

Output:

```text
========================================
PoC: ConfirmVote Hash Collision
========================================
Legitimate auth challenge: AQIDBAUGBwgJCgsMDQ4PEBESExQVFhcYGRobHB0eHyA=
Replay auth challenge:     ISIjJCUmJygpKissLS4vMDEyMzQ1Njc4OTo7PD0+P0A=
Legitimate nonce: 123456789
Replay nonce:     999999999

toHashableForm() equal? true

Hash legitimate: b9967f3cf602e9e573f21a5a99238b08ffdeee1add1b8dfcf00c104b6ee8b068
Hash replay:     b9967f3cf602e9e573f21a5a99238b08ffdeee1add1b8dfcf00c104b6ee8b068
Hashes equal? true
========================================
```

Two payloads with completely different challenges and nonces produce the same `recursiveHash`. This is the exact hash the `IdempotenceService` uses for request matching. No Docker, no Spring context, no mocks: just the real crypto primitives.

Note on live testing: the demo instance at `demo.evoting.ch` is active but its `/api/v1/processor/...` endpoints sit behind a WAF (HTTP 403), so I could not drive the replay against it directly. The tests above run the same server code from the same release tag.

## Attack scenarios

**Interception.** A single capture of a `confirmVote` request gives the attacker every value needed: the URL path carries `electionEventId`, `verificationCardSetId`, `credentialId` and `verificationCardId`; the body carries `encryptionGroup` and `confirmationKey`. Vectors: MITM, XSS in the voter portal, malicious browser extension, malware on the voter's device, or misconfigured proxies/logs recording request bodies.

**Voter materials.** A coercer or vote buyer holding the voter's printed materials (mailed by Swiss Post) can derive the `credentialId` from the Start Voting Key and compute the `confirmationKey` from the Ballot Casting Key; both derivation algorithms (`DeriveCredentialIdAlgorithm`, confirmation key derivation) are public and deterministic. They can then confirm the victim already voted and replay without ever touching the voter portal.

**Unauthenticated enumeration (amplifier).** `VotingCardManagerController` exposes, with only an `x-tenant-id` header and no authentication (no Spring Security configuration anywhere in the module, only a multitenancy filter in `ContextWebFilter`):

- `GET /api/v1/votingcardmanager/electionevents/` — all election events
- `GET /api/v1/votingcardmanager/electionevents/{eeId}/votingcards/used` — every voter who already voted, with `verificationCardId`, `verificationCardSetId`, `votingCardId` and state `CONFIRMED` (per `UsedVotingCardDto`)

These endpoints do not expose `credentialId` or `confirmationKey`, so they do not complete the attack on their own. But they confirm participation at scale and narrow what the attacker still needs to a single intercepted request or one voter's materials.

## Impact

- Disclosure of the Vote Cast Return Code, a value only the voter should know. It proves their vote was cast and registered.
- Post-hoc vote buying and coercion verification: a buyer can prove a voter participated and demonstrate knowledge of their confirmation code, without the voter's cooperation at verification time and without being present during voting.
- Combined with the `used` voting cards endpoint, both participation and confirmation are exposed.
- No trace: the server logs `Execution bypassed` as a routine idempotent replay, and since `verifyAuthenticationChallenge` is never invoked, there is no failed-authentication log either.

Votes cannot be altered or cast on behalf of anyone: this is a confidentiality issue, not an integrity one.

## Suggested fix

Verify the challenge on the getter path as well (minimal change):

```java
final Supplier<CompletableFuture<String>> getter = () -> {
    verifyAuthenticationChallengeService.verifyAuthenticationChallenge(
        electionEventId, AuthenticationStep.CONFIRM_VOTE, authenticationChallenge);
    return confirmVoteService.getShortVoteCastReturnCode(contextIds, credentialId);
};
```

Alternatives: include the `authenticationChallenge` in `toHashableForm()` (breaks the intended retry-with-fresh-nonce semantics), or increment the attempt counter on success so a replay becomes a new execution and goes through full verification. Independently, the `votingcardmanager` endpoints should not be unauthenticated.

## References

- `ConfirmVoteController.java` lines 110-135 — idempotency setup, execution vs getter
- `ConfirmVotePayload.java` lines 46-57 — `toHashableForm()` excludes `authenticationChallenge`
- `IdempotenceService.java` lines 34-55 — exists/hash check choosing execution vs getter
- `VerificationCardStateService.java` lines 92-98 — `getNextConfirmationAttemptId` not incremented on success
- `ConfirmVoteService.java` lines 61-71 — `getShortVoteCastReturnCode` reads from DB without auth
- `VotingCardManagerController.java` lines 53-132 — unauthenticated GET endpoints
- `ContextWebFilter.java` lines 99-110 — `x-tenant-id` only, multitenancy not authentication
- `GetVoterAuthenticationDataAlgorithm.java` line 70 — `credentialId` derived from the Start Voting Key
- PoC tests: `ConfirmVoteIdempotencyIntegrationPoC.java`, `ConfirmVoteHashCollisionPoC.java` (raw outputs in `confirmvote_integration_poc_output.txt`, `confirmvote_poc_test_output.txt`)

Thanks, so much,
Greetz.
