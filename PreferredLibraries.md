# Preferred Libraries

## Automatic cleanup of FileFields

`django-cleanup` works seamlessly via signals.

## Currency storage

Use `django-money`, add something like `CURRENCIES = ('GBP',)` to settings and use a field definition along the lines of this:

```python
price = MoneyField(
    'Price',
    max_digits=20,
    decimal_places=2,
    default_currency='GBP'
)
```

In template:

```liquid
{% load djmoney %}

...

{% money_localize object.price %}
```

## Database-based config/settings

Use `django-constance` to supplement runtime settings with database-backed configuration. Be aware that as of July 2018 `django-constance` appears to have an undeclared dependency on `django-picklefield`, so you'll need to install that as well.

## Inlining static files in templates

E.g. priority JavaScript files, SVG Symbol files etc. Use `django-simpleinliner` (full disclosure: I wrote it).

## Image cropping controls in the admin

Have had previous success with `django-image-cropping` though should investigate current library landscape.

## Mailchimp integration

For simple mailing list sign-up without CMS integration just use the `mailchimp` library (`pip install mailchimp`).

## Measurement/dimensions fields on models

Use `django-measurement` but beware providing multiple unit choices – it can screw up when dealing with existing values. I instead limit to one unit, but then use the field properties to output in multiple units:

```python
from django_measurement.models import MeasurementField
from measurement.measures import Distance

...

width = MeasurementField(
    'Width',
    blank=True,
    null=True,
    measurement=Distance,
    unit_choices=(('cm', 'cm'),),
)
```

In template:

```liquid
<p>Width</p>
<p>{{ object.width.cm|floatformat }}cm</p>
<p>{{ object.width.inch|floatformat }}”</p>
```

## Model translation

`django-modeltranslation` seems to be best in class.

## Modify form field widgets at the template level

`django-widget-tweaks` provides template tags for this, e.g. `{{ field|add_class:'form__input' }}` or `{% render_field form.search_query type="search" %}`.

## ReCAPTCHA

The canonical ReCAPTCHA library is `django-recaptcha`.

## Simple colour picker field

`django-colorfield` is lightweight and works well.

## Sortable models and inlines

`django-admin-sortable2` provides the simplest integration and seemingly the most effective UI.

## WYSIWYG editing

`django-ckeditor` is better than TinyMCE, more easily configurable and doesn't require you to manage any static files. Here's a sensible default config:

```python
CKEDITOR_CONFIGS = {
    'default': {
        'toolbar': [
            ['Format'],
            ['BulletedList', 'NumberedList'],
            ['Bold', 'Italic', 'Subscript', 'Superscript', '-',
             'RemoveFormat'],
            ['Link', 'Unlink'],
            ['Undo', 'Redo'],
            ['Source']
        ],
        'format_tags': 'p;h1;h2',
        'format_h1': {'element': 'h3'},
        'format_h2': {'element': 'h4'},
        'removePlugins': 'elementspath,save,font',
        'language': 'en-us'
    },
}
```
