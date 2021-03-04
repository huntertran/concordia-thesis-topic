Thesis - Patterns and Anti patterns in RESTful API
---

Concordia University - Montréal - Québec - Canada

<!-- TOC -->

- [1. Potential new patterns and anti patterns](#1-potential-new-patterns-and-anti-patterns)
    - [1.1. API has at least one version <> API don't have any version](#11-api-has-at-least-one-version--api-dont-have-any-version)
    - [1.2. Hardcoded Endpoints?](#12-hardcoded-endpoints)
    - [1.3. Time out <> No time out](#13-time-out--no-time-out)
    - [1.4. An extension of Breaking Self-Descriptiveness](#14-an-extension-of-breaking-self-descriptiveness)
- [2. Criteria for RESTful API design](#2-criteria-for-restful-api-design)
- [3. Papers](#3-papers)

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

In the extension to this anti patterns, there is some case that the developer don't use the headers, but invent their own.

Example:

```
https://example.com/api/module/status/json
```

In the example above, instead of using `Accept` header, the developer include the MIME type in the URI.

# Criteria for RESTful API design
<a id="markdown-criteria-for-restful-api-design" name="criteria-for-restful-api-design"></a>



# 2. Papers
<a id="markdown-papers" name="papers"></a>

1. A systematic Review of API Evolution Literature

[Summary](topics/out/api_evo_lit_review_rq1.png) for RQ1

2. Maintenance Support for Microservice-based Systems by Specification and Identification of Microservice Antipatterns

3. Design and Describe REST API without Violating REST: A Petri Net Based
Approach

