Timeout - No timeout
---

OR

**Long running operation can be cancelled**

Some resources:

> https://docs.microsoft.com/en-us/azure/architecture/patterns/async-request-reply
> 
> https://stackoverflow.com/questions/49704708/what-is-a-connection-timeout-during-a-http-request
> 
> https://www.brendanlong.com/http-timeout-header-for-requesting-resources-from-the-future.html - Solve media streaming latency problem

# 1. Problems

When there is a long running operation on the server, the client need to wait to receive the response. In this case, there was multiple potential problems that can happened:
* The client and the server disconnected
    * The operation continue to run on the server, but the result was not needed anymore
* The client cancel or abandon the request, wasting the server resource

> Definition of long running transaction:

> * [Microsoft](https://github.com/Microsoft/api-guidelines/blob/vNext/Guidelines.md#13-long-running-operations)

# 2. Constrain

* The long-running operation transaction should have a mechanism to be cancelled in case of the result was no longer needed.

# 3. Approaches

## 3.1. Circuit Breaker Pattern

Not really, this pattern was designed to prevent repeated call to an operation that is likely to fail and usually employed in microservices architecture

> More information:
> * https://www.ibm.com/garage/method/practices/manage/> practice_circuit_breaker_pattern
> * https://docs.microsoft.com/en-us/azure/architecture/patterns/circuit-breaker
> * Original idea from [Release It!](https://pragprog.com/titles/mnee2/> release-it-second-edition/) book

## 3.2. Timeout

The long running operation will have a timeout predefined by the developer. When the time is up, the operation will be cancelled.

## 3.3. Asynchronous Request-Reply pattern (HTTP Polling)

This pattern will require some extra work from the client

1. When the client send the request that trigger a long running operation, the server will response as quick as possible with `202 Accepted` HTTP code.
2. The server continue it long running operation, while the client periodically polling for the result
3. When the result available, the polling will return with `302 Found` HTTP code with the URI for the result
4. The client send a `GET` request to get the result

![diagram](https://docs.microsoft.com/en-us/azure/architecture/patterns/_images/async-request.png)

Source: https://docs.microsoft.com/en-us/azure/architecture/patterns/async-request-reply