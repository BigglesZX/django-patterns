# DRY Admin Thumbnails

I often want to add thumbnails to the Django admin list or change views, based on a field in the model being listed or changed, and after trying a few approaches to reduce repetition in this area this is my current go-to solution. I need to adapt this to be agnostic of use of `easy_thumbnails`, but that aside this should be pretty flexible.

```python
# easy_thumbnails config in site settings
THUMBNAIL_ALIASES = {
    '': {
        'admin_thumbnail': {'size': (100, 0)},
        # ...
    },
}
```

```python
# admin.py in site root

from django.db.models import FileField
from django.utils.safestring import mark_safe
from easy_thumbnails.fields import ThumbnailerImageField

def admin_thumbnail_factory(fieldName, *args, **kwargs):
    def admin_thumbnail(self, obj):
        field = obj._meta.get_field(fieldName)
        fieldValue = getattr(obj, fieldName)
        if not fieldValue:
            return ''
        if isinstance(field, ThumbnailerImageField):
            url = fieldValue['admin_thumbnail'].url
        elif isinstance(field, FileField):
            url = fieldValue.url
        else:
            raise TypeError(
                'Field supplied to admin_thumbnail should be an instance of '
                'easy_thumbnails’ ThumbnailerImageField or Django’s FileField '
                '(received: {0})'.format(field.get_internal_name())
            )
        style = 'width: 100px; height: auto; {0}' \
            .format('background: black;' if kwargs.get('bg') else '')
        return mark_safe(
            '<img src="{0}" style="{1}">'.format(url, style)
        )
    admin_thumbnail.short_description = args[0] if len(args) else 'Preview'
    return admin_thumbnail
```

Import and use in your app's `admin.py`:

```python
from django.contrib import admin

from ..admin import admin_thumbnail_factory
from . import models

@admin.register(models.Foo)
class FooAdmin(admin.ModelAdmin):
    list_display = ('title', 'description', 'logo_thumbnail')
    
    logo_thumbnail = admin_thumbnail_factory('logo')
```

The virtual field's name can be specified (it defaults to "Preview"):

```python
class FooAdmin(admin.ModelAdmin):
    # ...
    logo_thumbnail = admin_thumbnail_factory('logo', 'Thumbnail')
```

A black background can also be toggled:

```python
class FooAdmin(admin.ModelAdmin):
    ...
    logo_thumbnail = admin_thumbnail_factory('logo', bg=True)
```

For use in change views, just add the `*_thumbnail` (or whatever you've called it) virtual field to the ModelAdmin's `readonly_fields` or `fieldsets` properties.

Note that thumbnails are only updated when the model is saved (selecting a new file in a `FileField`/`ImageField`/`ThumbnailerImageField` input won't update the thumbnail. You might want to add a note to this effect in your admin UI if your users get confused.
