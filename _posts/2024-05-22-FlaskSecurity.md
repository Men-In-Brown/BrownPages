---
toc: True
comments: True
layout: post
title: Login & Security with Flask Security
description: CORs, Security and Deployment with Flask
type: tangibles
---

# Introduction of Login, Security, and CORs in Python Flask

In a full stack project, you are going to have constant communication between frontend and backend applications. This blog goes over key elements of this process and how to set up. 

## Table of Contents
A quick overview of each step of this blog, and how things are going to look:
1. Key Terms
2. A top down overview of steps
3. Looking in depth at frontend, backend, and deployment


## Key Terms

- URI - Uniform Resource Identifier. A set of characters that identifies a specific resource what may be on a website, such as an address, phone, etc.
- URL - Uniform Resource Locator. A set of characters that, rather than identifying any resource, identifies specific locations on the internet.
- CORS - Cross Origin Resource Sharing. Allows websites that aren't at the same origin to reference one another
- JWT - Java Web Token. Authentication token is encrypted and is a compact way of transmitting information across domains.
- Frontend/Backend - The frontend is the client side interface. It is where the user interacts with and achieves the purpose of your program. The backend is where all the magic happens, where data is stored, etc.

## Looking at the steps in a nutshell

1. Set up fetch and URI inside your frontend (using config.js)
2. Set up JS fetch and cookies (config.js)
6. Python and CORS (__init__.py)
   1. Python instance data (__init__.py)
7. Python Login and authentication
   1. Managing Secret Keys
   2. Security and Authentication
8. Nginx credentials through AWS
   1. Nginx and CORS through AWS
   2.  Certbot with Nginx and HTTP Methods


## Setting up and Fetching URI in Authentication

If we set up variables in set locations on the frontend,  we can ensure that we can use the right URI/URL location regardless of whatever hostname we use, and where we fetch to. Consider the following code;
```javascript
export var javaURI; // variable to store URI
if (location.hostname === "localhost") { // if localhost (mac)
        javaURI = "http://localhost:8085";
} else if (location.hostname === "127.0.0.1") { // if IP-based
        javaURI = "http://127.0.0.1:8085";
} else { // otherwise, deployed
        javaURI = "https://spring.nighthawkcodingsociety.com";
}

export const fetchOptions = {
    method: 'GET', // *GET, POST, PUT, DELETE, etc.
    mode: 'cors', // no-cors, *cors, same-origin
    cache: 'default', // *default, no-cache, reload, force-cache, only-if-cached
    credentials: 'include', // include, same-origin, omit
    headers: {
        'Content-Type': 'application/json',
        'X-Origin': 'client' // New custom header to identify so
    },
};
```

- **Method**: The REST Api method for databases. Allows to GET (Read) data, POST (Create) data, PUT (Update) data, and DELETE (Delete) data. There are a lot more, but these are the main four for CRUD operations.
- **Mode**: The mode of the request, basically a description of where the request is going to. If it requires `cors`, then it is a cross origin request. If `no-cors`, it allows you to make the request, but you do not have access to the response. If it is in the same origin, then it makes a request to the same origin
- **Cache**: Describes how the browser will store the data. 
- **Credentials**: Specifies whether to include cookies, authorization headers, etc. Allows for tokens in cookies to be stored.
- **Headers**: Details what headers are sent on the request. Currently, everything is sent in JSON (Javascript Object Notation) format. The `X-Origin` represents what origin the request is being sent from, that being the client.

### Breakdown
The first option is pretty simple, it is a simple if conditional:

- **IF the hostname is localhost -> set URI to localhost:8085**
- **OTHERWISE - IF the hostname is 127.0.0.1 -> set URI location to 1 127.0.0.1:8085**
- **OTHERWISE - We know its deployed, thus set the URI location to https://spring.nighthawkcodingsociety.com**

The second half makes a bit less sense. We create a variable called `fetchOptions`, which, as the name suggests, are the options we have when we make a call to an endpoint.

## Login code on the frontend:

