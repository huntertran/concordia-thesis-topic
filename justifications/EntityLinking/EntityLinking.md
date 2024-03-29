Entity Linking
---

# 1. The pattern

When requesting a resource, server should include all the link to the related actions and resource that the client are able to do.

For example, and API for a blog:

```
GET https://localhost/posts/123
Accept: application/json
```

In case of `Entity Linking`, the server should check if the client is correctly loggin, then return the following result.

```json
{
    "post": {
        "title": "Lorem ipsum",
        "content": "Lorem ipsum",
        "posted_on": 123456789,
        "links": [{
            "rel": "comment",
            "method": "post",
            "uri": "/post/123/comment"
        },{
            "rel": "like",
            "method": "get",
            "uri": "/post/123/like"
        }]
    }
}
```

If the client was not logging in, the server should not include the link for comment.

```json
{
    "post": {
        "title": "Lorem ipsum",
        "content": "Lorem ipsum",
        "posted_on": 123456789,
        "links": [{
            "rel": "like",
            "method": "get",
            "uri": "/post/123/like"
        }]
    }
}
```

# 2. Constrains

- The current action should have access to the instance of other controller that contain the related resource.
- The current action should have access to class and method information (name, annotation, public and private variable, etc.)

# 3. Approach

Most of the web framework have annotations to decorates classes and methods to construct the RESTful API URIs. The information putted into the source code itself, not the data at runtime. To address this problem, we could use a modified version of [Visitor pattern](https://en.wikipedia.org/wiki/Visitor_pattern) with language reflection features to access these information.

![visitor pattern](https://github.com/huntertran/concordia-thesis-topic/blob/main/out/justifications/EntityLinking/entity_linking.png?raw=true)

In the above diagram:
* `RelatedResource1` and `RelatedResource2` are other controllers that contain the need resources (a, b, c and d fields, or methods with annotations).
* `LogicABResourceVisitor` and `LogicCDResourceVisitor` are the visitor implementation for each type of resources. Separation by resources type allow developer separate the logic to select the needed resources.
* Abstract class `CommonResourceVisitor` contains the complicated logic for language reflection. By extends this abstract class, the visitor will be more simpler and easier to use (hide the complexity of the language reflection). This could be realize because the web frameworks employ a coding conventions for classes and method annotations, as well as methods name to construct the URIs.

By "visiting" these controllers, `Client` can access the class information and extract the needed data.

## 3.1. Java Spring boot

Java Spring boot framework use custom Java annotation `GetMapping`, `PostMapping`, etc. to define the URI and method for handling client request.

We could use `ClassName.class.getMethods()` to get all method of a class, then `method.getAnnotation()` to get the annotations of a method. More specific in implementation

## 3.2. dotnet core

asp.net core use `HttpMethodAttribute` annotation like `HttpPost`, `HttpGet`, etc. to define the Http verb. For the URI, asp.net core use auto mapping with the name convention of the controller and method.

We could use `class.GetType().GetMethods(filter enum)` to get the methods information, then `method.GetCustomAttribute<AttributeClass>()` to get the annotation. More specific in implementation.

# 4. Implementation

We include implementations for dotnet core [here](https://github.com/huntertran/pattern-abiding-api/blob/main/dotnet/Controllers/EntityLinkedController.cs) and Java Spring boot [here](https://github.com/huntertran/pattern-abiding-api/blob/main/javaspring/src/main/java/patterns/sample/controllers/EntityLinkedController.java)