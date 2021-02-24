Thesis - Patterns and Anti patterns in RESTful API
---

Concordia University - Montréal - Québec - Canada

<!-- TOC -->

- [1. Potential new patterns and anti patterns](#1-potential-new-patterns-and-anti-patterns)
    - [1.1. API has at least one version <> API don't have any version](#11-api-has-at-least-one-version--api-dont-have-any-version)
    - [1.2. Hardcoded Endpoints?](#12-hardcoded-endpoints)
    - [1.3. Time out <> No time out](#13-time-out--no-time-out)
- [2. Papers](#2-papers)
    - [2.1. A systematic Review of API Evolution Literature](#21-a-systematic-review-of-api-evolution-literature)
    - [2.2. Maintenance Support for Microservice-based Systems by Specification and Identification of Microservice Antipatterns](#22-maintenance-support-for-microservice-based-systems-by-specification-and-identification-of-microservice-antipatterns)

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

Idea found: #2.2

Descriptions: Some or at least 1 of the API URI is hardcoded in the source code. This could be explained as the server self call the API rather than directly access the services, or simply bad coding.

## 1.3. Time out <> No time out
<a id="markdown-time-out-%3C%3E-no-time-out" name="time-out-%3C%3E-no-time-out"></a>

Idea found: #2.2

Description: Each transaction should have a time out value. The client cannot wait forever for a transaction to be complete.



# 2. Papers
<a id="markdown-papers" name="papers"></a>

## 2.1. A systematic Review of API Evolution Literature
<a id="markdown-a-systematic-review-of-api-evolution-literature" name="a-systematic-review-of-api-evolution-literature"></a>

[Summary](topics/out/api_evo_lit_review_rq1.png) for RQ1

## 2.2. Maintenance Support for Microservice-based Systems by Specification and Identification of Microservice Antipatterns
<a id="markdown-maintenance-support-for-microservice-based-systems-by-specification-and-identification-of-microservice-antipatterns" name="maintenance-support-for-microservice-based-systems-by-specification-and-identification-of-microservice-antipatterns"></a>

