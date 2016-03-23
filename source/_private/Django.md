# Django

1. 新建一个django-project

	`django-anmin startproject project-name`

2. 新建一个应用（app）

	`python manage.py startapp learn`

	把新定义的app加到settings.py中的INSTALL_APPS中。
	修改mysite/mysite/settings.py

	```python
	INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',

    'learn'
]
	```
3. 定义视图函数
