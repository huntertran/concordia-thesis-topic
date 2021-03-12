Content Negotiation (Ignore MIME type)
---

# 1. Constrains

* The resources should be served in various format (json, xml, file, base64)
* The developer should set a default format in case the caller doesn't specify the requested format.
* The format preparation implementations should be easily modified in the feature

# 2. Approach

Based on the request header, the server prepare the data in requested format, then return.

# 3. Implementation

Use factory design patterns to intiate the object in required format.

Class diagram:

![simple implementation](https://github.com/huntertran/concordia-thesis-topic/blob/main/out/justifications/ContentNegotiation/Content%20Negotiation.png?raw=true)

Source code:

Abstract class for all formats of object:

```java
public abstract class BaseObject {
    public abstract serialize();
}
```

Classes for object with preferred format:

```java
import BaseObject;

public class XmlObject extends BaseObject {
    @Override
    public String serialize() {
        String result = "";
        // XML serialization logic
        return result;
    }
}

public class JsonObject extends BaseObject {
    @Override
    public String serialize() {
        String result = "";
        // JSON serialization logic
        return result;
    }
}
```

Factory class:

```java

import BaseObject;
import XmlObject;
import JsonObject;

public class ObjectFactory {
    public static BaseObject getObject(String type) {
        switch(type) {
            case "xml": {
                return new XmlObject();
            }
            case "json":
            default: {
                return new JsonObject();
            }
        }
    }
}
```

Usage:

```java
import ObjectFactory;
import BaseObject;

public class Controller {
    public String requestHandler(String requestedMIMEType) {
        BaseObject result = ObjectFactory.getObject(requestedMIMEType);
        // compute the result
        return result.serialize();
    }
}
```