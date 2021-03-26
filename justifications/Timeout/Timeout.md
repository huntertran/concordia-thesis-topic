Timeout - No timeout
---

Some resources:

> https://docs.microsoft.com/en-us/azure/architecture/patterns/async-request-reply
> 
> https://stackoverflow.com/questions/49704708/what-is-a-connection-timeout-during-a-http-request
> 
> https://www.brendanlong.com/http-timeout-header-for-requesting-resources-from-the-future.html - Solve media streaming latency problem

# 1. Constrain

* Each transaction should have a time out value. The client cannot wait forever for a transaction to be complete.
* what happen on the server when the transaction exceed timeout value?
    * store data in cache
    * log warning to developer
    * apply the `Asynchronous Request-Reply pattern` by Microsoft

Definition of long running transaction:

* [Microsoft](https://github.com/Microsoft/api-guidelines/blob/vNext/Guidelines.md#13-long-running-operations)


# 2. Approach

## 2.1. Server-side timeout (Request timeout)

We could use pipe and filter architecture to implement a default timeout to every response. The developer has the choice to modify the timeout for specific response.

### 2.1.1. Middleware in asp.net

In `asp.net core` web programming framework, the request - response already applied pipe and filter architecture.

![pipelines](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/middleware/index/_static/request-delegate-pipeline.png?view=aspnetcore-5.0)

> source: [https://docs.microsoft.com/en-us/aspnet/core/fundamentals/middleware/?view=aspnetcore-5.0](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/middleware/?view=aspnetcore-5.0)

To implement a default timeout in asp.net core, simply create a new middleware that add timeout header to response.

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app)
    {
        app.Use(async (context, next) =>
        {
            // Do work that doesn't write to the Response.
            await next.Invoke();
            // Do logging or other work that doesn't write to the Response.
        });

        app.Run(async context =>
        {
            await context.Response.WriteAsync("Hello from 2nd delegate.");
        });
    }
}
```

### Java Spring Interceptor

> https://www.journaldev.com/2676/spring-mvc-interceptor-example-handlerinterceptor-handlerinterceptoradapter