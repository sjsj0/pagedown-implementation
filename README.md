Django Pagedown
===============

A Django form/widget that integrates the Pagedown Markdown editor (the editor used by Stack Exchange) into Django admin and custom forms.

This package provides a simple, configurable widget for editing Markdown with live preview and optional image upload support.

Key features

- Tiny, dependency-free widget that works in Django Admin and regular forms
- Live Markdown preview powered by Pagedown
- Optional image upload endpoint for inserting uploaded images directly into the editor
- Easy to customize templates, CSS, and JS

Requirements

- Python: compatible with the Django versions supported by this package (see package metadata / PyPI for exact compatibility)
- Django: 2.1+ for the most recent versions (older releases of this package support earlier Django versions)

Installation

1. Add the app to your Django project's `INSTALLED_APPS` (use the app config):

   ```python
   INSTALLED_APPS = [
       # ...
       'pagedown.apps.PagedownConfig',
       # ...
   ]
   ```

2. Collect static files for production deployments:

   ```bash
   python manage.py collectstatic
   ```

Quick start: Using the widget
------------------------------

### Inside Django Admin (apply to all TextField fields):

- Create or update an admin registration to use the `AdminPagedownWidget` for `TextField` form fields.

Example:

```python
from django.contrib import admin
from django.db import models

from pagedown.widgets import AdminPagedownWidget


class MyModelAdmin(admin.ModelAdmin):
    formfield_overrides = {
        models.TextField: {'widget': AdminPagedownWidget},
    }
```

### Outside Django Admin (use in a ModelForm):

```python
from django import forms
from pagedown.widgets import PagedownWidget
from myapp.models import Article


class ArticleForm(forms.ModelForm):
    body = forms.CharField(widget=PagedownWidget())

    class Meta:
        model = Article
        fields = ['title', 'body']
```

In templates, render the form media so the editor's JS/CSS are loaded:

```html
<!doctype html>
<html lang="en">
<head>
    <meta charset="utf-8">
    <title>Example form</title>
    {{ form.media }}
</head>
<body>
    <form method="post">{{ form.as_p }}</form>
</body>
</html>
```

Image uploads
-------------

This package can optionally provide an image upload endpoint which accepts image files and returns a URL that can be inserted into the editor content.

To enable image uploads:

1. Configure Django's media settings in your project `settings.py`:

   ```python
   MEDIA_URL = '/media/'
   MEDIA_ROOT = os.path.join(BASE_DIR, 'media')
   ```

2. Enable uploads via settings:

   ```python
   PAGEDOWN_IMAGE_UPLOAD_ENABLED = True
   ```

3. Include the pagedown URLs in your project's URL configuration so the upload endpoint is available:

   ```python
   from django.urls import include, path

   urlpatterns = [
       # ...
       path('', include('pagedown.urls')),
       # ...
   ]
   ```

4. (Optional) Customize upload behavior with these settings:

   - `PAGEDOWN_IMAGE_UPLOAD_PATH`: relative path inside `MEDIA_ROOT` where uploads are stored (default: `pagedown-uploads`)
   - `PAGEDOWN_IMAGE_UPLOAD_EXTENSIONS`: list of allowed file extensions (default: `['jpg','jpeg','png','webp']`)
   - `PAGEDOWN_IMAGE_UPLOAD_UNIQUE`: boolean; if `True`, store uploads in uniquely named subfolders (default: `False`)

Customization
-------------

- **Templates**: The widgets use Django templates. You can override the widget templates by providing templates with the same names in your project's templates directory.
- **Static files**: Override or extend the CSS/JS by placing files under your project's static directories and updating the widget template if required.
- **Widget subclassing**: You can subclass `PagedownWidget` or `AdminPagedownWidget` to change the `template_name` or `media`.

Example custom widget:

```python
from pagedown.widgets import PagedownWidget


class MyWidget(PagedownWidget):
    template_name = 'myapp/widgets/custom_pagedown.html'
```

Development and testing
-----------------------

- To run the package tests (if tests are present in the project), use Django's test runner from the project root:

  ```bash
  python -m pytest  # or the test command used in your environment
  ```

- The project includes static assets under `pagedown/static/` and templates under `pagedown/templates/`.

This repo is made for educational and learning puropses taking inspiration using other open source projects.