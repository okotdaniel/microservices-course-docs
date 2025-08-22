
Now that we have our models configured up and running, its time to use these models and make sure we come up with the view for our application. 


Lets see how the user auth app would work logically. 
A user signs into the application by registering, the user is saved in our postgres database in a relational database table structure. 

This is where the postgres comes in but for now we shall use the SQLlite database that will store the data. 

The user then uses the credentials(username and password) to login to the sytem, once the user is loggedin, we asssign them a token that will be used to access the system resources. 

But we can not just use this token just like that, so we shall have to validate that token. 

So if you are following allong, you can guess that we need three endpoints for this auth service. 
This will be 
    Register endpoint that will fields from the models that we created earlier on
    Login endpoint that will use the username and password to access the system resouces 
    Validate toke endpoint. this will make sure the token is valid and trusted. 

## Registering the user 

Lets go on and create our first endpoint. The `Register` enpoint. This will inhreit from the APIView that we recently imported. 

```python title="auth-service/app/views.py"
import jwt
from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework import status
from app.serializers import UserSerializer,

Class RegisterView(APIVIew):

    serializer_class = UserSerializer()
    
    def post(self, request, *args, **kwargs):
        if request.method="POST":
            serializer = self.serializer_class(data=request.data)
            if serializer.is_valid:
                user = serializer.save()
                return Response({
                    'user_id': user.id,
                    'token': user.api_token
                }, status=status.HTTP_201_CREATED)
            return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)
```

This section explains how to create a Register endpoint for user registration in a Django REST Framework application. The RegisterView class inherits from APIView and uses a UserSerializer to validate and save user data.

The post method handles HTTP POST requests, checks if the request data is valid, and saves the user. If successful, it returns the user's ID and token with a 201 Created status. If validation fails, it returns the errors with a 400 Bad Request status. 

This endpoint is part of an authentication service that will later include login and token validation endpoints.

## Logging in the user

Lets also take care of the `Login` endpoint .

```python title="auth-service/app/views.py"
import jwt
from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework import status
from app.serializers import UserSerializer,
from app.models import Account
from app.serializers import UserSerializer, TokenSerializer
from django.conf import settings
from django.contrib.auth import authenticate

class LoginView(APIView):
    
    def post(self, request):
        username = request.data.get('username')
        password = request.data.get('password')
        user = authenticate(username=username, password=password)
        if user:
            token = jwt.encode(
                {'user_id': user.id}, 
                settings.SECRET_KEY, 
                algorithm='HS256'
            )
            return Response({'token': token})
        return Response({'error': 'Invalid credentials'}, status=status.HTTP_401_UNAUTHORIZED)

```
In the code above we define a LoginView class that handles user login requests in a Django REST Framework (DRF) application.

We extracts the username and password from the request, authenticate the user against the database, and, if successful, we generate a JWT token containing the user's ID.

The token is signed using a secret key and the HS256 algorithm. If authentication fails, we return an error response with a 401 status. This demonstrates a basic token-based authentication flow.



## Validating the token
Now that we have a user loggedin and they have got a toke, Its better we make sure that the above token is valid.

```python title="auth-service/app/views.py"

class ValidateTokenView(APIView):
    
    def post(self, request):
        token = request.META.get('HTTP_AUTHORIZATION', '').split(' ')[-1]
        if not token:
            return Response({'error': 'Token missing'}, status=status.HTTP_400_BAD_REQUEST)
        
        try:
            payload = jwt.decode(token, settings.SECRET_KEY, algorithms=['HS256'])
            user = Account.objects.get(id=payload['user_id'])
            return Response({'valid': True, 'user_id': user.id})
        except (jwt.ExpiredSignatureError, jwt.DecodeError, Account.DoesNotExist):
            return Response({'valid': False}, status=status.HTTP_401_UNAUTHORIZED)
```


In our code we  defined a ValidateTokenView class, which is responsible for verifying the validity of a JWT (JSON Web Token) provided in the Authorization header of an HTTP request. Here's the process in brief:

The token is retrieved from the Authorization header in the request's META object. If the token is missing, a 400 Bad Request response is returned with an error message.

The token is decoded using the jwt.decode method, with the secret key and hashing algorithm used during token creation. This ensures the token's integrity and authenticity.

The decoded token payload is used to fetch the user from the database. If the user exists and matches the token's user_id, a success response is returned with valid: True and the user_id.

If the token is expired, invalid, or the user does not exist, a 401 Unauthorized response is returned with valid: False. This ensures that unauthorized access is denied.

## Putting It All Together: Views

To bring everything together, we now have three endpoints that handle user authentication: `Register`, `Login`, and `ValidateToken`. These endpoints work in harmony to ensure that users can securely interact with the system.


1. **Register Endpoint**: This endpoint allows new users to create an account. It validates the input data, saves the user to the database, and returns a token for further interactions.

2. **Login Endpoint**: This endpoint authenticates existing users by verifying their credentials. Upon successful authentication, it generates and returns a token that the user can use to access protected resources.

3. **ValidateToken Endpoint**: This endpoint ensures that the token provided by the user is valid and corresponds to an existing user in the system. It protects the system from unauthorized access.

### Example Workflow

Here’s how the endpoints work together in a typical user authentication flow:

1. **User Registration**:
    - The user sends a `POST` request to the `Register` endpoint with their details.
    - The system validates the data, creates the user, and returns a token.

2. **User Login**:
    - The user sends a `POST` request to the `Login` endpoint with their username and password.
    - The system authenticates the user and returns a token if the credentials are valid.

3. **Token Validation**:
    - The user includes the token in the `Authorization` header when accessing protected resources.
    - The system sends the token to the `ValidateToken` endpoint to ensure it is valid before granting access.


## Complete Code for Views

Here is the final code for the views

```python title="auth-service/app/views.py"
import jwt
from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework import status
from app.models import Account
from app.serializers import UserSerializer, TokenSerializer
from django.conf import settings
from django.contrib.auth import authenticate


class RegisterView(APIView):

    serializer_class = UserSerializer()

    def post(self, request):
        serializer = self.serializer_class(data=request.data)
        if serializer.is_valid():
            user = serializer.save()
            return Response({
                'user_id': user.id,
                'token': user.api_token
            }, status=status.HTTP_201_CREATED)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

class LoginView(APIView):
    
    def post(self, request):
        username = request.data.get('username')
        password = request.data.get('password')
        user = authenticate(username=username, password=password)
        if user:
            token = jwt.encode(
                {'user_id': user.id}, 
                settings.SECRET_KEY, 
                algorithm='HS256'
            )
            return Response({'token': token})
        return Response({'error': 'Invalid credentials'}, status=status.HTTP_401_UNAUTHORIZED)

class ValidateTokenView(APIView):
    
    def post(self, request):
        token = request.META.get('HTTP_AUTHORIZATION', '').split(' ')[-1]
        if not token:
            return Response({'error': 'Token missing'}, status=status.HTTP_400_BAD_REQUEST)
        
        try:
            payload = jwt.decode(token, settings.SECRET_KEY, algorithms=['HS256'])
            user = Account.objects.get(id=payload['user_id'])
            return Response({'valid': True, 'user_id': user.id})
        except (jwt.ExpiredSignatureError, jwt.DecodeError, Account.DoesNotExist):
            return Response({'valid': False}, status=status.HTTP_401_UNAUTHORIZED)
        
```

### Next Steps

With these endpoints in place, you can now integrate them into our lets 

By following this structure, you’ve laid the foundation for a secure and scalable authentication service. Great work!