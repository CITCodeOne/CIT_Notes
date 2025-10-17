Web Service Communication Models

#### Early Approaches & Evolution
##### Plain Old XML (POX)/(Plain Old HTTP (POH)
1. Pox refers to simply passing XML back and forth. All information for interpretation is within the XML.
2. Considered "level zero" on a sub-memory region. 
3. Basically uses the protocol for moving inforomation

###### SOAP Protocol
1. A protocol for how to pass information into XML
2. Concepts: *envelop, header, body, operations*
3. Uses XML schema for information transfer.
4. **Protocol Creation**: Thousands of protocols are mad, but few gain widespread adoption (e.g. HTTP).

###### Remote Procedure Call (RPC)
1. **Concept**: Client asks a bigger computer (server) to do some work (e.g., math) Binary communication.
2. **Components**: *Proxy* on one end, *stub* on the other end to transform information.
3. **Limitations (Binary communication)**:
	1. Requires same programming language on both ends.
	2. Requires same platform due to *endianness* (byte order for multi-byte numbers).
	3. Difficult string representation (e.g. C null-terminated vs objects with lenght).
	4. Fast but highly dependent and low-level.

### REST (Representational State Transfer)

**Definition**: An architectural style (not a protocol or standard) prposed by Roy Fielding (1990s PhD thesis).

**Core Idea**: Focuses on *resources* as key concepts, represented by URLs.
1. Resources are real-world entities (people, products, orders).
2. Operations are performed *on* these resources, not by calling operations *with* parameters (like RPC).

**Protocol Agnostic**: Can be implemented over various protocols, but most commonly uses HTTP.

**Contrast with SOAP**: REST uses HTTP protocol capabilities for manipulation, whereas SOAP wraps everything in XML envelopes.

###### REST Constraints 
1. Client-Server: Clear separation of concerns between client and server. 
2. Stateless:
	1. Each request is independent; server has no memory of previous requests.
	2. Session Management: Achieved by the client sending state information (e.g.g *tokens* for authentication) with each request. Server validates the token for access.
3. Cacheable: Server can decide to cache information to respond more quickly to subsequent requests. 
4. Uniform Interface
	1. Principle: Consistent structure and interaction methods for all resources. 
	2. Identification of Resources: Resources (e.g., categories, products) are identified by URLs.
	3. Manipulation Through Representations: Resources are manipulated via their representations (e.g., JSON, XML, HTML, plain text). Clients can request specific representations. 
	4. Self-Descriptive Messages: information should contain enough knowledge to be understood and used without prior knowledge of server's internal structure (e.g., send URL instead of just an ID).
	5. Hypermedia as the engine Application State (HATEOAS): The concept of providing links within resource representations to guide client interactions (e.g., links to delte or update an object within the object itself). This makes the client less dependent on server knowledge.
5. Layered System: Client does not need to know about internal server layers; abstraction hides implementation details (e.g., database structure).
6. Code-On-Demand (optional): Servers can provide executable code (e.g., JavaScript) to extend client functionality.

#### ASP.NET Core Web Services
###### ASP.NET Core Framework
Evolution: From a "huge package" (ASP.NET before Core) to a minimal, modular approach.

**Advantages of Core:**
1. Minimal core, add components as needed.
2. Faster runtime performance.
3. Cross-platform compatibility.
4. Faster development cycle.

**Services & Middleware:**
1. Services: Components providing specific functionalities (e.g., MVC, authentication) that are configured at startup.
2. Middleware: A design pattern for extending functionality by plugging components into the request-response pipeline.
	1. Order Matters: Middleware pieces are executed sequentially.
	2. Flow: Request -> Middleware 1 -> Middleware 2 -> ... -> Controller (or terminal middleware) -> Response (back through middleware in reverse).
	3. hort-circuiting: Middleware can stop the pipeline and return a response immediately (e.g., authentication failing).
	4. Examples: Logging (input & output), Authentication (checking tokens), Authorization.
	5. Implementation: Typically a class with a specific function (can be demonstrated via videos).


###### MVC (Model-View-Controller) Pattern
**Architectural Structure:** Separates concerns, especially useful for UIs.

**Components:**
1. Model: The data part (e.g., categories, products).
2. View: The representation shown to the user. For a web API, this is often the JSON or XML output.
3. Controller: Handles user interactions/requests (e.g., HTTP requests), processes them, and prepares the view.

**ASP.NET Core**: Supports MVC for web APIs. Also offers a "minimal interface" without
controllers, but MVC offers encapsulation benefits.

###### Routing
**Function:** Determines which controller and method (action) should handle an incoming HTTP request.

**Default Routing**: Sets up routes for controllers and actions, typically used for HTML pages
(e.g., default controller/action if none specified).

**Attribute-Based Routing (for APIs):**
1. Method: Decorate controllers and methods with attributes specifying the URL path and HTTP verb they handle.
2. Example: [HttpGet("api/categories/{id}")] on a method.
3. Process: Router matches request to controller, then to a specific method based on HTTP verb.

###### Dependency Injection (DI)
**Concept:** A design pattern to reduce strong coupling between classes by "injecting"
dependencies into a class rather than the class creating them internally (avoiding new keyword within the class).

**Benefits:**
1. Increased Testability: Allows mocking/stubbing dependencies during testing (e.g., using a dummy data service instead of a real database connection).
2. Reduced Coupling: Classes depend on interfaces, not concrete implementations.
3. Flexibility: Easier to swap implementations (e.g., different database providers).
4. Simplified Construction: If a dependency has complex construction requirements, the DI container handles it.

