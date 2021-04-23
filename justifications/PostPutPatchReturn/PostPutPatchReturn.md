POST-PUT-PATCH should return the modified object
---

# 1. Problem

When the client send a request to modified the database(Add/Modify an object), usually, the server only return a simple result like HTTP status code `200 OK`. There is no way for the client to know the added/modified object was correctly committed to the database unless the client make a new request to the resource.

# 2. Pattern

The request that create an object should return the successfully created object. The request that update an object should return the updated data.

This is to confirm with the caller the successful operation, rather than a simple `200 OK` status code, but not sure which data was created/updated to the database.

# 3. Constrains

* There should be a mechanism for the client to control the response in case the client only need a simple `200 OK` return.
* Minimize the request to the database

# 4. Approaches

## 4.1. POST - PUT - PATCH



`POST` = create

`PUT` = create or update the whole object (caller should know the ID of the object)

`PATCH` = update part of the object



## 4.2. dotnet with Entity Framework Core

## 4.3. java spring boot with MySql server and MySQL Connector/J