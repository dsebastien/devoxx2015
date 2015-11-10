# Getting Single Page Application Security Right

## About
* Philippe De Ryck (@PhilippeDeRyck)


## Links
* slides: https://distrinet.cs.kuleuven.be/events/websecurity/files/20151110_DeRyck_Devoxx2015.pdf
* slides: http://www.secappdev.org/handouts/2015/Philippe%20De%20Ryck/DeRyck_SecuringSPA.pdf
* OWASP top ten
* https://cwe.mitre.org/
* to check: OWASP encoder
* https://code.google.com/p/mustache-security/
* https://distrinet.cs.kuleuven.be/research/publications/index.do?sortBy=member&memberID=u0055658

## Modern age
* users are more vulnerable than ever
* web applications should go the extra mile for security
  * use the available tools to achieve a maximum level of security
  * anticipate security issues

...

## Traditional Web apps
* the server does everything
* ...

## Evolution
* started working with APIs & XHR
* the server still managed everything

## Single Page Applications
* logic deported to the client side
* the server still manages requests parsing, data storage and interactions with the client side
* the server is nothing more than a data store with some APIs

Q: how to separate back-end from the front-end?

## SPA Architecture
Client side application
* default browser security policies
* javascript apis
* server-controller security policies
* client-side data storage
* session data

Server-side:
* static application files
* API
* ...

Security flags:
* cookie security flags
* X-Frame-Options
* Content Security Policy (CSP)
* Cross-Origin Resource Sharing (CORS)
* HTTP Strict Transport Security
* HTTP Public Key Pinning
* Subresource Integrity

## HTTP and State Management
* HTTP is stateless by nature
* HTTP does support the sending of authentication data
  * authorization header
  * sends credentials on every request
  * server has no control over sessions

## Cookies make HTTP Stateful
* Secure; and HttpOnly flags are important
* on the server side, each session should have a unique id (UUID and not simple int incrementation)
* stateful on the server side
* gives full control at the server side (track active sessions, invalidate expired sessions)
* server-side sessions depend on the unguessability of the ID

## Session Management
### Server-side vs client-side sessions
With SPAs:
* stateless API pushes all session information to the client
* server has no control over the sessions
* ...

### Client-side sessions with cookies
* flow
  * logon
  * cookie generation by the server
  * stored by the client
  * next request: same flow (login, ...)
* stateless server (no session tracking)
* very similar to server-side sessions
* data is simply stored in a different place
* transparent to the application
* client-side sessions depend on the integrity of the data
* server-side sessions depend on the unguessability of the ID

How do you support cookie-based sessions in a SPA?
* by running it in a browser
* cookies are supported everywhere
* the browser stores everything per domain in a cookie jar
* sending the cookies is automatic

Cookies are only a means of transport:
* data is being sent back and forth between client and server
* ...

### Cross-Site Request Forgery (CSRF)
* malicious sites trigger unwanted requests to other domains
* browser happily sends cookies along with those requests (thus impersonating the user)

First approach:
* hide token in the HTML page and check on submission
* same-origin policy prevents other contexts from getting the token

Second approach:
* check the origin header sent by the browser
  * automatically added to state-changing requests (POST/PUT/DELETE)

Third way: transparent tokens
* compare a cookie value against a header value
* browser's cookie policies prevent illegitimate access
* well-suite for stateless APIs
  * supported by numerous libraries for various frameworks
* enabled by default in AngularJS

### Client-side sessions with tokens
...

Tokens as an alternative to cookies:
* login
* get a token -> stored on the client side
* send the token with new requests

Tokens inherently protects from CSRF attacks.

Tokens are sent to the client
* in an HTTP header or in the body

JSON Web Tokens (JWT):
* blob of data
* contains three sections of base64-encoded data
  * header
  * payload
  * signature

JWT is the standardized way to exchange session data:
* part of a JSON-based Identity Protocol Suite
  * together with specs for encryption, signatures and key exchange
* used by OpenID Connect on top of OAuth 2.0

...

With AngularJS:
* interceptor

By default, JWT are base64-encoded but not encrypted, so confidentiality is a problem. Integrity is built in through the signature.

There are libraries for most languages. Well suited to exchange identity information between microservices.

JWT has gotten a bad reputation lately
* implementation problems in the libraries
* not a systemic security failure
* quickly fixed in the official libraries

Problem 1: the "none" algorithm was widely supported
* client controls the contents of the JWT
* allows the attacker to create a valid but unsigned token
* ...

Problem 2: confusion between HMAC and public key crypto
* ...

Best practices:
* ....

## Cross-Site Scripting (XSS)

### About
Old problem.

XXS leads to the execution of attacker-controlled code in the context of the vulnerable application in the victim's browser.

The payload of an XSS is provided by the attacker.

The victim's browser triggers the attack

The malicious code runs within the application's context. the attacker's code has the same privileges as the application code.

### Stored XSS
The attacker injects a payload which is then stored on the server side of the application

### Reflected XSS
The attacker gives the victim a link that contains a URL going to the application with a payload. The victim opens the link and thus sends the request along with the payload to the server.

The server renders the response

...

### DOM-based XSS
Again a link towards the victim. The payload is not necessarily sent to the server. The payload is injected in the victim's page and gets executed.

### Protection
* secure coding practices
  * do not rely on simple filters (e.g., removing <, >, &, ", ')
  * use context-sensitive output encoding
    * encode the untrusted content in a relevant way depending on the context
    * OWAP encoder

### Mustache Security
* project dedicated to JS MVC security pitfalls
* ...

## Separating back-end and front-end
* server functionalities reduced
  * API and provide data
* ...

## Single page application

### Angular Strict Contextual Escaping (SCE)
AngularJS tries to protect you from injection attacks
ng-bind will never produce HTML
ng-bind can produce HTML, but not without protection
* ...

ngSanitize module:
* available in the core
* snitizes inputs
* removes potentially dangerous features (e.g., attributes of a form, event handlers, ...)

If you really really want raw trusted HTML...
* $sce.trustAsHtml

### Data Binding best practices
* always use the default binding mechanism
* if you need a safe set of HTML tags in the output
  * use sanitization
  * do not write it yourself
* ...

...

## Content Security Policy (CSP)


