### Configuring URLs for the Views

To make the authentication views accessible, we need to configure the URLs for each endpoint. This involves mapping the views to specific URL patterns in the `urls.py` file of the `auth-service` app.

Here’s how you can set it up:

```python title="auth-service/app/urls.py"
from django.urls import path
from .views import RegisterView, LoginView, ValidateTokenView

urlpatterns = [
    path('register/', RegisterView.as_view(), name='register'),
    path('login/', LoginView.as_view(), name='login'),
    path('validate-token/', ValidateTokenView.as_view(), name='validate-token'),
]
```

### Explanation

1. **Register Endpoint**:
   - URL: `/register/`
   - View: `RegisterView`
   - Purpose: Allows users to create an account.

2. **Login Endpoint**:
   - URL: `/login/`
   - View: `LoginView`
   - Purpose: Authenticates users and provides a token.

3. **ValidateToken Endpoint**:
   - URL: `/validate-token/`
   - View: `ValidateTokenView`
   - Purpose: Validates the token provided by the user.

### Next Steps

With the URLs configured, you can now test the endpoints using tools like Postman or curl. Ensure that each endpoint behaves as expected and returns the appropriate responses. This completes the setup for the authentication service!
