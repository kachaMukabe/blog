---
Author: Kacha Mukabe
Tags: Python, Django, Javascript, React
---

# What I learned in February

In my quest to catalog my developer journey here are a few things I have learned this month.

## React/Javascript

Local development of React applications with an express Api can have CORS errors. To solve this React has an option to serve the frontend on the same port as the backend. This is the proxy option that can be set in the React application's package.json file.

To do this open your react package.json file and add the following option:

```json
	"proxy": "http://localhost:<EXPRESS PORT>"
```

To read more on this read the React documentation [here](https://create-react-app.dev/docs/proxying-api-requests-in-development/).

## Python

Running a python module or a script has a subtle difference: -m. To run a python module add the "-m" tag.

So if you have a module like this:

```
hello/
	__init__.py
	__main__.py
	somescript.py
```

You can run it like this:

```bash
python -m <module-name>
python -m hello
```

As a note the module-name needs to be the name of the module not a string.

## Django

Using Sass with Django.

Install the following libraries

```bash
pip install lib sass django-compressor django-sass-processor
```

Add the Django sass processor to your installed apps in your Django settings. Whilst in the settings add the following static files settings.

```python
INSTALLED_APPS = [
	...,
	'sass_processor',
	...
]

STATICFILES_FINDERS = [
	'django.contrib.staticfiles.finders.AppDirectoriesFinder',
	'sass_processor.finders.CssFinder',
]

SASS_PROCESSOR_ROOT = os.path.join(BASE_DIR, 'static')
```

Pick your UI framework e.g. UIKit, Bootstrap, or Materialize. Copy the sass folder into your static files folder.

Create a main.scss file in the static files folder. This file is the primary way that the sass processor will interact with the sass framework.

In the base template add the following sass tags:

```html
<link href="{% sass_src 'main.scss' %}" rel="stylesheet" type="text/css">
```

### Django Deployment

Deploying an app to the Digital Ocean app platform with static files on digital ocean spaces. This will be a separate blog post.
