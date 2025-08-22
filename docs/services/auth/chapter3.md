## 1. Working with models 

Its time to create the models, so what we shall need to do is to head to our models.py and start modeling auth model and take care of the different stuff in the django 

### What are models
First of all before we try wrapping our heads around how the models are going to be created, lets pause and ask what are they, what do they really help us achiveve.

In Django, models are Python classes that define the structure and behavior of data stored in a database. They serve as the single, definitive source of information about your data, essentially acting as an abstraction layer over the database.

Key aspects of Django Models:
Python Classes:
Models are defined as Python classes that subclass django.db.models.Model.

Database Mapping:
Each model typically maps to a single database table, and each attribute of the model represents a database field (column).

Object-Relational Mapper (ORM):
Django's ORM allows you to interact with your database using Python objects and methods instead of writing raw SQL queries. This simplifies database operations like creating, retrieving, updating, and deleting records. 

Field Types:
Models define the types of data stored in each field (e.g., CharField for text, IntegerField for numbers, DateTimeField for dates and times), along with optional constraints like maximum length, default values, or uniqueness.

Relationships:
Models can define relationships between different database tables (e.g., one-to-one, one-to-many, many-to-many) using fields like ForeignKey or ManyToManyField.

Automatic Features:
Django automatically provides features like:
An auto-incrementing primary key field (unless you specify one).
An automatically generated database-access API for querying and manipulating data.
Integration with the Django Admin interface for easy data management.

Migrations:
Django's migration system manages changes to your model definitions and translates them into database schema modifications, ensuring your database structure remains synchronized with your models.


The authentication that comes with Django is good enough for most common cases, but you may have needs not met by the out-of-the-box defaults. Customizing authentication in your projects requires understanding what points of the provided system are extensible or replaceable. This document provides details about how the auth system can be customized.

In this example, the Post model defines a database table for users with fields for username, first name, email and  last name. The __str__ method defines how a Auth object will be represented as a string, which is useful in the Django Admin and other contexts.

```python title="auth-service/app/models.py"
from django.db import models

class Auth(models.Model):
    username = models.CharField(max_length=200)
    first_name = models.TextField()
    last_name = models.DateTimeField(auto_now_add=True)
    email = models.TextField()

    def __str__(self):
        return self.username
```

So now that you have a glimpse of how a model is created, lets create  our auth model based on the concepts  that you have covered above. 

What we shall be creating will be a little more complex based on what you have seen in the example.  but lets not worry about the complexity as i will explain as we go on. 

We shall be  creating a custom user model in Django that uses email, username, and password for user registration. We will also modify the user model to remove certain fields that are not needed for our application.

First of all, we need to create a custom user manager that will handle user creation. This manager will ensure that users can be created with an email and username.

Lets create a file named `auth-service/app/models.py` like this

```python title="auth-service/app/models.py"
from django.db import models
from django.contrib.auth.models import BaseUserManager

class CustomUserManager(BaseUserManager):

    def create_user(self, email, username, password=None, **extra_fields):
        if not email:
            raise ValueError("The Email field must be set")
        if not username:
            raise ValueError("The Username field must be set")
        
        email = self.normalize_email(email)
        user = self.model(email=email, username=username, **extra_fields)
        user.set_password(password)
        user.save(using=self._db)
        return user

    def create_superuser(self, email, username, password=None, **extra_fields):
        extra_fields.setdefault('is_staff', True)
        extra_fields.setdefault('is_superuser', True)

        if extra_fields.get('is_staff') is not True:
            raise ValueError("Superuser must have is_staff=True.")
        if extra_fields.get('is_superuser') is not True:
            raise ValueError("Superuser must have is_superuser=True.")

        return self.create_user(email, username, password, **extra_fields)

```
Okay Seems to be pretty much but lets condence it so that you have an idea of whats going on in the custom user manager that we recently created.  

#### 1. The `create_user` method or function
This method creates a regular user. It checks if the email and username are provided, normalizes the email, and sets the password securely before saving the user.


We need the user to give us their email and username, if a user doesnt do this, we shouldnt let the user to proced. so we do some validation as shown bellow. same with email 

