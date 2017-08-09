# Active Nav Items

A fairly common need is to be able to programmatically designate a navigation item "active" so as to highlight it somehow on the frontend. These items may be defined statically or they may come from the CMS. I've tried various approaches before including path inspection in the template, but I now consider the following to the be way forward.

Define a context processor that defines and/or prepares navigation items. For each, you can inspect the request's path or `resolver_match` properties to determine whether a give item should be considered active. This has a lot of flexibility, being able to match particular views, parameter combinations, paths or even whole apps. Note for the final example, you need to set `app_name` when using `include()` in your urlconf.

This same approach could be made to work using CMS-derived nav items, though depending on what they link to (is it URL strings, urlpatterns, FK objects etc) you may require some gymnastics. Fundamentally though you could cast them to a list and follow something like the below, adding an `is_active` attribute to each object.

```python
def navigation_items(request):
    ''' Add global navigation items to the current context '''
    items = [
        {
            'text': 'Home',
            'href': reverse('content:home'),
            'is_active': request.resolver_match.url_name == 'home',
        },
        {
            'text': 'About',
            'href': reverse('content:page', args=('about',)),
            'is_active': request.path_info == reverse('content:page', args=('about',)),
        },
        {
            'text': 'Events',
            'href': reverse('events:list'),
            'is_active': request.resolver_match.app_name == 'events'
        },
    ]
    return {
        'NAVIGATION_ITEMS': items,
    }
```
