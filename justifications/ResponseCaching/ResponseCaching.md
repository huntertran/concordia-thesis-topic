Response Caching
---

> Some resources:
> * [Response Caching in ASP.NET Core](https://docs.microsoft.com/en-us/aspnet/core/performance/caching/response?view=aspnetcore-5.0)

# 1. Constrains

* Caching can happened on both server side and client side
* There must be an expire time for the cache (`Expires` header)
* There must be a mechanism to request the new data (`Cache-Control: no-cache` header)
* `GET` requests should be cachable
* `POST` request should not be cached

# 2. Implementations

## 2.1. Choose the layer to implement caching

![layers](https://github.com/huntertran/concordia-thesis-topic/blob/main/out/justifications/ResponseCaching/layers.png?raw=true)

The caching mechanism can be implemented in any of these layers. Usually, developer choose to implement caching using a Proxy Server for easy scaling, separate of concern and easy management.

> DNS and CDN are already implemented caching

## 2.2. Caching mechanism

**`Cache-Control` header directives**

| Directive | Action |
|-|-|
| public | A cache may store the response. |
| private | The response must not be stored by a shared cache. A private cache may store and reuse the response. |
| max-age | The client doesn't accept a response whose age is greater than the specified number of seconds. Examples: max-age=60 (60 seconds), max-age=2592000 (1 month) |
| no-cache | On requests: A cache must not use a stored response to satisfy the request. The origin server regenerates the response for the client, and the middleware updates the stored response in its cache.<br><br>On responses: The response must not be used for a subsequent request without validation on the origin server. |
| no-store | On requests: A cache must not store the request.<br><br>On responses: A cache must not store any part of the response. |

> Source: https://docs.microsoft.com/en-us/aspnet/core/performance/caching/response?view=aspnetcore-5.0

**Other related headers**

| Header | Function |
|-|-|
| Age | An estimate of the amount of time in seconds since the response was generated or successfully validated at the origin server. |
| Expires | The time after which the response is considered stale. |
| Pragma | Exists for backwards compatibility with HTTP/1.0 caches for setting no-cache behavior. If the Cache-Control header is present, the Pragma header is ignored. |
| Vary | Specifies that a cached response must not be sent unless all of the Vary header fields match in both the cached response's original request and the new request. |
| Etag | An identifier for a specific version of the resource. Cache manager can use this identifier to see if the resource is updated or not. <br>`W/` can be use to indicate the server to use [weak validator](https://developer.mozilla.org/en-US/docs/Web/HTTP/Conditional_requests#weak_validation) |


## 2.3. Server side caching

1. Direct caching in application (In memory caching - not recommended)

The caching component is placed directly in the application (aka running on the same host as the application). This approach is simpler to implement, but was not recommended since it could slow down the application and difficult to scale.

2. Use a proxy server for caching (Distributed Caching)

Using a proxy server is more complicated in term of implementation and deployment. This approach is easy to scale, and developers could choose / swap multiple caching technology base on their need.

## 2.4. Implementations

Most caching libraries work as a dictionary with key and value pairs. On first request, a new pair added to the dictionary. On the repeated requests, the value from the entry was return.

To implement serverside caching capability, we need:
* A cache key
* Serialized data to be cached
* A cache manager that can:
    * Check the cache entry
    * Update the cache with new data

1. Java `spring framework`

The Java `Spring Framework` applied multiple design patterns for Caching feature:

**Cache Manager**

![CacheManager](https://github.com/huntertran/concordia-thesis-topic/blob/main/out/justifications/ResponseCaching/SpringCacheManager.png?raw=true)

* Proxy design pattern:
    * All the adapted caching solutions will support the same built-in awareness of Spring-managed transactions.
    * User can switch to use any implementation of the CacheManager without changing the underline algorithm for spring-managed transaction

**Cache Operation**

![CacheOperation](https://github.com/huntertran/concordia-thesis-topic/blob/main/out/justifications/ResponseCaching/CacheOperationSpringFramework.png?raw=true)

* Strategy design pattern
    * Each cache operations will handle the transaction differently
    * The design pattern was modified with an abstract class between the client of the strategy and the actual invoker. This was to implement some pre-conditions before invoke the corresponding strategy.

* Builder design pattern
    * Because each cache operation is the same in structure, but different in algorithm

## 2.5. Client side caching

When the server appropriately implements caching mechanism, the client can start using the headers listed above to request a resource to be cached. In addition, the browser could also cache some unlikely to change resources like javascripts and css libraries, images, non-html files, etc.

One common pratice to force replace the cached resource is to send the request with a new timestamp parameter.