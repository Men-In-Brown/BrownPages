---
toc: True
comments: True
layout: post
title: Login & Security with Spring Security
description: CORs, Security and Deployment with Spring
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
3. Java and CORS (__init__.py)
   1. Python instance data (__init__.py)
4. Java Login and authentication
   1. Managing Secret Keys
   2. Security and Authentication
5. Nginx credentials through AWS
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

```java
@PostMapping("/authenticate")
	public ResponseEntity<?> createAuthenticationToken(@RequestBody Person authenticationRequest) throws Exception {
		authenticate(authenticationRequest.getEmail(), authenticationRequest.getPassword());
		final UserDetails userDetails = personDetailsService
				.loadUserByUsername(authenticationRequest.getEmail());

		// Get the roles of the user
		List<String> roles = userDetails.getAuthorities().stream()
			.map(GrantedAuthority::getAuthority)
			.collect(Collectors.toList());

		// Generate the token with the roles
		final String token = jwtTokenUtil.generateToken(userDetails, roles);

		if (token == null) {
			return new ResponseEntity<>("Token generation failed", HttpStatus.INTERNAL_SERVER_ERROR);
		}

		final ResponseCookie tokenCookie = ResponseCookie.from("jwt_java_spring", token)
			.httpOnly(true)
			.secure(true)
			.path("/")
			.maxAge(3600)
			.sameSite("None; Secure")
			.build();

		return ResponseEntity.ok().header(HttpHeaders.SET_COOKIE, tokenCookie.toString()).body(authenticationRequest.getEmail() + " was authenticated successfully");
	}

	private void authenticate(String username, String password) throws Exception {
		try {
			authenticationManager.authenticate(new UsernamePasswordAuthenticationToken(username, password));
		} catch (DisabledException e) {
			throw new Exception("USER_DISABLED", e);
		} catch (BadCredentialsException e) {
			throw new Exception("INVALID_CREDENTIALS", e);
		} catch (Exception e) {
			throw new Exception(e);
		}
	}
```

There are two points to this, the `/authenticate` endpoint and `authenticate` function. The authenticate endpoint is where the command to login is received. Since that endpoint takes a person, it will then authenticate that person. It will run an external authenticate command, based in the authentication manager object. If it throws any self-explanatory exception, it will ensure that the user knows about that. Otherwise, it continues. After authenticating each person, it then proceeds to check what roles the person has. After checking roles, it creates and returns the cookie using a `ResponseEntity`. This response entity then sets the cookie in the browser.

By using the SameSite attribute, we also inherently protect our cookies from CSRF attacks.

## Requiring Tokens on Endpoints, Limiting Domains, and more (SecurityConfig.java)

Now that we can authenticate, we obviously need to ensure that this authentication can be used somewhere. This is an example of using roles and authentication in `SecurityConfig.java`. What this code does is that it takes the endpoints and ensures that they were authenticated with the proper role.

```java
authorizeHttpRequests(auth -> auth
                .requestMatchers(HttpMethod.POST,"/authenticate").permitAll()
                .requestMatchers(HttpMethod.POST, "/api/person/**").permitAll()
                .requestMatchers(HttpMethod.GET, "/api/person/**").authenticated()
                .requestMatchers(HttpMethod.GET, "/api/people/**").authenticated()
                .requestMatchers(HttpMethod.PUT, "/api/person/**").authenticated()
                .requestMatchers(HttpMethod.DELETE, "/api/person/**").hasAuthority("ROLE_ADMIN"))
```
There are a couple parts here to analyze:
- the requestMatchers matches the required methods to each endpoint to prevent misuse of endpoints.
- Similarly there are three ending functions: `permitAll()`, `authenticated()`, and `hasAuthority()`. 
  - `permitAll()` permits everyone to use that endpoint
  - `authenticated()` requires a token to be used
  - `hasAuthority(<ROLE>)` only allows with certain authority.

Thus, we can see if authentication is valid through these two methods.

Another way of preventing CSRF attacks is through this block: `.csrf(csrf -> csrf.disable())`. But note this is disabled. This is because we carry a token with us for authentication, with roles. Thus, CSRF + SameSession makes this much harder.

## CORS Configuration (SecurityConfig.java)

Your Spring project also needs to have some configuration you know about to ensure CORs, etc. works.

There are two main files where this is present in spring. The first is the line in `SecurityConfig.java`: `.cors(Customizer.withDefaults())`. 

What this does is that it sets cors to defaults, just setting default CORS policies.  

The other is another one document for configuration that includes config in any API controller. It typically follows:

```java
package com.nighthawk.spring_portfolio.system;

import org.springframework.lang.NonNull;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
import org.springframework.security.crypto.password.PasswordEncoder;
import org.springframework.context.annotation.*;
import org.springframework.web.servlet.config.annotation.*;

@Configuration
public class MvcConfig implements WebMvcConfigurer {

    // set up your own index
    @Override
    public void addViewControllers(@NonNull ViewControllerRegistry registry) {
        registry.addViewController("/login").setViewName("login");
    }

    @Bean
    public PasswordEncoder passwordEncoder(){
        return new BCryptPasswordEncoder();
    }

    /* map path and location for "uploads" outside of application resources
       ... creates a directory outside "static" folder, "file:volumes/uploads"
       ... CRITICAL, without this uploaded file will not be loaded/displayed by frontend
     */
    @Override
    public void addResourceHandlers(final @NonNull ResourceHandlerRegistry registry) {
        registry.addResourceHandler("/volumes/uploads/**").addResourceLocations("file:volumes/uploads/");
    }
    
    @Override
    public void addCorsMappings(@NonNull CorsRegistry registry) {
        registry.addMapping("/**").allowedOrigins("https://spring.nighthawkcodingsociety.com", "https://nighthawkcoders.github.io", "http://127.0.0.1:4100", "http://localhost:4100");
    }
    
}
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
    server_name schaal.stu.nighthawkcodingsociety.com ; # Change server name to the one on R53
    # Configure CORS Headers
    location / {
        proxy_pass http://localhost:8091; # Change port to port on docker
        # Simple requests
        if ($request_method ~* "(GET|POST|PUT|DELETE)") { # Customize Request methods based on your needs
        }
        # Preflighted requests 
        if ($request_method = OPTIONS ) {
                add_header "Access-Control-Allow-Credentials" "true";
                add_header "Access-Control-Allow-Origin"  "https://men-in-brown.github.io";
                add_header "Access-Control-Allow-Methods" "GET, POST, PUT, DELETE, OPTIONS, HEAD"; # Make sure the request >
                add_header "Access-Control-Allow-Headers" "Authorization, Origin, X-Requested-With, Content-Type, Accept";
                return 200;
        }
    }
}

    listen [::]:443 ssl; # managed by Certbot
    listen 443 ssl; # managed by Certbot
    ssl_certificate /etc/letsencrypt/live/schaal.stu.nighthawkcodingsociety.com/fullchain.pem; # managed by Certbot
    ssl_certificate_key /etc/letsencrypt/live/schaal.stu.nighthawkcodingsociety.com/privkey.pem; # managed by Certbot
    include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot
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


And now you know how Spring and CORS work!