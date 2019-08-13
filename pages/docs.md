---
layout: page
title: Documentation
permalink: /docs/
---

# Documentation

Welcome to the {{ site.title }} Documentation pages! Here you can quickly jump to a 
particular page.

<div class="section-index">
    <hr class="panel-line">
    {% for post in site.related_posts  %}        
    {% for category in post.categories %}
    {% if page.categories contains featured %}
    <div class="entry">
    <h5><a href="{{ post.url | prepend: site.baseurl }}">{{ post.title }}</a></h5>
    <p>{{ post.description }}</p>
    </div>
{% endif %}
{% endfor %}
{% endfor %}
</div>
