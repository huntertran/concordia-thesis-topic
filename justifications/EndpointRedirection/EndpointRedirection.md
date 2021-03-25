End-point Redirection
---

# 1. The pattern

Server send a new location with status code:
* 301 Moved Permanently
* 302 Found
* 307 Temporary Redirect
* 308 Permanent Redirect

# 2. Constrains

* The new location should be automatically generated
* The HTTP Code should be automatatically generated

# 3. Approach

Use the example of the API Versioning, the moved resource is the obsoleted API (aka the old version of the API)

## 3.1. Inherit from a Redirector class

The redirection logics were separated to `V1Redirector` class. The `V2Logic` class will extends this class.

