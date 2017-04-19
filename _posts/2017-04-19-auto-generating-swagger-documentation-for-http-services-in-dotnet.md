---
layout: post
title: "Auto-generating Swagger documentation for HTTP Services in .NET"
date: 2017-04-19
author: m.conen
description: 
categories: programming design swagger http services .net dotnet rest api documentation
---
# Auto-generating Swagger documentation for HTTP Services in .NET

While working towards a [Service Based Architecture](https://www.youtube.com/watch?v=pjN7CaGPFB4) we have been introducing HTTP APIs. [As no-one really does REST](https://devblast.com/b/calling-your-web-api-restful-youre-doing-it-wrong), we as a team had debates based on recent use cases, resulting in what we would adopt (for now) and what not. Consequently, we have been thinking how to implement this, extending on [ASP.NET Web API 2](https://msdn.microsoft.com/en-us/library/dn448365(v=vs.118).aspx).

One of the challenges with an(y) API is being explicit about how to use it. In the past decades, many hours have been spent writing manuals, documentation pages, etc. but as [entropy](https://en.wikipedia.org/wiki/Entropy_(order_and_disorder)) ever increases so does the chaos. In other words: **documentation does not have a natural tendency to stay up-to-date**.

In order to mitigate this, I have been exploring whether it is possible to automatically generate documentation. In particular documentation that is usable by [Swagger](http://swagger.io/). Of course I was not alone in this: There are open source projects like [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle) that provide this functionality too. An important part of auto-generated documentation is that it should need no to a minimal amount of maintenance. Although Swashbuckle does a great job, I felt that it has a serious shortcoming when it comes to refactoring code, and therefore inducing manual work.

In particular, the use of an attribute to declare the return type is not refactor-safe. The attribute tells the self-reflection mechanism what the intended return type of the method is, as this is typically not visible from the declared return type. The declared type is usually an `IHttpActionResult` or a `Task<IHttpActionResult>` instead. As the interface does not expose the intended return type (it wraps it), the return type could change due to a code change. Renames would probably still work (thanks [ReSharper](https://www.jetbrains.com/resharper/)!), adding and removing parameters too, but changing the type (or the number, i.e. 1 to an array) would create a mismatch between the value of the attribute and the actual return type. More importantly, this mismatch would be hard to detect (and at runtime probably).

So I looked how to overcome this and will propose that methodology to the Swashbuckle team, as it may be useful for a wider audience. **In short, I made sure the return type as well as the expected response code is explicit in the signature of a method.** 

As Web API comes with an `ApiExplorer` class out of the box—which helps a great deal understanding the nature of an API—it is fairly straightforward to detect the declared return type from an API. So the real issue is: how can the `IHttpActionResult` be more clear about the return type? Fairly straightforward actually. Implement the interface in a template class, like `ActionResult<T>` and create an `ActionResult` next to it to deal with void returns. To make the intended response code explicit, you can subclass those like: `class Ok200<T> : ActionResult<T>` or `class NoContent204 : ActionResult`.

This makes it possible to:

- Generate Swagger documentation that automatically picks up return type changes
- Be explicit about return types and response codes
- Make each endpoint respond to an OPTIONS request to get documentation of that endpoint
- Make a general endpoint for a service address (api-root-url/v1/documentation) that returns documentation on all endpoints (except itself)

So, what does this look like?

Here you can see a CRUD-type controller (only the **R**ead in this case) and note that it does not strictly need to know about the fact that it is being invoked as a HTTP service (this implementation is a bit superfluous, but that is not the point now):

```c#
public class ReadCurrencyExposuresController {
    
    private readonly IReadOnlyRepository<CurrencyExposure> _repository;

    public ReadCurrencyExposuresController(
    			IReadOnlyRepository<CurrencyExposure> repository) {
        _repository = repository;
    }

    public async Task<IEnumerable<CurrencyExposure>> Read() {
        return await _repository.GetAll();
    }
}
```
Here is the HTTP controller, with documentation functionality:

```c#
public class CurrencyExposuresController : ApiControllerWithDocumentation {
    
    private readonly ReadCurrencyExposuresController _readController;
    private readonly UpdateCurrencyExposuresController _updateController;

    public CurrencyExposuresController() {
        var repository = new CurrencyExposureRepository();
        _readController = new ReadCurrencyExposuresController(repository);
        _updateController = new UpdateCurrencyExposuresController(repository);
    }

    public async Task<Ok200<IEnumerable<CurrencyExposure>>> Get() {
        return await _readController.Read().WaitReturnOk(this);
    }

    public async Task<NoContent204> Put(
    	[FromBody] IEnumerable<CurrencyExposure> currencyExposures) {
        return await _updateController.Update(currencyExposures).WaitReturnNoContent(this);
    }
}
```
Note that the signature makes it explicit what kind of types are expected. If the `_readController.Read()` returns a different type, the `Get()` method will start to complain **at compile time** as the `WaitReturnOk` extension method will pass on the return type as is.

The `ApiControllerWithDocumentation` is straightforward:

```c#
public abstract class ApiControllerWithDocumentation : ApiController {
    [ApiExplorerSettings(IgnoreApi = true)]
    public async Task<Ok200<Documentation>> Options() 
        => await this.CreateOptionsMessage();
}
```
The documentation functionality won't reflect on itself via the `ApiExplorerSettings`.

The full documentation endpoint can easily be created:

```c#
public class DocumentationController : BaseDocumentationController {
}
```
Based on this:

```c#
[ApiExplorerSettings(IgnoreApi = true)]
public abstract class BaseDocumentationController : ApiController {
    private const bool _showAllApis = true;

    public async Task<Ok200<Documentation>> Get() {
        return await this.CreateOptionsMessage(_showAllApis);
    }
}
```
The documentation magic happens in the CreateOptionsMessage, of course.

To see if this works in Swagger:

![documentation to swagger](documentation to swagger.png)

- Step 1 shows the (all) documentation endpoint, returning a JSON object. 
- Step 2 shows that this JSON object is Swagger compatible. 
- Step 3 shows how Swagger renders this into a readable and presentable format.

In summary, this shows that it is possible to auto-generate documentation which is very much refactor proof, helping our developers to provide quality endpoints. Like!

Next step is to make a page where services can be registered (like http://apis/). In this registration process, the documentation link would be provided and would render a Swagger page. So the story continues.