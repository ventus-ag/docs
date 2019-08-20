---
title: Core Tasks
description: Ventus Cloud Platform Core Tasks
---

# Core Tasks
{: .no_toc }

{% include alert.html type="info" title="Welcome to Core Tasks!" content="Here you can quickly jump to core tasks which could be useful for implementing basic actions" %}


<div class="section-index">
    <hr class="panel-line">
    {% for post in site.docs  %}        
    {% if post.tags contains "coretasks" %}
    <div class="entry">
    <h5><a href="{{ post.url | prepend: site.baseurl }}">{{ post.title }}</a></h5>
    <p>{{ post.description }}</p>
    </div>
    {% endif %}
    {% endfor %}
</div>