Consider the Following Code:
```javascript
    // You would ge the options and request type from the parameters
        const requestOptions  = {
                ...fetchOptions, // This will copy all properties from options
                method: options.method, // Override the method property
                cache: options.cache, // Set the cache property
                body: JSON.stringify(options.body)
        };

        // Clear the message area
        document.getElementById(options.message).textContent = "";

        // Fetch JWT
        fetch(options.URL, requestOptions)
        .then(response => {
                // Trap error response from Web API
                if (!response.ok) {
                const errorMsg = 'Login error: ' + response.status;
                console.log(errorMsg);
                document.getElementById(options.message).textContent = errorMsg;
                return;
                }
                // Success!!!
                // Redirect to the Database location
                document.getElementById(options.message).textContent = "Success: " + document.getElementById("uid").value 
                options.callback();
        })
        .catch(error => {
                // Handle network errors
                console.log('Possible CORS or Service Down error: ' + error);
                document.getElementById(options.message).textContent = 'Possible CORS or service down error: ' + error;
        });
```

This is the login function which allows the authentication of a user. It makes the request to the proper authenticate


## Backend Code Access

In the backend, you will have to create your API endpoints for security, that will allow authentication to occur. Consider the following code below as the security endpoint in the backend. 

```python
# ... imports

# blueprint for user api endpoints
user_api = Blueprint('user_api', __name__,
                   url_prefix='/api/users')
api = Api(user_api)

# ... more resource code

# api resource class for security
class _Security(Resource):
    # method to authenticate user
    def post(self):
        try:
            body = request.get_json() # if no details are caught
            if not body:
                return {
                    "message": "Please provide user details",
                    "data": None,
                    "error": "Bad request"
                }, 400
                
            ''' Get Request Data '''
            uid = body.get('uid') # otherwise get data, validate garbage
            if uid is None:
                return {'message': f'User ID is missing'}, 401
            password = body.get('password')
            
            ''' Find User ID in Database '''
            user = User.query.filter_by(_uid=uid).first()
            if user is None or not user.is_password(password):
                return {'message': f"Invalid user id or password"}, 401
            if user: # if everything works
                try:
                    # Add an expiration date to the JWT token
                    token = jwt.encode(
                        {"_uid": user._uid},
                        current_app.config["SECRET_KEY"],
                        algorithm="HS256"
                    )
                    resp = Response("Authentication for %s successful" % (user._uid))
                    # Authenticate and create cookie
                    resp.set_cookie("jwt", token, 
                            max_age=3600,
                            secure=True,
                            httponly=True,
                            path='/',
                            samesite='None'  # This is the key part for cross-site requests
                            )
                    return resp
                except Exception as e:
                    return { # If error in creation
                        "error": "Something went wrong in cookie  creation!",
                        "message": "Failed to generate JWT token: " + str(e)
                    }, 500
            return { # if other errors
                "message": "Error fetching auth token!",
                "data": None,
                "error": "Unauthorized"
            }, 404
        except Exception as e: # If other errors
            return {
                    "message": "Something went wrong in data processing!",
                    "error": str(e),
                    "data": None
            }, 500

            
    # building a RESTapi endpoint
    api.add_resource(_Security, '/authenticate')
```

If the `try ... except exception as e` blocks are throwing you off, these are just there to ensure any weird errors we catch don't cause our code to break.

In a nutshell, what this code does is that it takes and validates if user data is present. If so, it creates a JWT token that uses those details, and sends that as a response from the endpoint. This is how the JWT token is created and sent to your browser.


## Requiring Tokens on Endpoints

Now that we can authenticate, we obviously need to ensure that this authentication can be used somewhere. This is an example API that requires the token to access

```python
# Example User API
class UserAPI:        
    class _CRUD(Resource):
        # ... more resource code omitted
        # The @token_required decorator ensures that the user is authenticated before they can access the data.
        @token_required()
        # The get method retrieves all users from the database.
        # The underscore (_) indicates that the current_user is not used in this method.
        def get(self, _): 
            users = User.query.all()    # Query all users from the database
            json_ready = [user.read() for user in users]  # Prepare the data for JSON output
            return jsonify(json_ready)  # Convert the data to a JSON response

    # Add the _CRUD resource to the API at the root endpoint ('/')
    api.add_resource(_CRUD, '/')
```

Notice the `@token_required` decorator, which basically adds some add on the code to authenticate the token