```python
 if not email:
    raise ValueError("The Email field must be set")
```

Assuming that the user enters an email like `example@gMail.com`, its first of ugly and we dont really want to store ugly stuff in our database, so we normalise it by lowering the domain portion of the email adress to make it something like `example@gmail.com`. this is done on this line `email = self.normalize_email(email)`


#### 1. create_superuser: 
This method creates a superuser with elevated permissions. It ensures that the superuser has the `is_staff` and `is_superuser` fields set to True.



<!-- 
Normalizes email addresses by lowercasing the domain portion of the email address.
Applies NFKC Unicode normalization to usernames so that visually identical characters with different Unicode code points are considered identical. -->

###  Creating the Custom User Model
Next, lets also create the custom user model that will use our custom user manager. Update the `auth-service/app/models.py` models.py file as follows:

```python title="auth-service/app/models.py"
from django.contrib.auth.models import AbstractUser

class User(AbstractUser):
    username = models.CharField(max_length=150, unique=True, blank=False, null=False)
    first_name = models.CharField(max_length=30, blank=True, null=True)
    last_name = models.CharField(max_length=30, blank=True, null=True)
    email = models.EmailField(unique=True, blank=False, null=False)
    is_active = models.BooleanField(default=True)
    api_token = models.CharField(max_length=255, blank=True, null=True)

    objects = CustomUserManager()

    REQUIRED_FIELDS = ['email']  # Ensure email is required for creates
    USERNAME_FIELD = 'username'  # Use username as the unique identifier

    def __str__(self):
        return self.username
    
    class Meta:
        verbose_name = 'account'
        verbose_name_plural = 'accounts'

```

To break this peiece of code down, lets remember why we created this. our table needs fields like the `username`, `first name`, `last name` and `email` to represent a user.

This are the fields that we shall be creating in our database my using the above table which will inhereit from the `Abstractuser` 

So here is a summerised information about each field that we defined earlier on in the User table.

`username`: A unique field for the user's username.
`first_name` and `last_name`: Optional fields for the user's first and last names.
`email`: A unique field for the user's email address.
`is_active`: A boolean field to indicate if the user account is active.
`api_token`: A field to store an API token for the user.
`objects`: This line assigns our custom user manager to the model.
`REQUIRED_FIELDS`: This specifies that the email field is required when creating a user.
`USERNAME_FIELD`: This indicates that the username will be used as the unique identifier for authentication.

Once we are done with this setup, we have introduced a new table `User` that has custom fields that is not shipped directly with django and we need to let the `settings.py` know about this. so we shall open our `settings.py` and add it 

```python title="auth-service/src/settings.py"
...
REST_FRAMEWORK = {
    # Use Django's standard `django.contrib.auth` permissions,
    # or allow read-only access for unauthenticated users.
    'DEFAULT_PERMISSION_CLASSES': [
        'rest_framework.permissions.DjangoModelPermissionsOrAnonReadOnly'
    ]
}

AUTH_USER_MODEL = 'app.User' # add it here 

ROOT_URLCONF = 'src.urls'
...
```

### Registering the User model.
For every model that we create, if we want to interact with it, we need to add it to the `admin.py` which is a central repository for managing different models with the django admin panel. To do this we start by importing the model and then use the register method as shown bellow.

```python title="auth-service/app/admin.py"
from django.contrib import admin
from app.models import User

admin.site.register(User)

```

### Making migrations 

Earlier on we saw that we have about 18 unapplied migrations. this is true because our database doesnt have our tables, even the ones that come shipped with the django. so in django migrations help us to manage our database tables in an effiecient way using the Object Relatioanl Mapper (ORM).

