Thesis - Patterns and Anti patterns in RESTful API
---

Concordia University - Montréal - Québec - Canada

<!-- TOC -->

- [1. Potential new patterns and anti patterns](#1-potential-new-patterns-and-anti-patterns)
    - [1.1. API has at least one version <> API don't have any version](#11-api-has-at-least-one-version--api-dont-have-any-version)
    - [1.2. Hardcoded Endpoints?](#12-hardcoded-endpoints)
    - [1.3. Time out <> No time out](#13-time-out--no-time-out)
    - [1.4. An extension of Breaking Self-Descriptiveness](#14-an-extension-of-breaking-self-descriptiveness)
    - [1.5. Using the wrong HTTP Verbs](#15-using-the-wrong-http-verbs)
    - [1.6. weak - POST - PUT - PATCH return simple result vs return the modified object](#16-weak---post---put---patch-return-simple-result-vs-return-the-modified-object)
    - [1.7. No timezone for time data](#17-no-timezone-for-time-data)
    - [1.8. Using pagination for list](#18-using-pagination-for-list)
- [2. Principle for RESTful API design](#2-principle-for-restful-api-design)
- [3. Some criteria for RESTful API design](#3-some-criteria-for-restful-api-design)
    - [3.1. Following standards](#31-following-standards)
    - [3.2. No customizing headers Minimized customization](#32-no-customizing-headers-minimized-customization)
    - [3.3. Adequate security implementation](#33-adequate-security-implementation)
    - [3.4. Support Cross-origin Resource Sharing CORS](#34-support-cross-origin-resource-sharing-cors)
- [4. Papers](#4-papers)
- [5. Tools](#5-tools)

<!-- /TOC -->

# 1. Potential new patterns and anti patterns
<a id="markdown-potential-new-patterns-and-anti-patterns" name="potential-new-patterns-and-anti-patterns"></a>

## 1.1. API has at least one version <> API don't have any version
<a id="markdown-api-has-at-least-one-version-%3C%3E-api-don't-have-any-version" name="api-has-at-least-one-version-%3C%3E-api-don't-have-any-version"></a>

Idea found: #2.2

Descriptions: API evolved. The user of the API should have time to adapt with the changes.

Approach:

* Using URI: The API URI should include versioning information to ensure that the functionality will be the same.

```
http://api.example.com/v1
http://apiv1.example.com
```

* Using headers: using the accept header to include the version information

```
Accept: application/vnd.example.v1+json
Accept: application/vnd.example+json;version=1.0
```

## 1.2. Hardcoded Endpoints?
<a id="markdown-hardcoded-endpoints%3F" name="hardcoded-endpoints%3F"></a>

Idea found: paper 2

Descriptions: Some or at least 1 of the API URI is hardcoded in the source code. This could be explained as the server self call the API rather than directly access the services, or simply bad coding.

## 1.3. Time out <> No time out
<a id="markdown-time-out-%3C%3E-no-time-out" name="time-out-%3C%3E-no-time-out"></a>

Idea found: paper 2

Description: Each transaction should have a time out value. The client cannot wait forever for a transaction to be complete.

Definition of long running transaction:

* [Microsoft](https://github.com/Microsoft/api-guidelines/blob/vNext/Guidelines.md#13-long-running-operations)

## 1.4. An extension of `Breaking Self-Descriptiveness`
<a id="markdown-an-extension-of-breaking-self-descriptiveness" name="an-extension-of-breaking-self-descriptiveness"></a>

Idea found: paper 3

Description: In [Breaking Self-Descriptiveness](http://sofa.uqam.ca/resources/antipatterns.php#Breaking%20Self-descriptiveness), the heuristic rule check if the `response headers` or `request headers` of the inspected API belong to a set of predefined standardized headers.

![breaking self-descriptiveness](https://i.imgur.com/DJWZrP0.png)

_source: http://sofa.uqam.ca/resources/antipatterns.php#Breaking%20Self-descriptiveness_

In the extension to this anti patterns, there is some case that the developer don't use the headers, but invent their own.

Example:

[Floodlight REST API](https://floodlight.atlassian.net/wiki/spaces/floodlightcontroller/pages/1343492/Floodlight+REST+API+pre-v1.0)

```
http://192.168.110.2:8080/wm/core/controller/switches/json
```

In the example above, instead of using `Accept` header, the developer include the MIME type in the URI.

> Floodlight is the leading open source OpenFlow controller.
>
> OpenFlow is a open standard managed by Open Networking Foundation. It specifies a protocol by which a remote controller can modify the behavior of networking devices through a well-defined “forwarding instruction set”. Floodlight is designed to work with the growing number of switches, routers, virtual switches, and access points that support the OpenFlow standard.

## 1.5. Using the wrong HTTP Verbs
<a id="markdown-using-the-wrong-http-verbs" name="using-the-wrong-http-verbs"></a>

* `GET` = read
* `POST` = create
* `PUT` = create or update the whole object (caller should know the ID of the object).
* `PATCH` = update part of the object
* `DELETE` = delete
* `HEAD` = get response headers
* `OPTION` = get supported verbs

`PUT` is not recommended, since it has issue with backward compatibility when adding new field to the object.

> _**IDEMPOTENT**_: A method is idempotent when multiple identical calls to the same method have the same effect as a single call.
> 
> `GET`, `HEAD`, `PUT`, `DELETE` have this property, while `POST` does NOT.
> 
> For example: Multiple `DELETE` request with the same parameters will have the same effect with a single `DELETE` request. In the contrary, multiple `POST` request with the same parameters suppose to create new distinc object on each request.

## 1.6. weak - POST - PUT - PATCH return simple result vs return the modified object
<a id="markdown-weak---post---put---patch-return-simple-result-vs-return-the-modified-object" name="weak---post---put---patch-return-simple-result-vs-return-the-modified-object"></a>

The request that create an object should return the successfully created object. The request that update an object should return the updated data.

This is to confirm with the caller the successful operation, rather than a simple `200 OK` status code, but not sure which data was created/updated to the database.

## 1.7. No timezone for time data
<a id="markdown-no-timezone-for-time-data" name="no-timezone-for-time-data"></a>

Client access the service from all over the world. Therefore, for any data related to time format, it should include timezone information. [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) format could be use

```
https://localhost.com/api/customers/123

==>
HTTP method: GET
Date: 2021-03-09T02:35:00+00:00

<==
HTTP/1.1 200 OK
```

> Noted: If only date is required, then timezone maybe omitted

## 1.8. Using pagination for list
<a id="markdown-using-pagination-for-list" name="using-pagination-for-list"></a>

A collection should support pagination, even if result are typically small

> Rationale: If an API does not support pagination from the start, supporting it later is troublesome because adding pagination breaks the API's behavior. Clients that are unaware that the API now uses pagination could incorrectly assume that they received a complete result, when in fact they only received the first page.
> 
> source: [https://cloud.google.com/apis/design/design_patterns#list_pagination](https://cloud.google.com/apis/design/design_patterns#list_pagination)

```java
//specify the requested page
page_number: int

//specify how many item should be included in one page
page_size: int

//a field in response to indicate the total page based on page_size
total_page: int
```

# 2. Principle for RESTful API design
<a id="markdown-principle-for-restful-api-design" name="principle-for-restful-api-design"></a>

In paper 4:
1. Usage of HTTP Verbs
2. Usage of HTTP status codes
3. Provide paging for collection
4. Definition of Resources

# 3. Some criteria for RESTful API design
<a id="markdown-some-criteria-for-restful-api-design" name="some-criteria-for-restful-api-design"></a>

## 3.1. Following standards
<a id="markdown-following-standards" name="following-standards"></a>

The developers/system architect of the RESTful API should follow the standards and protocols predefined by [IANA](https://www.iana.org/protocols)

## 3.2. No customizing headers (Minimized customization)
<a id="markdown-no-customizing-headers-minimized-customization" name="no-customizing-headers-minimized-customization"></a>

Customized headers add extra work to the implementation, usage and documentation of the API. The customized headers need to be maintained over generations of the service.

## 3.3. Adequate security implementation
<a id="markdown-adequate-security-implementation" name="adequate-security-implementation"></a>

The RESTful API should employ the current, up-to-date security mechanism. For 2021, these mechanism should be the usage of Secure Sockets Layer (SSL) to encrypt the traffic, and Transport Layer Security (TLS) to secure the data in the request/response

## 3.4. Support Cross-origin Resource Sharing (CORS)
<a id="markdown-support-cross-origin-resource-sharing-cors" name="support-cross-origin-resource-sharing-cors"></a>

# 4. Papers
<a id="markdown-papers" name="papers"></a>

1. A systematic Review of API Evolution Literature

[Summary](topics/out/api_evo_lit_review_rq1.png) for RQ1

2. Maintenance Support for Microservice-based Systems by Specification and Identification of Microservice Antipatterns
3. Design and Describe REST API without Violating REST: A Petri Net Based
Approach
4. Model-driven Development of RESTful APIs
5. Preliminary Analysis of REST API Style Guidelines

# 5. Tools
<a id="markdown-tools" name="tools"></a>

* [`Protocol Buffer`](https://developers.google.com/protocol-buffers/) is a language-neutral, platform-neutral for serializing structured data.