# AWS API Gateway

## What is an API

An API is an Application Programming Interface.

## Types of APIs

* REST APIs (Representational State Transfer)
    * Uses JSON
* SOAP APIs (Simple Object Access Protocol)
    * Uses XML

## What is API Gateway?

Amazon API Gateway is a fully managed service that makes it easy for developers to publish,
maintain, monitor, and secure APIs at any scale. With a few clicks in the AWS Management
Console, you can create an API that acts as a 'front door' for applications to access data,
business logic, or functionality from your back-end services, such as applications running on
EC2, code running on Lambda, or any web application.

## What can API Gateway Do?

* Expose HTTPS endpoints to define a RESTful API
* Serverless-ly connect to services like Lambda & DynamoDB
* Send each API endpoint to a different target
* Run efficiently with low cost
* Scale effortlessly
* Track and control usage by API key
* Throttle requests to prevent attacks
* Connect to CloudWatch to log all requests for monitoring
* Maintain multiple versions of your API

## How do I configure API Gateway?

* Define an API (container)
* Define Resources and nested Resources (URL paths)
* For each Resource:
    * Select supported HTTP methods (verbs)
    * Set security
    * Choose target (i.e. EC2, Lambda, DynamoDB, etc.)
    * Set request and response transformations
* Deploy API to a Stage
    * uses API Gateway domain, by default
    * Can use custom domain
    * Now supports AWS Cert Manager: Free SSL/TLS certs!

## What is API Caching?

You can enable API caching in Amazon API Gateway to cache your endpoint's response.
With caching, you can reduce the number of calls made to your endpoint and also improve
the latency of the requests to your API. When you enable caching for a stage, API Gateway
caches responses from your endpoint for a specified time-to-live (TTL) period, in seconds.
API Gateway then responds to the request by looking up the endpoint response from the cache
instead of making a request to your endpoint.

## Same Origin Policy

In computing, the same-origin policy is an important concept in the web application secruiy model.
Under the policy, a web browser ermits scripts contained in a frist web page to access data in
a second web page, but only if both web pages have the same origin.

This is done to prevent Cross-Site Scripting (XSS) attacks.

* Enforced by web browsers.
* Ignored by tools like Postman and curl.

## Cross-Origin Resource Sharing (CORS)

Cross-Origin Resource Sharing (CORS) is one way the server at the other end (not client code in browser)
can relax the same-origin policy.

Cross-origin resource sharing (CORS) is a mechanism that allows restricted resources (e.g. fonts) on a
web page to be requested from another domain outside the domain from which the first resource was served.

* Browser makes an HTTPS OPTIONS call for a URL
    * OPTIONS is an HTTP method like GET, PUT and POST
* Server returns a response that says:
    * "These other domains are approved to GET this URL"
* Error - "Origin policy cannot be read at the remote resource?"
You need to enable CORS on API Gateway

## API Gateway Exam Tips

* Remember what API Gateway is at a high level
* API Gateway has caching capabilities to increase performance
* API Gateway is low cost and scales automatically. (don't need autoscale groups)
* You can throttle API Gateway to prevent attacks
* You can log results to CloudWatch
* If you are using JavaScript/AJAX that uses multiple domains with API Gateway, ensure that you have enabled CORS on API Gateway
* CORS is enforced by the client

# Advanced API Gateway

## Import APIs

You can use the API Gateway Import API feature to import an API from an external definition file into API Gateway.
Currently, the Import API feature supports Swagger v2.0 definition files.

With the Import API, you can either create a new API by submitting a POST request that includes a Swagger definition
in the payload and endpoint configuration, or you can update an existing API by using a PUT request that contains a
Swagger definition in the payload. You can update an API by overwriting it with a new definition, or merge a definition
with an existing API. You specify the options using a mode query parameter in the request URL.

## API Throttling

By default, API Gateway limits the steady-state request rate to 10,000 requests per second (RPS).
The maximum concurrent requests is 5000 requests across all APIs within an AWS account.
If you go over 10,000 requests per second or 5000 concurrent requests you will receive a 429 Too Many Requests error response.

* If a caller submits 10k requests in a one second period evenly (i.e. 10 requests every millisecond),
API Gateway process all requests without dropping any.
* If the caller sends 10k requests in the first millisecond, API Gateway serves 5k of those requests
and throttles the rest in the one-second period.
* If the caller submits 5k requests in the first millisecond and then evenly spread another 5k requests
through the remaining 999 milliseconds (e.g. about 5 requests every millisecond), API Gateway processes
all 10k requests in the one-second period without returning 429 Too Many Requests error responses.

## SOAP Webservice Passthrough

You can configure API Gateway as a SOAP webservice passthrough.

## Advanced API Gateway Exam Tips

* Import API's using Swagger 2.0 definition files
* API Gateway can be throttled
    * Default limits are 10k RPS or 5k concurrently
* You can configure API Gateway as a SOAP webservice passthrough
