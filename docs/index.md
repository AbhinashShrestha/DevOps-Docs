# DevOps Notes

Short, task-focused notes collected over time.

## Quick start
- Use the left sidebar to browse by filename/folder.
- Use browser search (`Ctrl+F`) inside a page.

## Common topics
- Kubernetes / K3s
- CI/CD (Jenkins, GitLab, Argo CD)
- Observability (ELK, Prometheus, Grafana/Loki)
- Nginx / HAProxy
- Storage and Linux tooling

---

## All pages
Below is an automatically generated list of every page in this site.

{% for nav_item in nav %}
- [{{ nav_item.title }}]({{ nav_item.url }})
  {% if nav_item.children %}
  {% for child in nav_item.children %}
  - [{{ child.title }}]({{ child.url }})
    {% if child.children %}
    {% for grandchild in child.children %}
    - [{{ grandchild.title }}]({{ grandchild.url }})
    {% endfor %}
    {% endif %}
  {% endfor %}
  {% endif %}
{% endfor %}