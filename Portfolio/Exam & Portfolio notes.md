These notes cover crucial aspects of project development, exam requirements, and specific technical implementations discussed, emphasizing best practices, consistency, and understanding of code over mere production.

### Project Design and Implementation Best Practices
##### Paging Implementation
Problem: Managing many pages (e.g., 35 in different directions) is cumbersome.

Solution: Implement "first" and "last" page links/logic on the backend.
1. first: page 0
2. last: total pages - 1

Generic Paging: Code for paging should be moved into a generic method to be reusable across any pages/data.
1.  Requires updating the data service to accept paging parameters.
2. Anti-pattern: Copy-pasting paging code leads to maintenance issues (e.g., adding "first" and "last" links requires changing all instances).

##### Code Organization and Reusability
Controllers: Avoid repeating common methods (e.g., paging logic) across multiple controllers.

Solution: Create a Super Controller (e.g., MyBaseController) that inherits from ControllerBase (for ASP.NET features). All other controllers can then inherit from MyBaseController to share features like paging.

Consistency: Maintain consistency in how features are presented in current and future projects.

### Exam and Portfolio Project Expectations
##### Exam Structure
1. Questions: Three questions, one from each section discussed.
2. Topics Covered: All exam questions will be on topics discussed in class.
3. Availability: Exam details will be available at the end of the first session, providing a couple of months for preparation.

##### Technology Requirements
1. Required: Use C# for the portfolio project.
2. Required: Use ASP.NET and Entity Framework.
3. Mandatory: Projects not written in C# will not pass. The instructor will not review code in other languages (e.g., Node.js, Python).

##### Code Understanding and Cheating Policy
1. Crucial: Do not write any code you don't understand.
2. AI Tools: Using tools like ChatGPT or Copilot is acceptable for examples or answers, but you must understand and own the code you submit.
3. Consequences of Not Understanding:
	1. If you cannot answer questions about your code, you will not pass.
	2. If no one in a group can explain the code, it will be reported as cheating.
	3. AI-generated code often over-engineers solutions, making them harder to understand, leading to trouble if questions cannot be answered.
4. Focus: Solve the problem directly ("Keep it simple, stupid - KISS"). The goal is understanding, not fancy complexity.
5. Instructor's Method: The instructor can identify code they didn't write and will ask "Why did you do it this way? Why not another way? What's the strategy?"

##### Project Documentation
*A template for the project report will be provided over the weekend, outlining required sections.*
1. Content:
	1. Design & Architecture: Explain the chosen architecture, provide diagrams, discuss design decisions, and database records (can use provided examples, but explain them).
	2. Layer Communication: Document how layers communicate and the interfaces between services (e.g., data service). Focus on principles and member names for higher-level abstraction, not all arguments.
	3. Outer-Facing Contract: Document the API contract. Explain principles once for common patterns (e.g., list structures) instead of repeating details for every object.
	4. Domain Model: Describe how the database maps to the object model (e.g., inheritance, media types). Focus on principles, not every detail.
	5. Data Access Layer: Similar to Part 1 assignment. For static data (e.g., movies), only read operations are needed, not full CRUD, as movies are visualized, not added.
	6. Web Service: Create and explain a nice, generic interface for the client. Ease of change is key.
	7. Security: Implement user awareness (for bookmarks, search history). Three ways to implement security:
		1. Hard-coded User: Simplest, always logged in. Recommended for smaller groups if security is difficult.
		2. Username in Request Header: Username passed in clear text. Allows login/logout but not very secure.
		3. Tokens (Standard): Login returns a token, stored on the client, passed with requests for access. (Video available).

> Code in Report: Don't put all code in the report. Include snippets for specific solutions, problems encountered, and explanations of the thought process, especially for problems that were hard to solve or solutions that are particularly good.

1. Git Repository: Link to your Git repository (or equivalent).
2. Code Comments: Use comments in the code itself for internal documentation.

##### Testing
1. Web Service Testing: The web service produced must be tested.
2. Scope: Not exhaustive testing, but examples of tests for different elements/types of functionality.
3. Approach:
	1. Test functions returning paging content (e.g., data service).
	2. Connect to backend, provide arguments, verify expected results (e.g., list count, first link).
	3. If multiple endpoints do the same thing for different resources, one test example is sufficient.
	4. Show different types of tests to demonstrate understanding of testing patterns.

