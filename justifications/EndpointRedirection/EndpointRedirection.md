## End-point Redirection

# 1. The pattern

Server send a new location with status code:

-   301 Moved Permanently
-   302 Found
-   307 Temporary Redirect
-   308 Permanent Redirect

# 2. Constrains

-   The new location should be automatically generated
-   The HTTP Code should be automatatically generated

# 3. Approach

Use the example of the API Versioning, the moved resource is the obsoleted API (aka the old version of the API)

## 3.1. Extend from a Redirector class

The redirection logics were separated to `V1Redirector` class. The `V2Logic` class will extends this class.

![Extend class](https://github.com/huntertran/concordia-thesis-topic/blob/main/out/justifications/EndpointRedirection/EndpointRedirectionExtendClass.png?raw=true)

## 3.2. Nested class to remove inheritance

The redirection logics were separated to `V1Redirector` class. This class will be nested inside the `V2Logic` class. This will allow the redirector access methods in V2Logic, useful for reflection to generate the URI.

![Nested class](https://github.com/huntertran/concordia-thesis-topic/blob/main/out/justifications/EndpointRedirection/EndpointRedirectionNestedClass.png?raw=true)

## 3.3. Comparison

|  | Extend | Nested |
|-|-|-|
| **Pros** | Redirection logic were separated in class and file | Allow the outer class (the main class) be inherited from another class |
|  | Easier to implementation.<br>Can be implemented in multiple languages. | The Redirector has access to resources in the main class |
| **Cons** | The Redirector cannot access resources in the main class | Redirection logics are coded inside the main class |
|  | The main class cannot inherit from other class<br>(if the language didn't support multiple inheritance) | Not all languages support nested class |

# 4. Implementation

## 4.1. Common

Interface for V1 API

```java
package v1;

public interface IV1Logic {
    String actionA();
    String actionB();
}
```

V1 API implementation

```java
package v1;

public class V1Logic implements IV1Logic {
    public String actionA() {
        // implementation
    }

    public String actionB() {
        // implementation
    }
}
```

## 4.2. Separated Redirector

Redirector class

```java
package v2;

import v1.IV1Logic;

public class V1Redirector implements IV1Logic {
    public String actionA() {
            // REDIRECT LOGIC
            // return 3xx code with new location
        }

        public String actionB() {
            // REDIRECT LOGIC
            // return 3xx code with new location
        }
}
```

Main V2 logic class

```java
package v2;

public class V2Logic extends V1Redirector {
    public String actionC() {
        // implementation
    }

    public String actionD() {
        // implementation
    }
}
```

## 4.3. Nested Redirector

V1 API Redirector

```java
package v2;

import v1.IV1Logic;

public class V2Logic implements IV1Logic {

    private V1Redirector redirector = new V1Redirector();

    public String actionA() {
        return redirector.actionA();
    }

    public String actionB() {
        return redirector.actionB();
    }

    public String actionC() {
        // implementation
    }

    public String actionD() {
        // implementation
    }

    public class V1Redirector implements IV1Logic {
        public String actionA() {
            // REDIRECT LOGIC
            // return 3xx code with new location
            URI uri = new URI();
            uri =
        }

        public String actionB() {
            // REDIRECT LOGIC
        }
    }
}
```
