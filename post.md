<!-- TOC -->

- [1. REST API Anti-Patterns' Best Practices](#1-rest-api-anti-patterns-best-practices)
    - [1.1. Entity Linking](#11-entity-linking)
    - [1.2. API Versioning](#12-api-versioning)
    - [1.3. Server Timeout](#13-server-timeout)
    - [1.4. POST-PUT-PATCH return](#14-post-put-patch-return)
    - [1.5. Content Negotiation](#15-content-negotiation)
        - [1.5.1. Problem](#151-problem)
        - [1.5.2. Best Practice](#152-best-practice)
        - [1.5.3. Existing Framework Supports](#153-existing-framework-supports)
    - [1.6. List Pagination](#16-list-pagination)
- [2. Evaluation](#2-evaluation)

<!-- /TOC -->

# 1. REST API (Anti)-Patterns' Best Practices
<a id="markdown-rest-api-anti-patterns'-best-practices" name="rest-api-anti-patterns'-best-practices"></a>

## 1.1. Entity Linking
<a id="markdown-entity-linking" name="entity-linking"></a>

**Problem**

```json
{
  "post": {
    "title": "Lorem ipsum",
    "content": "Lorem ipsum",
    "links": [
      {
        "rel": "comment",
        "method": "post",
        "uri": "/post/123/comment"
      },
      {
        "rel": "like",
        "method": "get",
        "uri": "/post/123/like"
      }
    ]
  }
}
```

In the above sample response, the server returns the link to related resources. This pattern is Entity Linking.

**Best Practice**

The method that handles the current request will have a list of related classes containing the related resources. To loop through the list, all these classes should be an implementation of an interface. Accessing methods implemented the same interfaces in different classes, based on the class type (i.e., dynamic biding), is called `double-dispatch`.

To implement `double-dispatch` in a high-level programming language, we could use `Visitor Pattern`.

![entity linking best practice](https://i.imgur.com/FO4AiPK.png)

All the controllers that are intended for populating links for the related resources will be the implementation of the `LinkedResource` interface. This interface has a method `accept()` that accepts a visitor instance of type `ResourceVisitor`. For each logic to select the resource to be included, a separated concrete `Visitor` will be created. These visitors could share the common logic in the abstract class `CommonResourceVisitor`. Developers can put hide the complexity of language refection logic here.

> The sample implementation with reflection is available for Java Spring and ASP.NET Core (see https://git.io/JGRWW and https://git.io/JGRW8).

## 1.2. API Versioning
<a id="markdown-api-versioning" name="api-versioning"></a>

**Problem**

The server should support API Versioning to allows the coexistence of multiple versions of the API.

**Best Practice**

Use `Proxy design pattern` to redirect/convert the request to the old version of the API. For each version of the exposed API, there should be a corresponding interface. The interface does not serve a purpose in the current version but will be the contract for the next version. The class will contain a private instance of each old API class that supports this next version. Thus, all the API versions will have some common logic that did not change over time. This logic can be separated into an abstract class. The API controllers inherit from it.

![api versioning](https://i.imgur.com/T2roJfk.png)

## 1.3. Server Timeout
<a id="markdown-server-timeout" name="server-timeout"></a>

**Problem**

When there is a long-running operation on the REST API server, the client needs to wait to receive a response. In a traditional system, the communication between the running operation and the object that consumes the operation result is permanent. However, in a REST API system, the servers communicate with the consumers (the client applications) through the network. This introduces some potential problems: if the client and the server disconnected, the operation continues to run on the server, but the result was no longer needed. The same if the client cancels or abandons the request. These problems will waste the server's resources.

**Best Practice**

A combination of Timeout and `Asynchronous Request-Reply pattern (HTTP Polling)` by [William Eastbury](https://docs.microsoft.com/en-us/azure/architecture/patterns/async-request-reply)

The design will require some extra work from both server and client developers. Developers need to implement an operation status checker and a cache system to store the operation result on the server. On the client side, the developers need to implement a polling mechanism to periodically get the operation status and finally get the response from the `Resource Endpoint`.

![server timeout](https://i.imgur.com/KsN05yt.png)

Detailed steps:
* When the client sends the request that triggers a long-running operation, the server will respond as quickly as possible with `202 Accepted` HTTP code.
* The server continues its long-running operation while the client is periodically polling for the result.
* When the result available, the polling will return with `302 Found` HTTP code with the URI for the result.
* The client sends a `GET` request to get the result.

In case the client is disconnected, the server wait for a timeout then terminates the operation.

## 1.4. POST-PUT-PATCH return
<a id="markdown-post-put-patch-return" name="post-put-patch-return"></a>

**Problem**

When the client application sends a request to modify the database (Create, Update, Delete), the server usually returns a simple result indicating a successful request like HTTP status code `200 OK`. However, there is no way for the client application to know the added/modified object was correctly committed to the database unless the client makes a new request for the object.

**Best Practice**

To separate the database manipulation logic from business logic, we could use the Repository pattern. To ensure a database transaction was successfully executed before returning the result to the user, we could use the [Unit of Work](https://bit.ly/3uCO9du) pattern.

![postputpatch return](https://i.imgur.com/VNorpm7.png)

The Unit of Work with ASP.NET framework was supported in a separate library called Entity Framework that works like an Object Relational Mapping (ORM) framework.

> Java Spring has a Repository pattern [built-in](https://bit.ly/3c6GFcs) with the basic CRUD operations. ASP.NET Core does not have this function built-in. Still, there is an [instruction](https://bit.ly/3uCO9du) on how to implement them. In addition, both frameworks support Dependency Injection and Unit of Work.
> 
> Sample implementations exist for [Java Spring](https://git.io/JGRWE) and [ASP.NET Core](https://git.io/JGRWg).

## 1.5. Content Negotiation
<a id="markdown-content-negotiation" name="content-negotiation"></a>

### 1.5.1. Problem
<a id="markdown-problem" name="problem"></a>

The client can only process and manipulate the resources in some formats.

### 1.5.2. Best Practice
<a id="markdown-best-practice" name="best-practice"></a>

We could use factory design patterns to initiate the object in the required format. For each set of required formats, there should be a corresponding concrete Factory (i.e., XML and JSON will have a concrete factory. PNG, JPG and Base64 will have another concrete factory).

![content negotiation](https://i.imgur.com/4BRiOFT.png)

### 1.5.3. Existing Framework Supports
<a id="markdown-existing-framework-supports" name="existing-framework-supports"></a>

Both Java Spring and ASP.NET core support content negotiation with the default set to `JSON` format. The table below compares content negotiation implementation in popular frameworks.

|                                           | **Java Spring**        | **ASP.NET core**  | **Our approach**  |
| :---------------------------------------- | :--------------------- | :---------------- | :---------------- |
|         Common media types <br>           | Yes                    | Yes               | Yes               |
|         Customizable serializer <br>      | No                     | Yes               | Yes               |
| Do not require data annotation on model   | No                     | Yes               | Yes               |
|         Built-in support ignorable <br>   | Yes                    | Yes               | N/A               |


## 1.6. List Pagination
<a id="markdown-list-pagination" name="list-pagination"></a>

**Problem**

> In the REST API system, any API endpoint that returns data type as a list should support pagination, even if the list is typically small. Developers do not know if a list will grow in the future. Adding pagination support for a list after the API was published is not a good idea since the clients already assumed they have all items, while they only have the first page.
> 
> Common Design Patterns - https://cloud.google.com/apis/design/design_patterns

**Frameworks support**

Fortunately, the popular frameworks already support pagination in some ways.

For example, Java Spring has a `PagingAndSortingRepository` interface that helps developers quickly implement pagination with the help of the `Pageable` interface.

For ASP.NET, the pagination capability was built-in with Entity Framework Core, the open-source ORM by Microsoft, using `Skip` and `Take` methods on a collection of type `IQuerytable`. The use of these method require some conversion of the parameters: `Skip(itemPerPage * pageNumber)` and `Take(itemPerPage)`. Then developers can use it as follow:

```csharp
var result = _dbContext.transaction
                       .Skip(itemPerPage * pageNumber)
                       .Take(itemPerPage);
```

For other frameworks that do not have ORM libraries, the developers could use the `LIMIT` and `OFFSET` clauses of SQL, depending on the database technology. For example, for SQL Server:

```sql
SELECT *
FROM your_table
ORDER BY column_name ASC
OFFSET number_of_skipped_row ROWS FETCH NEXT max_number_of_row ROWS ONLY;
```

# 2. Evaluation
<a id="markdown-evaluation" name="evaluation"></a>

To evaluate our best practice, we surveyed experienced developers.

The survey link can be found here:

* Part A: [https://www.surveymonkey.com/r/restapi-bestpractices-a](https://www.surveymonkey.com/r/restapi-bestpractices-a)
* Part B: [https://www.surveymonkey.com/r/restapi-bestpractices-b](https://www.surveymonkey.com/r/restapi-bestpractices-b)

The initial survey result showed that most of the answers agree with our design. We are interviewing the participants with exciting responses to understand their thought.