---
title: "Authentication & Authorization: OAuth2, JWT, handling sessions, role-based access control (RBAC)"
categories:
  - authentication
  - authorization
tags:
  - authentication
  - authorization
---
## Authentication vs. Authorization
Authentication and authorization are core concepts in application security that ensure only the right users can access certain data or functionality.

- **Authentication**: Verifies who the user is. It’s like showing your ID at a security checkpoint.
- **Authorization**: Determines what an authenticated user is allowed to do. Once past the checkpoint, it’s like being told which rooms you can enter.

## OAuth2
**OAuth2** is an open standard for access delegation. It allows third-party services to exchange limited access to resources on behalf of the user without exposing their credentials.

### Layman Explanation:
Imagine a hotel where you don’t want to give your house key to someone, but you want them to water your plants. You can issue a temporary, limited-access key for only that task.

### How OAuth2 Works:
- **Client**: The application that wants to access the user’s data.
- **Resource Owner**: The user who authorizes the app to access their data.
- **Authorization Server**: Verifies user identity and grants tokens.
- **Resource Server**: Hosts the user’s data and validates tokens.

### OAuth2 Flow:
1. **User clicks "Login with Google"**: Redirects to the OAuth provider (e.g., Google).
2. **User consents**: Approves access to specific data.
3. **Authorization Code**: The app receives this code and exchanges it for an access token.
4. **Access Token**: Used to make requests to access user data from the resource server.

### Code Example:
```python
from requests_oauthlib import OAuth2Session

# OAuth2 configuration
client_id = 'YOUR_CLIENT_ID'
client_secret = 'YOUR_CLIENT_SECRET'
authorization_base_url = 'https://example.com/auth'
token_url = 'https://example.com/token'

# Step 1: Redirect user to provider for authorization
oauth = OAuth2Session(client_id)
authorization_url, state = oauth.authorization_url(authorization_base_url)
print('Please go to {} and authorize access.'.format(authorization_url))

# Step 2: User is redirected back with authorization code
redirect_response = input('Paste the full redirect URL here: ')

# Step 3: Fetch the access token
token = oauth.fetch_token(token_url, authorization_response=redirect_response, client_secret=client_secret)
print('Access token:', token)

# Step 4: Access protected resources
response = oauth.get('https://example.com/api/user')
print(response.json())
```

## JWT (JSON Web Tokens)
**JWT** is a compact, URL-safe means of representing claims between two parties. It’s often used for secure data transmission and stateless authentication.

### Layman Explanation:
Think of a movie ticket that you show at different checkpoints. The ticket proves you’ve paid for the movie. JWTs work similarly to indicate that a user is authenticated.

### Structure of a JWT:
- **Header**: Contains the algorithm (e.g., HS256) and token type (JWT).
- **Payload**: Contains claims (e.g., user data or session info).
- **Signature**: Verifies the authenticity of the token using a secret key.

**Example JWT**: `eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyX2lkIjoxLCJleHAiOjE2MzU4MjQ0MDB9.sGn-XeUn9YX2Tf2iAYzGKu1vI4dsB4JpA8QVYtvpF_k`

### Code Example:
```python
import jwt
import datetime

# Secret key
SECRET_KEY = 'my_secret_key'

# Create JWT
payload = {
    'user_id': 1,
    'exp': datetime.datetime.utcnow() + datetime.timedelta(hours=1)
}
token = jwt.encode(payload, SECRET_KEY, algorithm='HS256')
print('Generated Token:', token)

# Decode JWT
decoded_payload = jwt.decode(token, SECRET_KEY, algorithms=['HS256'])
print('Decoded Payload:', decoded_payload)
```

## Handling Sessions
Sessions are server-side storage mechanisms used to track user state.

### How Sessions Work:
1. User logs in, and a session is created on the server.
2. The server generates a session ID and sends it as a cookie to the client.
3. On subsequent requests, the client sends the session ID, and the server retrieves the stored session data.

### Example using Flask:
```python
from flask import Flask, session, redirect, url_for, request

app = Flask(__name__)
app.secret_key = 'super_secret_key'

@app.route('/login', methods=['POST'])
def login():
    session['username'] = request.form['username']
    return redirect(url_for('welcome'))

@app.route('/welcome')
def welcome():
    if 'username' in session:
        return f'Hello, {session["username"]}!'
    else:
        return 'You are not logged in!'

@app.route('/logout')
def logout():
    session.pop('username', None)
    return 'You have logged out!'

if __name__ == '__main__':
    app.run()
```

## Role-Based Access Control (RBAC)
RBAC restricts system access to authorized users based on their role.

### Layman Explanation:
In a company, not everyone has the same permissions. For example, managers can approve expenses, but regular employees cannot.

### Example:
```python
def check_access(role):
    def decorator(func):
        def wrapper(*args, **kwargs):
            user_role = kwargs.get('user_role')
            if user_role == role:
                return func(*args, **kwargs)
            else:
                return 'Access Denied'
        return wrapper
    return decorator

@check_access('admin')
def delete_user(user_id, user_role):
    return f'User {user_id} deleted'

print(delete_user(123, user_role='admin'))  # Allowed
print(delete_user(123, user_role='user'))   # Denied
```

## Summary:
- **OAuth2**: Delegates limited access to user resources without sharing credentials.
- **JWT**: Provides stateless and secure token-based authentication.
- **Sessions**: Keep track of user state on the server.
- **RBAC**: Ensures only specific roles have permissions to perform certain actions.

These mechanisms ensure that your Python applications are secure, scalable, and maintain a clear access control strategy.