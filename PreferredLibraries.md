# Preferred Libraries

## Automatic cleanup of FileFields

`django-cleanup` works seamlessly via signals.

## Image cropping controls in the admin

Have had previous success with `django-image-cropping` though should investigate current library landscape.

## Mailchimp integration

For simple mailing list sign-up without CMS integration just use the `mailchimp` library (`pip install mailchimp`).

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
