---
title: Handling OAuth2 Flow and JWT Authentication in a React + Flask Application
date: 2024-10-27 22:41:04
excerpt: implementing Google OAuth2 authentication in a React frontend with a Flask backend.
tags: [Debug, OAuth2, JWT, Google Authentication, Tutorial, Web Development]
categories:
- Daily Logs
- Debug Logs
---

## Introduction
In this blog post, I'll share my experience implementing Google OAuth2 authentication in a React frontend with a Flask backend. I encountered several challenges along the way, particularly with maintaining user sessions and handling redirects properly.

## Initial Problem: Missing Refresh Token
### The Issue
My first error occurred when trying to validate user tokens:

```python
ValueError: Authorized user info was not in the expected format, missing fields refresh_token.
```

This happened in the following code:

```python
def valid_token(user_token_path):
    if not os.path.exists(user_token_path):
        return 0, None
    else:
        creds = Credentials.from_authorized_user_file(user_token_path, SCOPES)
        # Error occurred here
```

### The Solution
The problem was that web applications need to explicitly request offline access to get a refresh token. I modified the authorization flow:

```python
def authorize():
    flow = google_auth_oauthlib.flow.Flow.from_client_secrets_file(
        CLIENT_SECRETS_FILE, scopes=SCOPES)
    flow.redirect_uri = url_for('oauth2callback', _external=True)
    authorization_url, state = flow.authorization_url(
        access_type='offline',  # Added this
        prompt='consent',       # Added this
        include_granted_scopes='true'
    )
    return authorization_url, state
```

## Second Problem: OAuth Redirect Issues
### The Issue
After successful OAuth authentication, instead of seeing my schedule page, I got a JSON response displayed on screen:

```json
{
    "message": "test",
    "state": "state",
    "auth_url": "authorization_url"
}
```

### The Solution
I modified the OAuth callback to properly redirect to the frontend:

```python
@app.route('/oauth2callback')
def oauth2callback():
    # ... OAuth flow code ...
    credentials = flow.credentials
    
    # Save credentials
    with open(user_token_path, "w") as token:
        token.write(credentials.to_json())
    
    # Redirect to frontend
    return redirect("http://localhost:3001")
```

## Third Problem: Lost Authentication State
### The Issue
After implementing the redirect, a new problem emerged: users were being shown the login page again after the OAuth redirect. This happened because the React state was being reset on page reload.

### The Initial Frontend Code
```typescript
export default function LoginPage() {
  const [isLoggedIn, setIsLoggedIn] = useState(false);
  
  const handleSubmit = async (e) => {
    // ... login logic ...
    setIsLoggedIn(true);
  }
}
```

### The Complete Solution
I implemented a JWT-based solution to maintain authentication state:

1. **Backend Changes**:
```python
@app.route('/oauth2callback')
def oauth2callback():
    # ... OAuth flow code ...
    
    # Create JWT token
    access_token = create_access_token(identity=email)
    
    # Redirect with token
    return redirect(f"http://localhost:3001?token={access_token}")
```

2. **Frontend Changes**:
```typescript
export default function LoginPage() {
  const [isLoggedIn, setIsLoggedIn] = useState(false);

  useEffect(() => {
    // Check for token in URL
    const queryParams = new URLSearchParams(window.location.search);
    const tokenFromUrl = queryParams.get('token');
    
    if (tokenFromUrl) {
      localStorage.setItem('token', tokenFromUrl);
      setIsLoggedIn(true);
      // Clean URL
      window.history.replaceState({}, document.title, window.location.pathname);
    } else {
      // Check existing token
      const existingToken = localStorage.getItem('token');
      if (existingToken) {
        setIsLoggedIn(true);
      }
    }
  }, []);

  // ... rest of component
}
```

## Key Learnings
1. Web applications need explicit offline access for refresh tokens
2. OAuth redirects need careful handling in SPAs
3. Use JWT tokens and localStorage to maintain authentication state
4. Always clean up URL parameters after processing
5. Consider token verification on page load

## Conclusion
This implementation now successfully:
- Handles Google OAuth2 authentication
- Maintains user sessions across page reloads
- Properly manages the authentication flow between frontend and backend
- Provides a smooth user experience without unnecessary logins

The final solution combines OAuth2 for secure Google API access with JWT for maintaining local authentication state, creating a robust authentication system for web applications.

Remember to always implement proper security measures and token validation in production environments!
