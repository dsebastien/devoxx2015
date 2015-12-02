# The never-ending REST API design

## Links
* Status codes map: http://restlet.com/http-status-codes-map
* Star Wars API example: https://swapi.co/
* DHC: REST/HTTP API Client: https://chrome.google.com/webstore/detail/dhc-resthttp-api-client/aejoelaoggembcahagimdiliamlcdmfm?hl=ending
* Design
  * http://www.vinaysahni.com/best-practices-for-a-pragmatic-restful-api
  * https://github.com/paypal/api-standards/blob/master/api-style-guide.md
  * http://blog.octo.com/en/design-a-rest-api/
  * https://github.com/interagent/http-api-design/blob/master/SUMMARY.md
  * http://www.troyhunt.com/2014/02/your-api-versioning-is-wrong-which-is.html

## REST
Representational State Transfer

Architectural properties:
* performance
* scalability
* simplicity
* modularity
* visibility
* portability
* reliability

Architectural constraints
* client-server
* stateless
* cacheable
* layered system
* code on demand
* uniform interface

Uniform interface
* identification of _resources_ (e.g., /api/v1/cars/123)
* manipulation of resources through _representations_ (e.g., json, xml, ...)
* self-descriptive messages (HTTP verbs, media types, cacheability, ...)

## Types of resources
Single item vs collections

* /api/v1/cars
  * GET: list the cars
  * POST: create a new car
  * PUT: replace the entire collection
  * DELETE: delete all the cars
* /api/v1/cars/123
  * GET: retrieve a specific car
  * POST: error
  * PUT: replace or create an individual car
  * DELETE: delete an individual car

## Favor _nouns_ over _verbs_.
* nouns refer to resources
* verbs refer to actions
  * resources are handled with HTTP verbs

## Verbs can be used for actions or calculations:
* /login, /logout
* /convertType
* /repositories/123/star

## Singular vs Plural resources
Prefer _plural forms_: /tickets/123 vs /ticket/123

Avoid confusing off singular vs plural forms:
* /person vs /people
* easier for URL routing (same prefix)
* think of it as "this is the 123th item of the tickets collection"

## Casing
* prefer _lowercase_ and _snake\_case_
* _underscores_ are common in APIs
* choose one and stay consistent

## Relations in URLs
* /tickets/123/messages/4
  * sub-resources of the main resource
  * a ticket could be a group of messages
  * think in term of whether that sub-resource stands on its own or not
* /usergroups/234/users/67
  * a user could belong to different usergroups
  * users should have an URL of their own, referenced from the usergroup payload

## API parameters
* Path
  * for required elements
  * if it identifies a resource if should be in the path
* Query
  * if it is option
  * if it refines a query collection (e.g., collection filter)
* Body
  * if there is advanced logic
  * if there is a need to send a search query
  * can be used/provided in addition to query (as alternative)
* Header
  * for global values (e.g., API key, JWT, ...)

## HTTP Status Codes
* 1xx: Hold on
* 2xx: Here you go!
* 3xx: Go away!
* 4xx: You fucked up!
* 5xx: I fucked up!

Frequently used codes:
* 100: continue
* 200: OK (do NOT use this one for all!)
* 201: Created
  * POST /cars ...
    * HTTP/1.1 201 Created
    * Location: http://.../v1/cars/5050
    * Helps with navigation through the API
    * Avoid having to guess what the URLs are
* 202: Accepted: OK but no response content (e.g., request to be processed asynchronously)
* 203: Non-authoritative information
* 204: No content: if resource deleted successfully and no payload is returned (could as well reply 200 with the deleted resource in the response content)
* 206: Partial content: return a part of the whole response and support paging through the 'Link' header
* 301: Moved Permanently
* 302: Found
* 303: See Other
* 304: Not modified: when HTTP caching are in play; the client should have a version in cache already
* 307: Temporary Redirect
* 308: Permanent Redirect
* 400: Bad Request
* 401: Unauthorized
* 403: Forbidden
* 404: Not Found
* 405: Method Not Allowed
* 429: Too Many Requests
* 500: Internal Server Error
* 501: Not Implemented
* 503: Service Unavailable
* 509: Bandwidth Limit Exceeded

## Last-Modified
First approach:
* GET /users/123
* Modified-Since: 1372400000

Reply:
* 200 OK
* Last-Modified: 1372700873

Second approach with ETag:

* GET /users/123
* If-None-Match: a45ef...

Reply:
* 200 OK
* ETag: 68689...

Second request:
* GET /users/123
* If-None-Match: 68689...

Second reply:
* 304 Not Modified


## Pagination with query parameters
With a page number: ?page=23
* can also specify a page size
* might get odd results when insertion happen

