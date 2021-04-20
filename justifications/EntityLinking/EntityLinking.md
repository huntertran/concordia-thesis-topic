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

To address this problem, we could use [Visitor pattern](https://en.wikipedia.org/wiki/Visitor_pattern)

