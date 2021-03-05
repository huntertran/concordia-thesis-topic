Thesis - Patterns and Anti patterns in RESTful API
---

Concordia University - Montréal - Québec - Canada

<!-- TOC -->

- [1. Potential new patterns and anti patterns](#1-potential-new-patterns-and-anti-patterns)
    - [1.1. API has at least one version <> API don't have any version](#11-api-has-at-least-one-version--api-dont-have-any-version)
    - [1.2. Hardcoded Endpoints?](#12-hardcoded-endpoints)
    - [1.3. Time out <> No time out](#13-time-out--no-time-out)
    - [1.4. An extension of Breaking Self-Descriptiveness](#14-an-extension-of-breaking-self-descriptiveness)
    - [1.5. Use POST instead of PATCH](#15-use-post-instead-of-patch)
    - [1.6. Use POST instead of PUT](#16-use-post-instead-of-put)
- [2. Principle for RESTful API design](#2-principle-for-restful-api-design)
- [3. Some criteria for RESTful API design](#3-some-criteria-for-restful-api-design)
- [4. Papers](#4-papers)

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

## 1.4. An extension of `Breaking Self-Descriptiveness`
<a id="markdown-an-extension-of-breaking-self-descriptiveness" name="an-extension-of-breaking-self-descriptiveness"></a>

Idea found: paper 3

Description: In [Breaking Self-Descriptiveness](http://sofa.uqam.ca/resources/antipatterns.php#Breaking%20Self-descriptiveness), the heuristic rule check if the `response headers` or `request headers` of the inspected API belong to a set of predefined standardized headers.

![breaking self-descriptiveness](http://sofa.uqam.ca/soda-r/img/BSD.jpg)
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

## 1.5. Use POST instead of PATCH
<a id="markdown-use-post-instead-of-patch" name="use-post-instead-of-patch"></a>

By definition, the `POST` method requests that the server accept the entity enclosed in the request as a new subordinate of the web resource identified by the URI. A lot of developers use `POST` to modify a piece of information, instead of `PATCH`

> This can be generalized as "using the wrong HTTP Verbs"

## 1.6. Use POST instead of PUT
<a id="markdown-use-post-instead-of-put" name="use-post-instead-of-put"></a>

By definition, the `PUT` method requests that the enclosed entity be stored under the supplied URI (aka create new object). In practice, developer usually use `POST` (or even `GET`) to create new object in database.

> This can be generalized as "using the wrong HTTP Verbs"

# 2. Principle for RESTful API design
<a id="markdown-principle-for-restful-api-design" name="principle-for-restful-api-design"></a>

In paper 4:
1. Usage of HTTP Verbs
2. Usage of HTTP status codes
3. Provide paging for collection
4. Definition of Resources

# 3. Some criteria for RESTful API design
<a id="markdown-some-criteria-for-restful-api-design" name="some-criteria-for-restful-api-design"></a>



# 4. Papers
<a id="markdown-papers" name="papers"></a>

1. A systematic Review of API Evolution Literature

[Summary](topics/out/api_evo_lit_review_rq1.png) for RQ1

2. Maintenance Support for Microservice-based Systems by Specification and Identification of Microservice Antipatterns
3. Design and Describe REST API without Violating REST: A Petri Net Based
Approach
4. Model-driven Development of RESTful APIs
5. Preliminary Analysis of REST API Style Guidelines