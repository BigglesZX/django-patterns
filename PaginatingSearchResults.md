# Paginating Search Results

Often the need arises to be able to replace a single GET parameter in a view from the template – most often when dealing with paginated objects that can also be dynamically sorted or filtered based on user input, e.g. search results. Here's a simple solution for that.

Add a new template tag file:

```python
from django import template
from urllib.parse import urlencode


register = template.Library()


@register.simple_tag
def build_absolute_uri(request, path):
    if type(request).__name__ != 'WSGIRequest':
        return path
    return request.build_absolute_uri(path)


@register.simple_tag(takes_context=True)
def url_replace(context, **kwargs):
    ''' source: https://stackoverflow.com/a/36288962/258794 '''
    query = context['request'].GET.dict()
    query.update(kwargs)
    return urlencode(query)
```

Load it in the template:

```
{% load urlhelper %}
```

Use it thus:

```html
<p>
    {% if page_obj.has_previous %}
    <a href="?{% url_replace page=page_obj.previous_page_number %}">&lt; previous</a>
    {% endif %}
</p>
<p>
    Page {{ page_obj.number }} of {{ page_obj.paginator.num_pages }}
</p>
<p>
    {% if page_obj.has_next %}
    <a href="?{% url_replace page=page_obj.next_page_number %}">next &gt;</a>
    {% endif %}
</p>
```