I**OC Container (Inversion of Control Container)**: A system (built into ASP.NET Core) that knows how to create objects and resolve their dependencies automatically.

#### RESTful API Design Principles
##### Out-of-Facing Contract
The external interface of the web service defines the contract with clients.

**Comprises:** Resource identifiers, HTTP methods, and representation (media type).

**Immutability:** Once deployed, this contract is difficult to change without impacting clients
(versioning is a solution).

**Decoupling:** The out-of-facing contract is not necessarily the internal entity or domain model; there should be a mapping. This allows internal changes without affecting the API contract.

##### Resource Naming
1. **Nouns, not Operations**: Resources should be named as things (e.g., /categories, /products), not operations (e.g., API/getCategories).
2. **HTTP Verbs for Operations:** GET, PUT, POST, DELETE define the operation on the resource.
3. **Hierarchy:** Use hierarchical URLs to represent relationships (e.g., /products/{productId}/categories).
4. **Consistency:** Maintain uniform naming conventions across all resources for ease of client
5. understanding.
6. **Operations on Resources:** Use query parameters for filtering, sorting, or other actions that modify the representation or selection of a resource, not the resource's identity itself (e.g., /categories?sortBy=name).
7. **Resource Identifiers:**
	1. Often database IDs (e.g., /categories/1).
	2. Can use GUIDs (Globally Unique Identifiers) for increased uniqueness and decoupling from
	3. internal database structure.
	4. Client vs. Server-Provided IDs: Generally, clients should not provide IDs for new objects; the server should generate them and return the newly created object with its ID.


##### HTTP Verbs and Their Properties
1. CRUD Operations:
	1. Create: POST
	2. Read: GET, HEAD
	3. Update: PUT, PATCH
	4. Delete: DELETE
2. Properties:
	1. Safe: An operation is safe if it does not change the state of the server (e.g., GET, HEAD).
	2. Idempotent: An operation is idempotent if calling it multiple times has the same effect as calling it once (e.g., GET, PUT, DELETE, HEAD).
		1. GET: Safe and Idempotent. Returns same data (assuming no external changes).
		2. HEAD: Like GET, but only returns headers, no body. Safe and Idempotent.
		3. POST: Not safe (changes state), not idempotent (multiple posts create multiple new resources).
		4. PUT: Not safe (changes state), idempotent (overwrites the resource; multiple calls result in the same final state). Requires sending the full object.
		5. DELETE: Not safe (changes state), idempotent (deletes once, subsequent calls don't change state, might return "not found").
		6. PATCH: Not safe (changes state), not idempotent (manipulates sub-parts; might not produce the same result if applied multiple times without considering the starting state). More complex, but efficient for partial updates.

###### HTTP Status Codes
1. Purpose: Indicate the outcome of an HTTP request.
2. Ranges:
	1. 1xx (Informational)
	2. 2xx (Success):
		1. 200 OK: General success.
		2. 201 Created: Resource successfully created (often returned after POST).
		3. 204 No Content: Successful request, but no content to return (e.g., DELETE).
	5. 3xx (Redirection)
	6. 4xx (Client Error): User made a mistake.
		1. 400 Bad Request: Invalid request data.
		2. 401 Unauthorized: Authentication failed.
		3. 403 Forbidden: Authenticated, but not authorized for the resource.
		4. 404 Not Found: Resource does not exist.
	7. 5xx (Server Error): Server-side problem.
		1. 500 Internal Server Error: Generic server error.
3. Reason Phrase: A small readable message accompanying the status code (e.g., "OK" for 200), primarily for human readability, status code is for programmatic use.

##### URL Structure
1. Components: Scheme://Host:Port/Path?Query#Fragment
	1. Scheme: e.g., http, https.
	2. Host: Server name (e.g., www.example.com).
	3. Port: Server port (e.g., 8080).
	4. Path: Identifies the resource (e.g., /api/categories/1).
	5. Query: Key-value pairs for operations/filtering (e.g., ?sortBy=name).
	6. Fragment: Internal link within a document (less common for APIs).

##### HTTP Request and Response Structure
1. Request:
	1. Request Line: METHOD URL HTTP/VERSION (e.g., GET /api/categories HTTP/1.1)
	2. Headers: Key-value pairs (e.g., Host: example.com, Accept: application/json).
	3. Empty Line: Separates headers from body.
	4. Body (Optional): Data for POST, PUT, PATCH requests. GET requests usually do not have a body.
2. Response:
	1. Status Line: HTTP/VERSION STATUS_CODE REASON_PHRASE (e.g., HTTP/1.1 200 OK)
	2. Headers: Key-value pairs (e.g., Content-Type: application/json, Content-Length: 123).
	3. Empty Line:
	4. Body (Optional): The resource representation (e.g., JSON data).
3. Content-Type header: Tells the client how to interpret the response body.
4. Content-Length header: Indicates the size of the response body.

##### Tools for Web Service Development
1. Browser: Good for GET requests. Can use JSON format extensions (e.g., in Chrome) for better viewing.
2. Postman: A dedicated application for building, sending, and saving HTTP requests (supports all verbs, headers, body, etc.). Useful for testing APIs.
3. HTTP Client Libraries: Built-in tools or libraries within programming languages for making HTTP requests.