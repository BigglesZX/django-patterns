# Tidy Pagination

To display a fixed number of page links in a paginator, regardless of which page is being viewed (e.g. first, middle, last), the following was arrived at after much scratching of heads.

Template tags definition, e.g. `paginationhelper.py`:

```python
from django import template
from math import floor

register = template.Library()


def get_page_ranges(page_obj):
    display = 5  # should be an odd number, really
    display_each_side = floor(display / 2)
    pages = list(page_obj.paginator.page_range)

    pages_before = range(page_obj.paginator.page_range[0], page_obj.number)
    pages_after = range(page_obj.number + 1, page_obj.paginator.page_range[-1] + 1)

    left_delta = display_each_side - len(pages_before)
    right_delta = display_each_side - len(pages_after)

    if left_delta > 0 and right_delta > 0:
        ''' both sides are short '''
        return (pages_before, pages_after)
    elif left_delta > 0:
        ''' left side is short '''
        return (pages_before, pages_after[0:min(len(pages_after), display_each_side + left_delta)])
    elif right_delta > 0:
        ''' right side is short '''
        return (pages_before[max(-len(pages_before), -display_each_side - right_delta):], pages_after)
    else:
        ''' neither side is short '''
        return (pages_before[-display_each_side:], pages_after[:display_each_side])

@register.filter
def page_range_before(page_obj):
    pages_before, pages_after = get_page_ranges(page_obj)
    return pages_before


@register.filter
def page_range_after(page_obj):
    pages_before, pages_after = get_page_ranges(page_obj)
    return pages_after
```

And in the template:

```html
{% load paginationhelper %}

{% for p in page_obj|page_range_before %}
<a href="?page={{ p }}">{{ p }}</a>
{% endfor %}
<a>{{ page_obj.number }}</a>
{% for p in page_obj|page_range_after %}
<a href="?page={{ p }}">{{ p }}</a>
{% endfor %}
```

See also: [PaginatingSearchResults](https://github.com/BigglesZX/django-patterns/blob/master/PaginatingSearchResults.md)