```python
# from auth_middlewares.py file
# ... more import code
# The token_required function is a decorator that checks if the user is authenticated.
# If roles are provided, it also checks if the user has the required role.
def token_required(roles=None):
    def decorator(f):
        @wraps(f)
        def decorated(*args, **kwargs):
            token = request.cookies.get("jwt")  # Get the JWT token from the cookies
            # If the token is missing, return an error message
            if not token:
                return {
                    "message": "Authentication Token is missing!",
                    "data": None,
                    "error": "Unauthorized"
                }, 401
            try:
                # Decode the token and get the user's data
                data = jwt.decode(token, current_app.config["SECRET_KEY"], algorithms=["HS256"])
                current_user = User.query.filter_by(_uid=data["_uid"]).first()
                # If the user doesn't exist or doesn't have the required role, return an error message
                if current_user is None:
                    return {
                        "message": "Invalid Authentication token!",
                        "data": None,
                        "error": "Unauthorized"
                    }, 401

                # If the doesn't have the required role, return an error message
                if roles and current_user.role not in roles:
                    return {
                        "message": "Insufficient permissions. Required roles: {}".format(roles),
                        "data": None,
                        "error": "Forbidden"
                    }, 403

            # If there's an error (likely with the token), return an error message
            except Exception as e:
                return {
                    "message": "Something went wrong, likely with the token!",
                    "data": None,
                    "error": str(e)
                }, 500

            # If the user is authenticated and has the required role (if applicable), call the decorated function
            return f(current_user, *args, **kwargs)

        return decorated

    return decorator
```

This then takes the the token and then sees if it is valid. If it sin't valid, it will throw and error, otherwise, it will validate and allow to continue. These limit those who can access certain endpoints to those who can log in.


## Flask Configuration (__init__.py)

Your flask project also needs to have some configuration you know about to ensure CORs, etc. works.

**Important note: This does not disregard your dockerfile/docker-compose file, but those are out of the current scope**

`__init__.py` is a special file that is used to initialize stuff for a specific package. In flask, this creates keys that prevent from Cross Site Request Forgery (CRSF) attacks, a type of attack that tries to forge a serious request, causing data leaks. It also defines what origins it supports. These are important to check if you have CORs errors. 

Consider a typical sample:
```python
from flask import Flask
from flask_cors import CORS
from flask_sqlalchemy import SQLAlchemy
from flask_migrate import Migrate
import os

"""
These object can be used throughout project.
1.) Objects from this file can be included in many blueprints
2.) Isolating these object definitions avoids duplication and circular dependencies
"""

# Setup of key Flask object (app)
app = Flask(__name__)
# Allowed servers for cross-origin resource sharing (CORS)
cors = CORS(app, supports_credentials=True, origins=['http://localhost:4100', 'http://127.0.0.1:4100', 'http://127.0.0.1:8086', 'https://nighthawkcoders.github.io'])

# Secret key for session handling and CSRF protection
SECRET_KEY = os.environ.get('SECRET_KEY') or 'SECRET_KEY'
app.config['SECRET_KEY'] = SECRET_KEY

# Setup SQLAlchemy object and properties for the database (db)
# Local SQLite database within the instance folder
dbURI = 'sqlite:///volumes/sqlite.db'
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False
app.config['SQLALCHEMY_DATABASE_URI'] = dbURI
db = SQLAlchemy()
Migrate(app, db)

# Images storage settings and location
app.config['MAX_CONTENT_LENGTH'] = 5 * 1024 * 1024  # maximum size of uploaded content
app.config['UPLOAD_EXTENSIONS'] = ['.jpg', '.png', '.gif']  # supported file types
app.config['UPLOAD_FOLDER'] = os.path.join(app.instance_path, 'uploads')
os.makedirs(app.config['UPLOAD_FOLDER'], exist_ok=True)
```

