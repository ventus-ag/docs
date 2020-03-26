---
title: Quickstarts
description: Ventus Cloud Platform Quick Start Tasks
---

# Quickstarts
{: .no_toc }

{% include alert.html type="info" title="Welcome to Quickstarts!" content="Here you can quickly jump to useful recipes" %}


<div class="section-index">
    <hr class="panel-line">
    {% for post in site.docs  %}        
    {% if post.tags contains "quickstart" %}
    <div class="entry">
    <h5><a href="{{ post.url | prepend: site.baseurl }}">{{ post.title }}</a></h5>
    <p>{{ post.description }}</p>
    </div>
    {% endif %}
    {% endfor %}
</div>