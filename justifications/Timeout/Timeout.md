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
> * https://www.ibm.com/garage/method/practices/manage/practice_circuit_breaker_pattern
> * https://docs.microsoft.com/en-us/azure/architecture/patterns/circuit-breaker
> * Original idea from [Release It!](https://pragprog.com/titles/mnee2/release-it-second-edition/) book

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

Note: For this to be realized, the client must be programmed in a way that can polling the server for result. The server must store the calculated result in some kinds of database or caching memory.

## 3.4. A comparisons between 2 approaches

<table>
<thead>
  <tr>
    <th></th>
    <th>Timeout</th>
    <th>Async request-reply</th>
  </tr>
</thead>
<tbody>
  <tr>
    <td rowspan="3">Pros</td>
    <td>No requirement for client side</td>
    <td>Client and server can re-establish connection after disconnected</td>
  </tr>
  <tr>
    <td>Easier to implementation</td>
    <td>The same result can be served instantly</td>
  </tr>
  <tr>
    <td>Single system involved</td>
    <td></td>
  </tr>
  <tr>
    <td rowspan="3">Cons</td>
    <td>Risk of incorrect predefined timeout value</td>
    <td>Require implementation in both client and server side</td>
  </tr>
  <tr>
    <td></td>
    <td>Require a mechanism to store calculated data</td>
  </tr>
  <tr>
    <td colspan="2">If the client disconnect early, the server still wasting resources until timeout</td>
  </tr>
</tbody>
</table>

## 3.5. A combination of Timeout and Asynchronous Request-Reply pattern

Developer can choose to combine both approaches to maximize the benefit.

![combined](https://github.com/huntertran/concordia-thesis-topic/blob/main/out/justifications/Timeout/timeout_disconnect_early.png?raw=true)

When the client is polling for the result status, the client goes offline. The server wait for the client to poll again, and after a period of time, the server terminate the process to save server resource

# 4. Proof of concept

## 4.1. Terminate an operation in C# and dotnet

C# and dotnet core support asynchronous programming, make it easier to isolate an operation and terminate it later.

## Terminate an operation in Java

In java, starting from java 7, developer can use `ExecutorService` to execute a task that could be terminated later.

# 5. Some other ideas

WebSocket, with Ping/Pong - a fun name for HTTP Polling: https://github.com/websockets/ws#how-to-detect-and-close-broken-connections
