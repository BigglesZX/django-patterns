# Pip Requirements

Typical preferred project requirements with upgrade restriction notes.

```
Django==1.11.3
boto==2.48.0
coverage==4.4.1
django-admin-sortable2==0.6.14
django-autoslug==1.9.3
django-cleanup==1.0.1
django-debug-toolbar==1.8
django-nose==1.4.4
django-recaptcha==1.3.1
django-storages==1.6.3
django-tinymce==2.6.0
django-widget-tweaks==1.4.1
easy-thumbnails==2.4.1
factory-boy==2.8.1
flake8==3.3.0
freezegun==0.3.9
gunicorn==19.7.1
ipdb==0.10.2  # do not upgrade on python 2.7
ipython==5.4.1  # do not upgrade past 5.x.x on python 2.7
MySQL-python==1.2.5
Pillow==4.2.1
pip-tools==0.3.6  # do not upgrade past 0.3.6 in order to keep pip-review
pyflakes==1.5.0
pytz  # do not pin to version, needs to be kept current
raven==5.0.0  # do not upgrade while still using Heroku-based Sentry
```
