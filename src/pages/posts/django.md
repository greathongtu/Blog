---
layout: '../../layouts/MarkdownPost.astro'
title: 'Django 学习'
pubDate: 2023-03-09
description: '记录学习使用 Django 框架遇到的知识'
author: 'Aaron'
cover:
    url: '/preview/django.png'
    square: ''
    alt: 'cover'
tags: ["python", "Django"]
theme: 'light'
featured: false
---
```bash
# create virtual environment
python -m venv tutorial-env
.\Scripts\activate
python -m pip install --upgrade pip
pip3 install django

deactivate


# This creates a demoproject folder, inside which there’s another demoproject folder. The inner folder is a Python package. For a folder to be recognized by Python as a package, it must have a file __init__.py
django-admin startproject demoproject 
# The manage.py has the same role as the django-admin utility.
# If you have multiple projects, use django-adminand specify the settings. 

# use: python manage.py <command> 

# a Django project folder can contain one or more apps. An app is also represented by a folder of a specific file system.
python manage.py startapp <name of app>

# Then, add the 'demoapp' string inside the INSTALLED_APP list.

# Django manages the database operations with the ORM technique. Migration refers to generating a database table whose structure matches the data model declared in the app. 
python manage.py makemigrations
# synchronize
python manage.py migrate

# 127.0.0.1:8000
python manage.py runserver


# set database
DATABASES = {   
    'default': {   
        'ENGINE': 'django.db.backends.mysql',   
        'NAME': 'djangotest',   
        'USER': 'root',   
        'PASSWORD': 'password',   
        'HOST': '127.0.0.1',   
        'PORT': '3306',            
    }   
} 
```