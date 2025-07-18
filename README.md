# Webex Flask OAuth Integration Sample

A comprehensive Flask application demonstrating the complete OAuth 2.0 Authorization Code flow with Webex APIs. This sample showcases secure user authentication, token management, and API integration using a clean web interface.

## 🎯 Features

This sample demonstrates how to implement OAuth 2.0 with Webex APIs using Flask:

* **OAuth 2.0 Authorization Code Flow** - Standard three-legged OAuth implementation
* **Token Management** - Access token and refresh token handling
* **Automatic Token Refresh** - Seamless handling of expired tokens
* **Webex Rooms API** - Retrieve user's room/space listings
* **Session Management** - Secure token storage with Flask sessions
* **State Parameter Validation** - CSRF protection for OAuth flow
* **Clean Web Interface** - Professional HTML templates with styling

## 📚 Prerequisites

### Software Requirements

- **Python 3.6 or higher**
- **Flask** web framework
- **Requests** library for HTTP operations

### Webex Developer Account

- **[Webex Developer Account](https://developer.webex.com/)** - Sign up for free
- **Webex Integration** configured for OAuth flow

## 🚀 Quick Start

### Install Dependencies

Install the required Python modules:

```bash
pip install flask requests
```

### Create Webex Integration

1. **Navigate** to [Webex Developer Portal](https://developer.webex.com)
2. **Log in** with your Webex account
3. **Create Integration**:
   - Go to **My Apps** > **Create New App** > **Integration**
   - Fill in required details (name, description, icon)
   - **Redirect URI**: `http://0.0.0.0:10060/oauth` (for local development)
   - **Scopes**: Select `spark:all` for this sample (fine-tune for production)
4. **Save** your **Client ID** and **Client Secret**

### Setup and Configuration

1. **Update credentials** in `oauth.py` (lines 23-24):
   ```python
   clientID = "YOUR_CLIENT_ID_HERE"
   secretID = "YOUR_CLIENT_SECRET_HERE"
   ```

2. **Update authorization URL** in `templates/index.html`:
   ```html
   <a href='YOUR_OAUTH_AUTHORIZATION_URL_HERE'>
   ```

   **Authorization URL Format:**
   ```
   https://webexapis.com/v1/authorize?client_id=YOUR_CLIENT_ID&response_type=code&redirect_uri=http://0.0.0.0:10060/oauth&scope=spark:all&state=1234abcd
   ```

3. **Start the application**:
   ```bash
   python3 oauth.py
   ```

4. **Access the application**:
   ```
   Listening on http://0.0.0.0:10060...
   ```

5. **Test the OAuth flow**:
   - Open browser to `http://0.0.0.0:10060`
   - Click "GRANT" to authorize
   - View your Webex rooms/spaces

## 📁 Project Structure

```
webex-flask-oauth-example/
├── oauth.py                # Main Flask application
├── static/css/            # CSS styling files
├── templates/             # HTML templates
│   ├── index.html         # Landing page with authorization button
│   ├── granted.html       # Post-authorization page
│   ├── spaces.html        # Room/space listing display
│   └── temp.html          # Base template
├── LICENSE                # Cisco Sample Code License
└── README.md              # This file
```

## 🔧 Functionality Overview

### OAuth 2.0 Authorization Code Flow

| Step | Description | Implementation |
|------|-------------|----------------|
| **1. Authorization Request** | User clicks GRANT button | `index.html` with authorization URL |
| **2. User Authentication** | User logs into Webex and authorizes | External Webex authorization |
| **3. Authorization Code** | Webex redirects with auth code | `/oauth` endpoint receives code |
| **4. Token Exchange** | Exchange code for access tokens | `get_tokens()` function |
| **5. API Access** | Use tokens for authenticated requests | `spaces()` endpoint |

### Core Functions

#### Token Exchange
```python
def get_tokens(code):
    url = "https://webexapis.com/v1/access_token"
    payload = {
        "grant_type": "authorization_code",
        "client_id": clientID,
        "client_secret": secretID,
        "code": code,
        "redirect_uri": redirectURI
    }
    # Exchange authorization code for tokens
```

#### Token Refresh
```python
def get_tokens_refresh():
    url = "https://webexapis.com/v1/access_token"
    payload = {
        "grant_type": "refresh_token",
        "client_id": clientID,
        "client_secret": secretID,
        "refresh_token": session['refresh_token']
    }
    # Refresh expired access token
```

#### API Calls with Token Refresh
```python
def spaces():
    response = api_call()
    if response.status_code == 401:
        get_tokens_refresh()  # Auto-refresh on token expiration
        response = api_call()  # Retry with new token
    # Process rooms/spaces data
```

## 🔐 Authentication Flow

### Step-by-Step Process

1. **Landing Page**: User sees "GRANT INTEGRATION ACCESS" button
2. **Authorization**: Clicking GRANT redirects to Webex authorization server
3. **User Login**: User authenticates with Webex credentials
4. **Authorization**: User approves requested scopes
5. **Callback**: Webex redirects back with authorization code
6. **Token Exchange**: App exchanges code for access and refresh tokens
7. **API Access**: User can now access their Webex data

### Security Features

- **State Parameter**: CSRF protection with hardcoded state value
- **Secure Sessions**: Tokens stored in Flask session with random secret key
- **Automatic Refresh**: Expired tokens automatically refreshed
- **Scope Validation**: OAuth scopes properly configured

## 📡 API Integration

### Webex Rooms API

The application demonstrates API integration by fetching the user's rooms:

```python
def api_call():
    accessToken = session['oauth_token']
    url = "https://webexapis.com/v1/rooms"
    headers = {
        'accept': 'application/json',
        'Content-Type': 'application/json',
        'Authorization': f'Bearer {accessToken}'
    }
    response = requests.get(url=url, headers=headers)
    return response
```

### Error Handling

Comprehensive error handling with automatic token refresh:

```python
def spaces():
    response = api_call()
    # Check for unauthorized response
    if response.status_code == 401:
        get_tokens_refresh()    # Refresh tokens
        response = api_call()   # Retry API call
    
    # Process successful response
    rooms = response.json()['items']
    space_titles = [room['title'] for room in rooms]
    return render_template("spaces.html", spaces=space_titles)
```

## 🎨 User Interface

### Flask Templates

The application uses Jinja2 templates with inheritance:

- **`temp.html`**: Base template with common styling
- **`index.html`**: Authorization page with GRANT button
- **`granted.html`**: Success page with SPACES button
- **`spaces.html`**: List of user's rooms/spaces

### Template Structure

```html
<!-- index.html -->
{% extends "temp.html" %}
{% block content %}
<h1>GRANT INTEGRATION ACCESS</h1>
<div class='center'>
    <a href='OAUTH_URL_HERE'>
        <div class='button' style='width:512px;'>GRANT</div>
    </a>
</div>
{% endblock %}
```

### User Experience Flow

1. **Authorization Page**: Clean interface with prominent GRANT button
2. **Webex Login**: External Webex authentication
3. **Success Page**: Confirmation with SPACES button
4. **Data Display**: List of user's rooms/spaces

## 🔧 Configuration

### Required Configuration Variables

| Variable | Location | Description | Example |
|----------|----------|-------------|---------|
| `clientID` | `oauth.py` line 23 | Integration Client ID | `C1234567890abcdef...` |
| `secretID` | `oauth.py` line 24 | Integration Client Secret | `secret123...` |
| `redirectURI` | `oauth.py` line 25 | OAuth callback URL | `http://0.0.0.0:10060/oauth` |
| Authorization URL | `index.html` | Webex OAuth endpoint | See format below |

### Authorization URL Format

```
https://webexapis.com/v1/authorize?
client_id=YOUR_CLIENT_ID&
response_type=code&
redirect_uri=http://0.0.0.0:10060/oauth&
scope=spark:all&
state=1234abcd
```

### Flask Application Settings

```python
app = Flask(__name__)
app.secret_key = os.urandom(24)  # Secure session key
app.run("0.0.0.0", port=10060, debug=False)  # Server configuration
```

## 🌐 Production Considerations

### Security Best Practices

1. **Environment Variables**: Store credentials securely
   ```python
   import os
   clientID = os.getenv('WEBEX_CLIENT_ID')
   secretID = os.getenv('WEBEX_CLIENT_SECRET')
   ```

2. **HTTPS**: Use HTTPS in production
   ```python
   redirectURI = "https://yourdomain.com/oauth"
   ```

3. **Dynamic State**: Generate random state parameter
   ```python
   import secrets
   state = secrets.token_urlsafe(32)
   ```

4. **Secure Token Storage**: Use database instead of sessions
   ```python
   # Replace Flask sessions with secure database storage
   # Implement proper token encryption
   ```

### Production Deployment

```python
# Production configuration
if __name__ == '__main__':
    port = int(os.environ.get('PORT', 10060))
    app.run(host='0.0.0.0', port=port, debug=False)
```

### Error Handling Enhancement

```python
def get_tokens(code):
    try:
        response = requests.post(url=url, data=payload, headers=headers, timeout=30)
        response.raise_for_status()
        results = response.json()
        # Process tokens
    except requests.exceptions.RequestException as e:
        logger.error(f"Token exchange failed: {e}")
        # Handle error appropriately
```

## 🔧 Development

### Local Development Setup

1. **Virtual Environment**:
   ```bash
   python -m venv venv
   source venv/bin/activate  # On Windows: venv\Scripts\activate
   pip install flask requests
   ```

2. **Development Mode**:
   ```python
   app.run("0.0.0.0", port=10060, debug=True)
   ```

### Adding Features

Example: Adding user profile information:

```python
@app.route("/profile")
def profile():
    accessToken = session['oauth_token']
    url = "https://webexapis.com/v1/people/me"
    headers = {'Authorization': f'Bearer {accessToken}'}
    
    response = requests.get(url, headers=headers)
    if response.status_code == 401:
        get_tokens_refresh()
        response = requests.get(url, headers=headers)
    
    user_info = response.json()
    return render_template("profile.html", user=user_info)
```

### Testing the Flow

1. **Start application**: `python3 oauth.py`
2. **Open browser**: `http://0.0.0.0:10060`
3. **Click GRANT**: Initiates OAuth flow
4. **Authorize**: Log in and approve access
5. **View spaces**: See your Webex rooms/spaces

## 🔗 Related Resources

- [Webex Developer Portal](https://developer.webex.com/)
- [OAuth 2.0 Authorization Code Flow](https://tools.ietf.org/html/rfc6749#section-4.1)
- [Webex API Documentation](https://developer.webex.com/docs/api/v1/)
- [Flask Documentation](https://flask.palletsprojects.com/)
- [Integration Management](https://developer.webex.com/my-apps)

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test thoroughly with Webex integration
5. Submit a pull request

## 📄 License

This project is licensed under the **Cisco Sample Code License**.

### License Summary

- ✅ **Permitted**: Copy, modify, and redistribute for use with Cisco products
- ❌ **Prohibited**: Use independent of Cisco products or to compete with Cisco
- ℹ️ **Warranty**: Provided "as is" without warranty
- ℹ️ **Support**: Not supported by Cisco TAC

See the [LICENSE](LICENSE) file for full license terms.

## 🆘 Support

- Create an issue in this repository
- Visit [Webex Developer Support](https://developer.webex.com/support)
- Join the [Webex Developer Community](https://developer.webex.com/community)

---

**Ready to integrate OAuth 2.0 with Webex!** 🚀🔐
