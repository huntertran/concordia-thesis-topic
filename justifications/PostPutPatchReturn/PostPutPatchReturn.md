POST-PUT-PATCH should return the modified object
------------------------------------------------

# 1. Problem

When the client send a request to modified the database(Add/Modify an object), usually, the server only return a simple result like HTTP status code `200 OK`. There is no way for the client to know the added/modified object was correctly committed to the database unless the client make a new request to the resource.

# 2. Pattern

The request that create an object should return the successfully created object. The request that update an object should return the updated data.

This is to confirm with the caller the successful operation, rather than a simple `200 OK` status code, but not sure which data was created/updated to the database.

# 3. Constrains

* There should be a mechanism for the client to control the response in case the client only need a simple `200 OK` return.
* Minimize the request to the database

# 4. Approaches

## 4.1. POST - PUT - PATCH usage

<img src="POST.svg"/>

`POST` = create

Create a new object. The client should not know the ID of the object. The server (or the database) will handling ID creation

<img src="PUT-CREATE.svg"/>

<img src="PUT-UPDATE.svg"/>

`PUT` = create or update the whole object (caller should know the ID of the object)

<img src="PATCH.svg"/>

`PATCH` = update part of the object

Most of the database return the number of changed rows as the result of `UPDATE` or `INSERT`

## 4.2. General architecture

To split the logic for database manipulation in a RESTful API service, we could use `Repository` pattern, in combination of `Unit of Work` pattern and `Dependency Injection`.

For each resource, or group of related resources, there will be a repository class that handle CRUD operations. This class will be injected into Controllers using dependency injection.

<img src="repository.svg"/>

The repository classes will handle database operations. For `CREATE` and `UPDATE` operation, the repository should implement an extra check to determine if it should return the newly created/modified object or not.

## 4.3. dotnet with Entity Framework Core

The following implementation will take `Student` as the object to create the repository

In dotnet, the server connect with database using Entity Framework, an open source Object-Relational Mapper (ORM).

For creating new object from scratch, at the end, if the server want to return the newly created object, it has to query the object.

The sequence diagram for creating new object

<img src="post_put_patch_return.svg"/>

Demo source code

```csharp
public class DemoController : ControllerBase
{
    private readonly MyDbContext _context;

    public DemoController(MyDbContext context)
    {
        _context = context;
    }

    [HttpPost]
    public async Task<IActionResult> Create(DemoModel model, bool isReturnObject = false)
    {
        _context.DemoModels.Add(model);
        var isSuccess = await _context.SaveChangesAsync();

        if(isSuccess)
        {
            if(isReturnObject)
            {
                var returnObject = await _context.DemoModels.SingleOrDefaultAsync(m => m.Id == model.Id);

                return Ok(returnObject);
            }
            else
            {
                return Ok();
            }
        }
        else
        {
            return new HttpStatusCode(500);
        }
    }
}
```

Entity Framework use object tracking for `UPDATE`, so only the changed part of the object will be translate into `sql` command and send to the database.

## 4.4. java spring boot with MySql server and MySQL Connector/J