Test Databases: For testing, consider using a separate test database or ensuring test data setup/teardown is robust to avoid inconsistent states.

>Recommendation: Use a script to migrate and populate the database for consistency across development environments and CI servers.


##### Exam Interview Process
1. Individual Participation: First, you'll be asked what parts of the code you participated in.
2. Detailed Questions: Expect detailed questions about the code you claim to have worked on. You should know "every small detail."
3. General Questions: Also expect general questions about the entire project, even parts you didn't implement (e.g., "Explain what this function is doing?"). You need to understand the overall architecture and purpose.
4. Role of Assignment: The assignment serves as a common reference point. The instructor will use it to guide questions, especially if you get stuck, to help you recall how a problem was solved previously.

### Technical Implementations and Concepts
##### Auto-Incrementing IDs
Database Specific: Auto-incrementing (AUTO_INCREMENT in MySQL, IDENTITY in SQL Server, SEQUENCE in PostgreSQL/Oracle) is not a universal SQL standard.

Sequences: PostgreSQL uses sequences behind the scenes for auto-increment.

Simulation (Less Efficient): MAX(ID) + 1 is possible but more expensive as it queries the database for the highest ID. Sequences are preferred for performance.

##### Entity Framework (EF) and Data Loading
1. Include: To load related data (e.g., categories with products), use Include in EF queries.
2. JSON Serialization: ASP.NET automatically transforms objects, including related ones, into JSON.
3. Design Decision: Decide whether to show full nested objects or just essential info + a link to the related resource (Hypermedia API approach).

##### Hypermedia API Design
1. Goal: Provide minimal information for a list view and links to detailed information.
	>Example: Instead of full category object in product list, provide CategoryName and CategoryURL.
2. LinkGenerator: Use LinkGenerator in ASP.NET to create URLs to other endpoints programmatically, avoiding hardcoded strings.
> 	Relies on endpoint names `(e.g., nameof(GetProduct))`. Changes to method names will cause compile-time errors, safeguarding against runtime issues.
3. Performance: For foreign keys (e.g., `CategoryID` in Products table), including the ID directly can improve performance by avoiding joins for just the ID. Decision depends on use case.
4. Real-World Example: GitHub API uses hypermedia (e.g., /users/{username} for individual user details, links to repositories).

##### Paging Implementation Details
Controller Parameters: Add page and pageSize parameters to API endpoints, with default values (e.g., page = 0, pageSize = 5).

Data Service Logic:
1. Pass page and pageSize to the data service.
2. Use Skip(page * pageSize).Take(pageSize) for efficient pagination.
3. Crucial: Always OrderBy results before Skip/Take to ensure consistent ordering.

EF Query Optimization: EF Core is smart enough to optimize paging queries (e.g., LIMIT and OFFSET clauses) to retrieve only the necessary subset of data before joining with related tables, improving performance for large datasets.

Paging Response Object: Wrap the paged items in a response object containing:
1. Items: The list of paged items.
2. Next / Previous: Links to the next/previous pages.
3. NumberOfItems: Total count of items (fetched via Count() in data service).
4. NumberOfPages: Calculated using Math.Ceiling((double)numberOfItems / pageSize).

Link Generation:
1. Current Page Link: `GetUri(nameof(endpointName), new { page, pageSize }).`
2. Previous Link: Only if `page > 0. GetUri(nameof(endpointName), new { page = page - 1, pageSize }).`
3. Next Link: Only if `page < numberOfPages - 1. GetUri(nameof(endpointName), new { page = page + 1, pageSize }).`

Flexibility: Easily extend to generate multiple next/previous page links.

##### Configuration Management
Problem: Hardcoding passwords/settings.

Solution: Use environment variables or configuration files (e.g., config.json that is excluded from Git/version control).
1. Environment variables are set before starting the server and are only accessible within that server's process.
2. For team collaboration, a config.json (or similar) can hold local settings and be ignored by Git, allowing each developer to configure their environment without conflicts.

Video Resources: Instructor will provide videos on:
1. Authentication (three different ways).
2. Salty passwords (why needed, how to implement).
3. Calling database functions from EF Core (returning tables or scalar values).
4. Accessing ADO.NET directly from within EF Core for complex scenarios.
5. Using environment variables/JSON files for configuration.
