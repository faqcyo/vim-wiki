[Back to PortSwigger index](index)

# API Testing



## API Recon

Get to know the API you're trying to test as much as possible, to discover its **attack surface**.

1. Identify the API's endpoints.
2. Determine how to interact with them
  - The input data the API processes
  - The type of data the API accepts
  - Rate limits and authentication mechanisms

## API Documentation

If the documentation of an API is publicly available, always start the API Recon stage from here.

There can be some endpoints without public documentation. In these cases you should crawl the API to find hidden, undocumented endpoints.

If you find the endpoint for a resource, make sure to investigate it completely, i.e, if i have the resource /api/endpoint/resource.jpg, we should investigate the endpoints /api/endpoint and /api.

Useful tools: Burp Scanner and Intruder.


## Identifying And Interacting With Api Endpoints

You can also gather information from applications that use the API. This is worth doing even if you have access to the documentation of the API.

For this, you should use a program to automate the crawling process, and gather enough information that helps in finding patterns that suggest some API endpoint.

Look for the JavaScript files, which can contain references to API endpoints.

Once you've located some endpoints, interact with them to expand the attack surface. For example, you can investigate how the HTTP request reacts when changing its method and media type.

As you interact with the requests, examine carefully the error messages and other responses. This information can be useful to later on construct a correct HTTP Request.

When testing HTTP methods, target low-priority objects.

If you are using Burp Suite, you can use the built-in HTTP verbs list in Burp Intruder.

Changing the content type on a request can have the following consequences:
  - Trigger errors that reveal valuable information, such as the version of a framework the webapp is using.
  - Bypass flawed defenses
  - Take advantage on the processing logic. For example, a request may work as intended with a JSON body, but may be susceptible with an XML body.

To change the content type we first have to modify the *Content-Type* header on the request and then reformat the body accordingly.

Some HTTP Methods:
GET
HEAD
POST
PUT
DELETE
CONNECT
OPTIONS
TRACE
PATCH

To identify hidden endpoints we can use a tool like Burp Intruder. For example, if we've discovered the endpoint "PUT /api/user/update", using Burp Intruder we can test for endpoints that have a similar structure. For example, we could replace */update* with */delete* or */add*, so that with Burp Intruder we test for the endpoints */api/user/delete* and */api/user/add* with all the possible methods.

When trying to look for these hidden endpoints, try to search for common patterns based on the API convention the product you're seeing has.

## Finding Hidden Parameters

Burp Intruder can help you find hidden parameters using a wordlist of common parameter names.

The Param Miner Bapp enables you to automatically guess up to 65.536 param names per request.

The content discovery tool enables you to discover content that isn't linked to visible content that you can browse to.

## Mass Assignment Vulnerabilities

Mass assignment (also known as auto-binding) can create hidden parameters by binding request parameters to fields of an internal object automatically. 

See https://cheatsheetseries.owasp.org/cheatsheets/Mass_Assignment_Cheat_Sheet.html#references-and-future-reading

Since mass assignment creates parameters based on an object field, you can identify these hidden parameters by manually examining objects returned by the API. 

For example, if we do **GET /api/user/123**, we may obtain:

```
{
  "id": 123,
  "name": "John Doe",
  "email": "john@example.com",
  "isAdmin": "false"
}
```

This may indicate that the hidden parameters *id* and *isAdmin* are fields of the *User* object, alongside with the name and email parameters.

## Server Side parameter pollution

Some products may have APIs that are not accessible through the internet. With Server Side parameter pollution, we can communicate through the public api and interact with this hidden API.

This vulnerability happens when a website embeds **user input** in a server-side request to an internal API.

This can make it possible to inject or manipulate parameters sent to the internal API by the server, allowing the attacker to: 
  - Override existing parameters
  - Modify the application behavior
  - Access unauthorized data.

You can test any type of user input for a possibility of parameter pollution, for example, query parameters, form fields, headers, and URL path parameters.

## Truncating query strings

Using an URL encoded *#* (%23) we can truncate the URL that the server will read. For example, if we send the following request `GET /userSearch?name=peter%23foo&back=/home` (note that the parameter *back* is added by the browser), the server would then send a request to its internal api with the following request: `GET /users/search?name=peter#foo&publicProfile=true`. The server might be able to tell whether we are trying to maliciuosly truncate the URL or not.

To identify whether we have truncated the URL succesfully, we should check the output from the request we've initially sent. If it returns the username, the server may have received the truncated URL. If we get an error like *Invalid Username*, the server treated #foo as part of the username, this suggests that the URL wasn't succesfully trunctated.

Note that if we are able to truncate the URL, the parameter publicProfile=true is removed, allowing you to set it to false and searching for private users.

You can also try injecting parameters using the URL enconded form of & (%26)

### Overriding existing parameters

We can do this by injecting the same parameter with a different value: `GET /userSearch?name=carlos%26name=admin&back/home` resulting in `GET /userSearch?name=carlos&name=admin&publicProfile=true`

Depending on the implementation of the server the interpretation of the parameter might gives us a clue about it.

For example:
  - In PHP it would take only the last paremeter.
  - In ASP.NET it would combine both parameters, resulting in carlos,admin returning an invalid username response
  - In Node.js / express it would take the first parameter only, without changes in the end response.

## Testing for server side parameter pollution

Consider a RESTful API that it reads the parameters directly from the URL rather than from its query. For example `GET /api/user/123` instead of `GET /api/user?id=123`.

We could do some parameter pollution with this using some path traversal statements.

Consider the following endpoint accesible from the internet: 

`GET /edit_profile.php?name=peter`

this will do the following request to the internal API:

`GET /api/private/users/peter`

If we replace peter with `peter%2f..%2fadmin` (which is the URL encoded equivalent of `peter/../admin`), the request received from the internal API would be

`GET /api/private/users/peter/../admin`

which in turn may get us info about the admin user, it depends on whether the server normalizes this path or not.

## Testing for server side parameter pollution in structured data formats

Examples of structured data formats are: JSON and XML.

To test for this type of parameter pollution, consider the following scenario:

The user makes the request

```
POST /myaccount
name=peter
```

Then the internal API receives the request

```
PATCH /users/7312/update
{"name": "peter"}
```

If we replace the body `name=peter` with `name=peter", "access_level": "administrator`, then the internal API receives the request:

```
PATCH /users/7312/update
{"name": "peter", "access_level": "administrator"}
```

Note that this will only happen whenever a server doesn't sanitize the user input.

Also note that the parameter `access_level` should be found on the finding hidden parameters stage.

If instead of an XML body on the initial request we use a JSON body, all we have to do is escape the ", for example `{"name": "peter\", \"access_level\": \"administrator`
