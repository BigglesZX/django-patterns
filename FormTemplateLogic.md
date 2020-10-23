# Form Template Logic

In order to make decisions in template logic based on form field class, input type and so on, install `django-widget-tweaks` and use its template filters (as well as the `input_type` property since Django 1.11):

`forms.py`:

```python
class ContactForm(forms.Form):
    name = forms.CharField(
        max_length=150,
        required=True,
        label='Your name'
    )
```

`template.html`:

```liquid
{% load widget_tweaks %}

{% for field in form.visible_fields %}
{{ field.label }}
{{ field.field.widget.input_type }}
{{ field|field_type }}
{{ field|widget_type }})
{% endfor %}
```

**Result:**

```
Your name
text
charfield
textinput
```

If you need to capture the output of one of these filters for use in `if` statements, you can use the `with` template tag.

Since Django 3.1, there's also the [`widget_type`](https://docs.djangoproject.com/en/3.1/ref/forms/api/#django.forms.BoundField.widget_type) field attribute:

```liquid
{% for field in form %}
    {% if field.widget_type == 'checkbox' %}
        # render one way
    {% else %}
        # render another way
    {% endif %}
{% endfor %}
```
