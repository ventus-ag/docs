---
title: Kubernetes
description: Ventus Cloud Platform Kubernetes
---

# Kubernetes
{: .no_toc }

{% include alert.html type="info" title="Welcome to Kubernetes!" content="Here you can quickly jump to Kubernetes services provided by Ventus cloud." %}


<div class="section-index">
    <hr class="panel-line">
    {% for post in site.docs  %}        
    {% if post.tags contains "kubernetes" %}
    <div class="entry">
    <h5><a href="{{ post.url | prepend: site.baseurl }}">{{ post.title }}</a></h5>
    <p>{{ post.description }}</p>
    </div>
    {% endif %}
    {% endfor %}
</div>