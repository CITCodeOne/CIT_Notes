This document details the process of building an ASP.NET web service, covering core concepts such as controllers, routing, HTTP verbs (GET, POST, DELETE), handling request/response, dependency injection, and lifetime management. It emphasizes ASP.NET's convention-over-configuration approach and its built-in abstractions for common web service tasks.

### Project Structure
• Solution contains two projects:
	◦ DataServiceLayer: Simple data service using static data; focus is not on data persistence here.
		‣ Provides methods and an interface (IDataService) for data operations (e.g., GetCategories).
	◦ WebServiceLayer: The main focus for today, contains the ASP.NET web service.

### Web Service Initialization
1. Program.cs: Contains the Main method, service registration, and middleware configuration.
2. Launch Settings: launchSettings.json specifies the port the service listens on.
3. HTTPS can be enabled but is not used in this demonstration for simplicity.

### Controllers
Definition: For a class to act as a controller in ASP.NET, it must:
1. Inherit from ControllerBase: Provides core API controller functionalities.
2. Specify a Route: Uses `[Route("api/[controller]")]` attribute or similar. `[controller]` is a placeholder for the controller's name (e.g., CategoriesController maps to api/Categories).
3. Use `[ApiController]` attribute: Helps ASP.NET interpret requests, improves model binding, and enables automatic HTTP 400 responses for validation errors. Example: Basic MyController

Route: api/mine (specified by `[Route("api/mine")]`).
1. Method Resolution:
	1. Initially, with only one string Hello() method, ASP.NET magically routes GET api/mine to this method due to convention.
	2. If multiple methods exist (e.g., Hello() and Hey()), ASP.NET becomes confused and returns an error (e.g., "I don't know which method to call now").
	3. Solution: Use HTTP verb attributes (e.g., `[HttpGet]`) to explicitly map methods to HTTP GET requests.
		‣ Example: `[HttpGet] public string Hello() { ... }`

### HTTP GET Operations
1. Getting All Categories
	1. Controller: CategoriesController
	2. Route: api/Categories
	3. Method: `public IEnumerable<Category> GetCategories()`
	4. Implementation: 
		1. Accesses DataService (initially via direct instantiation, later via Dependency Injection). 
		2. Calls `dataService.GetCategories()`.
		3. Default Behavior: ASP.NET automatically serializes returned objects (e.g., `IEnumerable<Category>`) to JSON by default.
2. • Getting Category by ID:
	1. Method: public Category GetCategory(int id)
	2. Route Specification: Use `[HttpGet("{id}")]` attribute.
		1. The `{id}` in the URL path is mapped to the id parameter of the method.
		2. Example request: `GET api/Categories/3`

### Handling Not Found (404)
Problem: If `GetCategory(id)` returns null for a non-existent ID, the server returns a 204 No Content status, which is not ideal for "not found" scenarios.

Solution: Return IActionResult instead of the raw object.
1. `IActionResult` provides helper methods for common HTTP responses.
2. Logic:
```C#
public IActionResult GetCategory(int id) {     
	var category = _dataService.GetCategory(id);     
	if (category == null) {         
	return NotFound(); } // Returns 404 Not Found          
	return Ok(category); // Returns 200 OK with the object 
	}
```
3. `Ok()` returns 200 OK.
4. `NotFound()` returns 404 Not Found.
5. Other options: `NoContent() (204)`, `Created() (201)`, etc.

### HTTP POST Operations (Creating Resources)
Method: `[HttpPost] public IActionResult CreateCategory([FromBody] CreateCategoryModel model)`

Input Model:
1. Define a separate model (e.g., `CreateCategoryModel)` for client input.
2. This model should contain only the necessary fields for creation (e.g., Name, Description), not the ID (as the server generates it).
3. Snippet: prop + Tab for quick property creation.
4. Usually placed in a Models folder.

Automatic Model Binding:
1. ASP.NET magically maps the JSON body of the request to the `CreateCategoryModel` object in the method parameter.
2. It looks for properties in the request body that match the input model's properties (e.g., name and description).
3. Can be explicitly hinted with attributes like `[FromBody], [FromQuery], [FromRoute]`, etc., to specify where the data should come from.

Return Value:
1. Typically Created() (201 status code) when a resource is successfully created.
2. Example: `return Created("url/to/new/resource", newCategory);` (Though `Created()` without a URI is shown here initially).

### HTTP DELETE Operations
1. Method: `[HttpDelete("{id}")] public IActionResult DeleteCategory(int id)`
2. Logic:
	1. Call `_dataService.DeleteCategory(id).`
	2. If deletion is successful, `return NoContent() (204)`.
	3. If the item is not found, `return NotFound() (404)`.

### API Testing Tools
Postman: A popular external tool for making HTTP requests.
1. Visual Studio's HTTP File (.http):
	1. Allows defining HTTP requests directly within Visual Studio.
	2. Integrates with the IDE, making development workflow smoother.
	3. Can also be used with VS Code and other IDEs via extensions (e.g., Rest Client by Huachao Mao).

### Lifetime Management & Dependency Injection (DI)
##### Initial Problem: Object Lifetime
Scenario: Instantiating `DataService directly inside the controller (_dataService = new DataService();).`

Issue: ASP.NET creates a new controller for each incoming request. This means a new `DataService` instance is created each time, losing any data added in previous requests if the `DataService` stores data in memory.

Solution Attempt (Not Ideal): Make DataService a static property in Program.cs.
1. public static IDataService DataService { get; } = new DataService();
2. This ensures only one instance of DataService throughout the application's lifetime, but it ties the controller to a specific static implementation and is not scalable.

##### Preferred Solution: Dependency Injection (DI)
Concept: Inversion of Control (IoC). The framework (ASP.NET) controls the instantiation of dependencies, not the developer directly within the consumer class.

Steps for DI:
1. Define an Interface: Ensure the dependency (e.g., DataService) implements an interface (IDataService).
2. Controller Constructor Injection:
	Modify the controller's constructor to request the interface as a parameter.

```C#
public class CategoriesController : ControllerBase 
{     
	private readonly IDataService _dataService; 
	public CategoriesController(IDataService dataService) {
		_dataService = dataService;     
	}     // ... methods use _dataService 
}
```

3. Register Services in Program.cs:
	1. Tell ASP.NET how to create instances for registered interfaces.
		`builder.Services.AddSingleton<IDataService, DataService>();`
4. Lifetime Options:
	1. `AddSingleton<TInterface, TImplementation>():` One instance created for the entire application lifetime. Subsequent requests get the same instance. Suitable for services without mutable state or those that should be globally shared (like the in-memory DataService here).
	2. `AddScoped<TInterface, TImplementation>():` One instance per client request (or "scope"). Useful for services that need to maintain state within a single HTTP request, but not across requests (e.g., database contexts in a web request).
	3. `AddTransient<TInterface, TImplementation>():` A new instance is created every time it's requested. Useful for lightweight, stateless services.

Benefits of DI:
1. Loose Coupling: Controllers depend on interfaces, not concrete implementations.
2. Testability: Easier to mock dependencies for unit testing.
3. Maintainability: Changes to implementation don't require changes in consuming classes.

### ASP.NET Built-in Services
ASP.NET pre-registers many services for injection (e.g., `ILinkGenerator, IConfiguration`).

Developers can request these directly in constructors without explicit registration in Program.cs.







