So we can now go ahead and make the migrations using the the following commands. For more, you can read the [Django Migrations Documentation](https://docs.djangoproject.com/en/stable/topics/migrations/). that has detailed information. but for now, we shall only focus on what matters and that is making and applying the migrations.

So we can now go ahead and make the migrations using the following commands:

```bash
(venv) python manage.py makemigrations
(venv) python manage.py migrate
```


### Putting It All Together: Models

At this point, we have successfully created a custom user model and a custom user manager to handle user creation and authentication. Here's a summary of what we've done:

1. **Created a `CustomUserManager`**:
    - This manager includes methods for creating regular users (`create_user`) and superusers (`create_superuser`).
    - It validates that both `email` and `username` are provided during user creation.
    - It normalizes email addresses and securely sets passwords before saving users.

2. **Created a `User` Model**:
    - This model extends Django's `AbstractUser` to customize the user fields.
    - It includes fields like `username`, `email`, `first_name`, `last_name`, `is_active`, and `api_token`.
    - It uses the `CustomUserManager` for user management.
    - It defines `USERNAME_FIELD` as `username` and includes `REQUIRED_FIELDS` to ensure `email` is mandatory.

3. **Integrated the Custom User Model**:
    - We updated the `AUTH_USER_MODEL` setting in `settings.py` to point to our custom user model (`app.User`).

4. **Registered the Model in the Admin Panel**:
    - We registered the `User` model in `admin.py` to manage it via the Django Admin interface.

5. **Applied Migrations**:
    - We used Django's migration system to create the necessary database schema for our custom user model.

### Complete Code for Models

Here is the final code for the models:

```python title="auth-service/app/models.py"
from django.db import models
from django.contrib.auth.models import BaseUserManager, AbstractUser

class CustomUserManager(BaseUserManager):
    def create_user(self, username, email, password=None, **extra_fields):
        if not email:
            raise ValueError("The Email field must be set")
        email = self.normalize_email(email)
        user = self.model(username=username, email=email, **extra_fields)
        user.set_password(password)
        user.save(using=self._db)
        return user

    def create_superuser(self, username, email, password=None, **extra_fields):
        extra_fields.setdefault('is_staff', True)
        extra_fields.setdefault('is_superuser', True)

        if extra_fields.get('is_staff') is not True:
            raise ValueError("Superuser must have is_staff=True.")
        if extra_fields.get('is_superuser') is not True:
            raise ValueError("Superuser must have is_superuser=True.")

        return self.create_user(username, email, password, **extra_fields)

class User(AbstractUser):
    username = models.CharField(max_length=150, unique=True, blank=False, null=False)
    first_name = models.CharField(max_length=30, blank=True, null=True)
    last_name = models.CharField(max_length=30, blank=True, null=True)
    email = models.EmailField(unique=True, blank=False, null=False)
    is_active = models.BooleanField(default=True)
    is_staff = models.BooleanField(default=False)
    is_superuser = models.BooleanField(default=False)
    date_joined = models.DateTimeField(auto_now_add=True)
    last_login = models.DateTimeField(blank=True, null=True)
    api_token = models.CharField(max_length=255, blank=True, null=True)

    objects = CustomUserManager()

    REQUIRED_FIELDS = []
    USERNAME_FIELD = 'username'

    def __str__(self):
        return self.username

    class Meta:
        verbose_name = 'account'
        verbose_name_plural = 'accounts'
```

With this setup, we now have a fully functional custom user model that can be used for authentication and user management in our Django application. This marks a significant milestone in building a robust and extensible authentication system.

## 2. Working with serializers. 

We have come from  far to this point that we totaly unsertand what is happening to our auth class and now its time to create provision to take care of the data, to be able to model it into JSOn format. 

This process is called serialization and to achieve this we shall use serilizers from the django rest framework to return an appropriate format for our data 

### What are serializers
Serializers allow complex data such as querysets and model instances to be converted to native Python datatypes that can then be easily rendered into JSON,

So now that we know about serializers, we need to go ahead and create the serializers file in our app directory `auth-service/app/serializers.py` 

```bash 
(venv) cd auth-service/app/
(venv) touch serializers.py
```

Serializers goes hand in hand with the models. we can not serialize something that we dont have. so we need to import the model that we recently created. We shall also need to use the django restframework to create the serializers. 

The process of creating the serializers is not that different from the proces off defining the models.
For this tutorial, we shall focus more on creating a simple serializer what uses the model (User) that we created earlier on.


Lets define a `UserSerializer` class that we shall use to serialize our data. To serialize our data, we shall use the `ModelSerializer` class that comes from the serializer module in the django rest framework.

```python title="auth-service/app/serializers.py"
from rest_framework import serializers
from app.models import User 

class UserSerializer(serializers.ModelSerializer):
    class Meta:
        model = User
        fields = ('id', 'username', 'password')
        extra_kwargs = {'password': {'write_only': True}}

```
And then a `Meta` class which we will use a model `User` we just imported, this will be also accampnied `fields` and `extra_kwargs`.
To break this down, In the fields, we specify the fields that we want to expose as JSONand then extra attributes such as making password `write_only` for security purposes.

This is basic, so lets spice this up by defining a `create` method that will take care of a couple of things.

One more thing to do, lets install `jwt` using pip

```bash
(venv) pip install jwt
```
This will install jwt that we shall use to encode our sensitive infromation while creating a user.

```python title="auth-service/app/serializers.py"
from rest_framework import serializers
from app.models import User 

import jwt
from django.contrib.auth.hashers import make_password
from django.conf import settings
class UserSerializer(serializers.ModelSerializer):
    class Meta:
        model = User
        fields = ('id', 'username', 'password')
        extra_kwargs = {'password': {'write_only': True}}

    def create(self, validated_data):
        user = User.objects.create(
            username=validated_data['username'],
            password=make_password(validated_data['password'])
        )
        user.api_token = jwt.encode(
            {'user_id': user.id}, 
            settings.SECRET_KEY, 
            algorithm='HS256'
        )
        user.save()
        return user
```

In the code above, we also import a couple of functions like `make_password`, `jwt`and  `settings` 
From the above code, we create a `user` using the `User` class with a validated username and password then use the newly created user and assign a token which is encoded using the `secret key`, `user_id` and `HS256` algorthm.
Later on we save the user in our database.

Lets also create a token serializer that will convert our token into a JSON compatible response that we need for our enpoint. For this we shall use the basic serializer from restframework serializer module.

```python title="auth-service/app/serializers.py"
class TokenSerializer(serializers.Serializer):
    token = serializers.CharField()
```


### Putting It All Together: Serializers

At this point, we have successfully created serializers to handle the conversion of our `User` model data into JSON format and vice versa. Here's a summary of what we've done:

1. **Created a `UserSerializer`**:
    - This serializer is based on the `User` model and exposes the `id`, `username`, and `password` fields.
    - The `password` field is marked as `write_only` to ensure it is not exposed in API responses.
    - A `create` method was implemented to handle user creation, including:
      - Hashing the password using `make_password`.
      - Generating a JWT token for the user using the `user_id` and the `SECRET_KEY` from the settings.
      - Saving the user with the generated token.

2. **Created a `TokenSerializer`**:
    - This simple serializer is used to represent the token as a JSON-compatible response.

3. **Installed `jwt`**:
    - The `jwt` library was installed to handle token encoding for secure user authentication.

### Complete Code for Serializers

Here is the final code for the serializers:

```python title="auth-service/app/serializers.py"
import jwt
from rest_framework import serializers
from app.models import User 

from django.contrib.auth.hashers import make_password
from django.conf import settings


class UserSerializer(serializers.ModelSerializer):
     class Meta:
          model = User
          fields = ('id', 'username', 'password')
          extra_kwargs = {'password': {'write_only': True}}

     def create(self, validated_data):
          user = User.objects.create(
                username=validated_data['username'],
                password=make_password(validated_data['password'])
          )
          user.api_token = jwt.encode(
                {'user_id': user.id}, 
                settings.SECRET_KEY, 
                algorithm='HS256'
          )
          user.save()
          return user

class TokenSerializer(serializers.Serializer):
     token = serializers.CharField()
```

With these serializers in place, we are now ready to integrate them into our views and endpoints to handle user registration and token-based authentication. This marks a significant step in building a robust authentication system for our application.

This is not that much what is expected from signing and encryption but for this tutorial. it fits the purpose.
