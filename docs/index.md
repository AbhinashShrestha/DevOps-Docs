---
title: DevOps Notes
---

# DevOps Notes

## All pages
<ul>
{% assign items = site.pages | sort: "title" %}
{% for p in items %}
  {% if p.name != "index.md" and p.path contains ".md" %}
    <li><a href="{{ p.url | relative_url }}">{{ p.title | default: p.name | replace: ".md","" }}</a></li>
  {% endif %}
{% endfor %}
</ul>