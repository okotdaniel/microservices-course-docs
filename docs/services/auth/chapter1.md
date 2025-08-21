## 1. Introduction

So we have managed to create a directory, created a dependancy file to hold our priject dependancies, installed django and verified the installation in the previous chapter. we are now good to go.

## 2. Creating the django project.

To create the django project, all we need to do is to navigate to the ``` auth-service ``` directory and start run the following command. 

``` bash
django-admin startproject src .
```

running this command will create a django project named src in the current directory becuase we used the dot at the end of the command.

so if we take a look at our project structure, it showuld be something like this .
```bash 
auth-service/
├── manage.py
├── src/
│   ├── __init__.py
│   ├── settings.py
│   ├── urls.py
│   ├── wsgi.py
│   └── asgi.py
├── venv/
└── requirements.txt

```

To make sure that our project was created successfully, we can go on and verify this by running the application server using the manage.py file that we recently saw in the project we creatred. 

Django makes its easy for us by using the manage.py script that has several commands that we can use for different commands and purpses. but for the case of this section, we shall cover the runserver command. 

To do this, lets head to our root project directory and run the command 

```bash 
cd Documents/Projects/auth-service
```
Now that we are in the project directory, we can run the 
```bash 
(venv) python manage.py runserver
```

We can now go ahead and create an app that will be basically holding our app specific files and configurations for the auth service. 

At this point in time, we should be able to see something like this on our console. if you didnt see something like this, then there should be something wrong. 

```bash 
Watching for file changes with StatReloader
Performing system checks...

System check identified no issues (0 silenced).

You have 18 unapplied migration(s). Your project may not work properly until you apply the migrations for app(s): admin, auth, contenttypes, sessions.
Run 'python manage.py migrate' to apply them.
August 19, 2025 - 13:23:31
Django version 5.2.5, using settings 'src.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.

WARNING: This is a development server. Do not use it in a production setting. Use a production WSGI or ASGI server instead.
For more information on production servers see: https://docs.djangoproject.com/en/5.2/howto/deployment/
```

## 3.Running the application on the browser
We can now go on to the browser and navigate to the link provided on the console to be able to see our django application. 

We should be able to see something like this on the browser. 

![Django Welcome Page](../img/django_welcome_page.png)

If you are careful enough, you might have noticed something about migrations on the console highlighted in red. This is absolutely what it says, so how do we fix that. 
First lets understand migrations in django. 

### 4. Creating the auth app
So far everything sounds to be okay but we have one more task to do. that is to create the service app that will hold our files such as serializers, models, url configs etc. 

TO do this lets head to our project directory and create the app using the ``` django-admin `` command. 

``` python 
(venv) django-admin startapp app
```
Running this command will create a app in the root directory and if we take a close look at our file structure, we can tell that diffrent files have beeen created using the command that we just previously run 

Lets naviage to this app directory and make sure we create a urls.py file which shall hold all our endpoints fror the authentication

``` bash 
cd Documents/Projects/auth-service/app
touch urls.py
```
Now our updated project structure will look like this. 

``` bash 
Project
│
│    auth-service/
│    │
│    ├── manage.py
│    ├── src/
│    │   ├── __init__.py
│    │   ├── settings.py
│    │   ├── urls.py
│    │   ├── wsgi.py
│    │   └── asgi.py
│    │
│    ├── app/
│    │   ├── migrations/
│    │   │   └── __init__.py
│    │   ├── __init__.py
│    │   ├── admin.py
│    │   ├── urls.py
│    │   ├── apps.py
│    │   ├── models.py
│    │   ├── tests.py
│    │   └── views.py
│    │
│    ├── venv/
│    │
│    └───requirements.txt
│    
```

### What are migrations.
Django migrations are a system for propagating changes made to your models (adding a field, deleting a model, etc.) into your database schema. They are designed to be largely automatic, simplifying the process of managing database schema evolution.

Well, for now, lets not worry about the migrations, we shall take care of that later on in the tutorial.

So we have managed to create a directory, created a dependancy file to hold our priject dependancies, installed django and verified the installation in the previous chapter. we are now good to go.
## Summary

In this section, we created a Django project named `src` and explored its structure, including the `manage.py` script and core files like `settings.py` and `urls.py`. We also learned how to run the development server to verify the project setup and navigate to the application in the browser. Additionally, we created an app named `app` to hold specific files and configurations for the authentication service, updating the project structure accordingly. Finally, we briefly introduced Django migrations and their purpose, which we will address in detail later.

## Conclusion

By following the steps outlined in this section, we successfully set up a Django project, ran the development server, and created an app for the authentication service. These foundational steps are crucial for building and organizing a Django application. In the next sections, we will dive deeper into migrations, app configurations, and other essential aspects of Django development. 



