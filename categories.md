---
layout: default
title: Categorías
permalink: /categorias
---

<p class="site">
@ <a href="/">{{ site.name }}</a>
</p>

<h1>Categorías</h1>

<script type="text/javascript">
function handle_selection(element) {
    if (element.value === "#all") {
        document.querySelectorAll('.catbloc').forEach(function (el) {
            el.classList.replace('catbloc', 'catbloc_showing_all');
        });
        history.replaceState(null, '', window.location.pathname);
    } else {
        document.querySelectorAll('.catbloc_showing_all').forEach(function (el) {
            el.classList.replace('catbloc_showing_all', 'catbloc');
        });
        window.location.hash = element.value;
    }
}
</script>

<nav>
    <select id="category_selector" name="Categories" onchange="handle_selection(this);">
        <option value="#all" selected="selected">Todos los posts</option>
        {% for category in site.categories %}
            <option value="#{{ category | first | remove:' ' }}">{{ category | first }}</option>
        {% endfor %}
    </select>
</nav>

<div>
    {% for category in site.categories %}
	<div class="catbloc_showing_all" id="{{ category | first | remove:' ' }}">
    	<h2 class="category_header">{{ category | first }}</h2>

    	<ul class="index-list">
        	{% for posts in category %}
            	{% for post in posts %}
                	{% if post.url %}
                    	<li>
                        	<a href="{{ post.url }}">{{ post.title }}</a><br>
                        	<small>{{ post.date | date: '%d de' }}
                                {% assign m = post.date | date: "%-m" %}
                                {% case m %}
                                    {% when '1' %}enero
                                    {% when '2' %}febrero
                                    {% when '3' %}marzo
                                    {% when '4' %}abril
                                    {% when '5' %}mayo
                                    {% when '6' %}junio
                                    {% when '7' %}julio
                                    {% when '8' %}agosto
                                    {% when '9' %}septiembre
                                    {% when '10' %}octubre
                                    {% when '11' %}noviembre
                                    {% when '12' %}diciembre
                                {% endcase %}
                                {{ post.date | date: 'de %Y' }}</small>
                    	</li>
                	{% endif %}
            	{% endfor %}
        	{% endfor %}
    	</ul>
	</div>
	{% endfor %}
</div>