This file, as you can see, sets up the origins where you can access the endpoints from (in this case, it is a list including https://localhost:4100, etc.). It also tells you where the DB is (mentioned by dbURI), that being in the `/volumes/` folder. It also configures the file extensions or content link, etc. It is very self explanatory


## In AWS

Once we get to AWS after running through previous deployment processes, it is necessary to create a file that can deal with caching, proxying, and stability. We do this through nginx. 

Nginx, which you have already used to start this deployment, is going to be used to configure things related to CORS.

### Nginx Configuration File

When operating with CORS on the backend, you need to create specific blocking systems for each domain and different types of requests. 

Example Nginx
```
server {
    listen 80;
    listen [::]:80;
    server_name spring.nighthawkcodingsociety.com;
    location / {
        proxy_pass http://localhost:8086;
        # Preflighted requests
        if ($request_method = OPTIONS) {
            add_header "Access-Control-Allow-Credentials" "true" always;
            add_header "Access-Control-Allow-Origin"  "https://nighthawkcoders.github.io" always;
            add_header "Access-Control-Allow-Methods" "GET, POST, PUT, OPTIONS, HEAD" always;
            add_header "Access-Control-Allow-MaxAge" 600 always;
            add_header "Access-Control-Allow-Headers" "Authorization, Origin, X-Origin, X-Requested-With, Content-Type, Accept" always;
            return 204;
        }
    }
}
```

This above nginx code configures the **proxy pass**, or the port where all your code is actually hosted, which is then mapped onto the DNS you created. The biggest thing to understand is the add headers. They are

- **Authorization**: This header is used to include credentials with a request. It commonly contains tokens like OAuth or JWT tokens that authenticate the request. The server uses this header to verify that the client has the right to access the requested resource.
Origin
- **Origin**: This header indicates the origin (scheme, host, and port) of the request. It is used primarily for Cross-Origin Resource Sharing (CORS) to determine if the request is coming from an allowed origin. This helps prevent unauthorized domains from accessing resources.
X-Origin
- **X-Origin**: This is a custom header typically used to indicate the original source of the request. It can be used for logging, analytics, or enforcing certain policies based on the request's origin. Since it's a non-standard header, its use is defined by the server's specific needs.
X-Requested-With
- **X-Requested-With**: This header is often used by JavaScript frameworks to identify AJAX requests. Commonly set to XMLHttpRequest, it helps servers distinguish between regular HTTP requests and AJAX requests, enabling different processing paths if needed.
Content-Type
- **Content-Type**: This header specifies the media type of the resource or the data being sent to the server. For example, application/json indicates that the request body contains JSON data. It helps the server understand how to parse and process the incoming data.
Accept
- **Accept**: This header tells the server what content types the client is willing to receive in response. For example, Accept: application/json indicates that the client expects JSON data in the response. The server can then tailor its response based on the client's specified preferences.

## Nginx + Certbot configuration

Nginx also has Certbot, a way to ensure HTTPS certification. The different between HTTP and HTTPS is that HTTPS is secure. This means HTTPS sends all information encrypted, and thus is safer... and isn't blocked by the school. The command to do this is simple: `sudo certbot --nginx`. This will add certbot to our nginx conversions. Then you'll have to set up certification, which is as follows:

Here's how to explain it as a diagram

```
Browser                    Server
   |                          |
   |--- ClientHello --------> |
   |                          |
   |<--- ServerHello -------- |
   |                          |
   |<--- Certificate -------- |
   |                          |
   |--- Key Exchange -------->|
   |                          |
   |--- Finished ------------>|
   |                          |
   |<--- Finished ----------- |
   |                          |
   |--- Encrypted Data ------>|
   |<--- Encrypted Data ----- |
```

1. Request and Issuance: When a website owner wants to enable HTTPS, they generate a Certificate Signing Request (CSR) containing the website's public key and some information about the site and organization. This CSR is sent to a Certificate Authority (CA), which verifies the applicant's identity and domain ownership. Upon verification, the CA issues an SSL certificate containing the public key and the verified information, digitally signed by the CA.

2. Installation: The website owner installs the SSL certificate on their web server. The server is configured to use the certificate for HTTPS connections.

3. Handshake Process: When a user visits an HTTPS-enabled website, their browser initiates a secure connection with the server. This process, called the SSL/TLS handshake, involves several steps:

4. Hello Messages: The browser sends a "ClientHello" message with its SSL/TLS capabilities, and the server responds with a "ServerHello" message, choosing the encryption method supported by both.
   1. Certificate Exchange: The server sends its SSL certificate to the browser. The browser verifies the certificate against a list of trusted CAs.
   2. Key Exchange: The browser and server agree on a session key using asymmetric encryption (e.g., RSA) or Diffie-Hellman key exchange.
   3. Secure Connection: Once the session key is established, both parties use symmetric encryption to encrypt and decrypt data transmitted during the session.
   4. Data Encryption: The established session key is used to encrypt the data exchanged between the browser and the server, ensuring confidentiality and integrity.


And now you know how Flask and CORS work!