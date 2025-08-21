## 1. Introduction
In the previous chapter, we were able to set up our django project, run the applicationn using the django-admin command. now in this chapter, we shall go deeper and make configuration and install the necessary tools we need to run the auth service. 

First of all. we shall need to create endpoints, we shall leverage the django restframework that will help us achieve this. to do this lets install django rest framework package and we proced. 

## 2. Django Rest framework 
Django REST Framework (DRF) is a powerful and flexible toolkit for building Web APIs in conjunction with the Django web framework. It simplifies the creation of RESTful APIs by providing a set of tools and conventions that streamline common API development tasks.

### Features
Key features and concepts of Django REST Framework include:
#### 1. Serialization:
DRF provides serializers to convert complex data types, such as Django models and querysets, into native Python datatypes that can then be easily rendered into JSON, XML, or other content types.
#### 2. Viewsets:
Viewsets abstract the logic for handling common API operations (like list, create, retrieve, update, destroy) into a single class, reducing code repetition.
#### 3. Routers:
Routers automatically handle URL routing for viewsets, simplifying the process of mapping API endpoints to their corresponding views.
#### 4. Authentication and Permissions:
DRF offers various authentication schemes (e.g., TokenAuthentication, SessionAuthentication, OAuth) and permission classes to control access to API resources.

This are some of the common ones, but for the purpose of this tutorial we shall on focus on what we need and what truly matters for our application.

In essence, Django rest framework extends Django's capabilities to facilitate the creation of robust, maintainable, and secure RESTful APIs, making it a popular choice for developers building backend services that interact with frontend applications, mobile clients, or other systems and that is why we have chosen to go with it for our auth service.

## 2. Installing django rest framework in our project.

To install DRF in our proejct, lets go and install this using pip. the documentation is pretty comprehensive and easy to read. You can follow using the DRF docs here . [Official Django REST Framework Documentation](https://www.django-rest-framework.org/)

On our project directory, lets install django rest framework.
``` python
(venv) pip install rest_framework
```
Now if we run ``` pip freeze ```, we can see that django restframework has been installed on our environment.

## Adding the restframework into the installed apps 
So once we are done installing rest framework, lets go on add it to the installed apps so that our project can recorgnise it as a dependency since this is not shipped directl with django. 
```bash
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'rest_framework', # add it here
]
```
Lets also add the permission default permission classes into our `settings.py` file, we shall talk more baout permissions later. 

```bash
REST_FRAMEWORK = {
    # Use Django's standard `django.contrib.auth` permissions,
    # or allow read-only access for unauthenticated users.
    'DEFAULT_PERMISSION_CLASSES': [
        'rest_framework.permissions.DjangoModelPermissionsOrAnonReadOnly'
    ]
}
```
## 3. Project configuration (Putting it all together)

For every app that we create, we need to make sure that they are recorgnised by our  ```settings.py``` file in the `src` directory. If this doesnt happen then we are likely to face issues. 

So lets go in the ```settings.py``` file and add our app 
This is done by adding tha pp in the installed apps as shown bellow

```bash
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'rest_framework', 
    'app', # add it here
]
```

Now we have our app and restframework registered in the settings.py. next we have to connect our app to the main project route file. to do this we shall open the urls.py in the root directory in the `src` folder

we shall need to make an import from the urls modules. from the coed bellow, we can see that the path is already included, so all we need to do is to add the `include` at rhe end and we are good to go 

To add the urls in the app config, we need to use the path function and include the app as shown in the code bellow 

```bash 
"""
URL configuration for src project.

The `urlpatterns` list routes URLs to views. For more information please see:
    https://docs.djangoproject.com/en/5.2/topics/http/urls/
Examples:
Function views
    1. Add an import:  from my_app import views
    2. Add a URL to urlpatterns:  path('', views.home, name='home')
Class-based views
    1. Add an import:  from other_app.views import Home
    2. Add a URL to urlpatterns:  path('', Home.as_view(), name='home')
Including another URLconf
    1. Import the include() function: from django.urls import include, path
    2. Add a URL to urlpatterns:  path('blog/', include('blog.urls'))
"""
from django.contrib import admin
from django.urls import path, inclue # import include too

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('app.urls')),

]
```
At this point, you might be tempted to run the app, but i can assure you, you will get an error. why are we likely to get an error ? well the answer lies in the fact that we dont have eerything confiured as expected yet. if thats the case, then lets go on and make sure that we make things right. 

If you run 
```bash 
(venv) python manage.py runserver
```
You will get something like this.

```bash 
  File "<frozen importlib._bootstrap>", line 1387, in _gcd_import
  File "<frozen importlib._bootstrap>", line 1360, in _find_and_load
  File "<frozen importlib._bootstrap>", line 1324, in _find_and_load_unlocked
ModuleNotFoundError: No module named 'app.urls'
```

Now this brings us to an imporntant aspect in programming, errors. well the truth is that everytime you write code, you are likely to be faced with errors and this is absolutely normal.

So for the error that we have, its absolutely clear that there is a modeule that is not being found when we try to run the server and that module is the app module. 

In django, if we register the app to the url configuration, we also need to make sure that this is taken care of properly from where it is coming from. 

For our case, this is coming from the `urls.py` file that we defined earlier on in the urls conf in the app module. 
So lets fix that. 

So if we open our `urls.py` in the app folder, we can see that we its an empty folder so lets do somehing ther. 

We are going to define a couple of urls that will point to the different endpoints that we shall be creating in a moment 

