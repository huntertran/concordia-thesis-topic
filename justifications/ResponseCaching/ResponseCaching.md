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

## Choose the layer to implement caching

![layers](https://github.com/huntertran/concordia-thesis-topic/blob/main/out/justifications/ResponseCaching/layers.png?raw=true)



## 2.1. Server side caching

### 2.1.1. Caching mechanism



### 2.1.2. Direct caching in application (not recommended)

### 2.1.3. Use a proxy server for caching

## 2.2. Client side caching 