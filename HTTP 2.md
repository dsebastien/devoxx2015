# unRESTful Web Services with HTTP/2

## About
* Fabian Staber (@fstabr

## HTTP/2 intro
* available now
* all major Web browsers support it
* Jetty, Netty, WildFly (Undertow)
* Apache mod_h2
* NGINX

History:
* HTTP/1.1: 1999
* SPDY: 2009
* HTTP/2: 2015

HTTP/2 is all about reducing latency:
* less protocol overhead
* header compression
* multiplexing
* request prioritization
* server push

HTTP/2 does not break the Web
* same semantics as HTTP/1
  * request/response based communication
  * HTTP sessions and cookies
* any HTTP/1 application can be deployed on an HTTP/2 server without code change

Application Layer Protocol Negociation
* SSL is implemented in the JRE in package sun.security.ssl
* Jetty provides ALPN version for many OpenJDK 7 and 8 releases
* must start Java with VM parameter -Xbootclasspath/p:alpn-boot-VERSION.jar
* ALPN will be part of Java 9 (JEP 244)

HTTP/2 is chunked.

Upcoming standard libraries:
* Java SE: new HttpUrlConnection (JEP 110)
* Java EE: JAX-RS Client API (?)

## HTTP/2 new features and how to use them in Java

### Multiplexing
Execute multiple requests in parallel, multiplexed over a single connection.

### Server Push
When the server knows that the client will request another things right afterwards, it can directly send it to the client.

The client puts it in a cache, then when it's about to get fetch the file, it just checks its local cache and gets it from there.

HTTP/2 is not a replacement for WebSockets

Efficient polling with server push:
* REST interface where clients polls a resource
* in case server push is supported, client polls not by time interval but polling is triggered by push message
* fully compatible with REST semantics but much more efficient than polling with HTTP/1
* makes workarounds like long polling, websockets, etc obsolete

### Stream Prioritization and Flow Control
Headers frame may content stream dependency.
Priority frame contains 'Weight' field

An API is planned for this in Servlet 4.0

### Testing HTTP/2-based REST services