With a cursor: ?cursor=34ea3fd6
* insertion-friendly

With a semantic parameter: ?page=A
* interesting when limited / discrete number of pages

## Pagination with accept range header
Accept range header not only for bytes

* GET /users
* 206 Partial content
* Accept-Rangers: users
* Content-Range: users 0-9/200

* GET /users
* Range: users=0-9

Not often seen.

IETF states that 206 Partial content should only be sent in reply to ranged requests. If you would like to realize pagination or other partial requests using the 206 Partial Content response code, you should go the full step and use range request headers (ranges are not queries) and specify your own range unit like "page" as part of your protocol.

## Wrapped collections
Prefer unwrapped collections
* unless there is specific collection payload metadata
* pagination are better in HTTP headers

Avoid the additional boilerplate

Example:
```
GET /tickets
Content-Type: application/json

{
    data: {
        { id: 1, ... },
        { id: 2, ... }
    }
}
```

VS the more lightweight approach:

```
GET /tickets
Content-Type: application/json

{
    { id: 1, ... },
    { id: 2, ... }
}
```

Thus unless there is metadata about the whole collection, the succint form is preferred.

## Provide helpful error paylods
No definitive standard (yet)

Don't only return the error code. Return an object containing as many details as possible. There are proposals (e.g., vnd-error mime-type)

```
403 Forbidden
Content-Type: application/problem+json
Content-Language: en

{
    "type": "https://...",
    "title": "You do not have enough credit",
    "instance": "blablabla",
    "correlationId": "....",
    balance: 30,
    ...
}
```

## Treating unknown status codes
An unknown status code should be treated as the first of the family.
Example: 599 -> 500: Internal Server Error

## Rate limitation
Often make use of custom headers (i.e., non-standard):

* 200 OK
* Date: ...
* X-RateLimit-Limit: 60
* X-RateLimit-Remaining: 30
* X-RateLimit-Reset: 12903234 (remaining window before reset in UTC epoch seconds)

## One size fits it all (or not)
Provide different payloads for different consumers

## Filtering
Different styles

* Specify fields you're interested in: GET http://...?fields=firstname,lastname,age
* Specify excluded fields: ...?exclude=biography,resume
* Specify a "style": ...?style=compact

Prefer the ... prefer header:

```
GET /users/123 HTTP/1.1
Content-Type: application/json
Prefer: return=minimal (different profiles: minimal, mobile, full, ...)
Vary: Prefer,Accept,Accept-Encoding

HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Vary: Prefer,Accept,Accept-Encoding
Preference-Applied: return=minimal
```

## Expanding referenced resources
Use the dot notation to state that sub-resources should be returned:
* GET https://.../users/123?fields=address,zip

* user
  * name
  * address
    * zip
    * country
    * ...
  * ...

## Sorting
SQL-style:
* GET https://...?sort=title+DESC
* GET https://...?sort=title+DESC,author+ASC

Sort + asc/desc combo:
* GET https://...?sort=title&desc=title
* GET https://...?sort=title,author&desc=title&asc=author

In the last example, the sorting is separated from the list of fields to retrieve.

## Searching
* combine various filtering fields
* or provide a full-blown query language

No standard here either

## Versioning
Most frequently put in the URL: https://.../v1/restaurants/123

Less frequent custom header: X-API-Version: 2

Even less frequent: Accept header: clients don't have to change endpoint but update headers:

```
GET /restaurants
Accept: application/vnd.restaurants.v2+json
```

## Hypermedia
Make the API more discoverable (HATEoAS)

First described in the maturity model of Richardson:
* Level 0: The Swamp of POX: exchange XML, only GET/POST and not the rest of the verbs
* Level 1: Resources: define actual resources
* Level 2: HTTP verbs: properly use all HTTP verbs
* Level 3: Hypermedia controls

Level 3 provides links to navigate within the API (e.g., if clients are linked to enterprises, then getting a client will give a link to the enterprises)

Pros for Level 3:
* more generic clients
* can palliate the need for API versioning

Cons for Level 3:
* heavier payloads (bad for mobile)
* clients still need to understand what links are about and how to represent them in their UIf

Many choices to implement hypermedia:
* HAL: most well known
* JSON-LD
* Collection+JSON
* SIREN
* ...

## About IDs
Usually, avoid counters for IDs (e.g., 1, 2, 3, 4, ...).
Prefer UUIDs:
* makes it harder for attackers to scan and discover existing resources
* auto-incrementing IDs might not be unique in distributed systems
  * suppose that the API is provided by multiple servers: it could be that the same ID is generated and given to different clients, whereas it would be almost impossible with UUIDs
