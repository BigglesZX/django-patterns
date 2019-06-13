# Custom Admin Views With Context

When writing custom admin views via extending the `AdminSite` or `ModelAdmin` classes' `get_urls` method, or by manually including URLs, I couldn't figure out how I was meant to ensure my admin-facing templates received the same context as regular admin views, for data like `title`, `site_header` etc. Without this, the full admin UI won't render.

The solution, once I found it, was pretty simple:

```python
# views.py
from django.contrib import admin
from django.views.generic import View

from .models import MyModel


class MyCustomAdminView(View):
    model = MyModel
    
    # ...
    
    def get_context_data(self, **kwargs):
        context = super(MyCustomAdminView, self).get_context_data(**kwargs)
        context.update(admin.site.each_context(self.request))
        context.update({
            'opts': self.model._meta,
            'title': 'Page title for my custom view',
        })
        return context
```

* Calling the `AdminSite`'s `each_context` method provides a bunch of useful data that the admin UI requires to function properly.
* The `opts` context variable holds various model metadata and is used in the breadcrumb component of the admin UI.
* Finally, the `title` variable populates the page's `<title>` tag as well as an `<h1>` located at the top of most of the standard admin templates.

For use with multiple views, you might find it easier to include the above in a mixin. And don't forget to run your own permission checks inside the views you create.
