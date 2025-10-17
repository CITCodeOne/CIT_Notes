### Main Takeaway
This document discusses two distinct but related topics: different strategies for API versioning (Java vs. Python approach, URL, parameters, content negotiation, custom headers) and various aspects of software testing, including handling dependencies, test pyramid, properties of good unit tests, and mocking techniques.

### Versioning
Versioning addresses how to manage changes in software versions, especially for APIs.
Approaches to Versioning

• Java Approach (Backward Compatibility):
	◦ Problem: Avoids breaking existing systems by maintaining compatibility across versions.
	◦ Consequence: Accumulates "old stuff" requiring continuous maintenance alongside new features, leading to increased code complexity and slowness.
	◦ Example: Java's reluctance to change core components due to fear of breaking systems.
• Python Approach (Breaking Changes):
	◦ Method: Introduces breaking changes between major versions (e.g., Python 2 to Python 3).
	◦ Consequence: Forces clients to adapt to new versions, potentially requiring parallel maintenance of older versions but allowing for cleaner breaks and isolated maintenance.
	◦ Advantage: Can "break free of the chains" of legacy code.

#### API Versioning Strategies (for Web Services)
Several methods exist for exposing API versions to clients:
1.	Versioning in the URI Path:
	◦ Method: Add the version number directly to the URL (e.g., /api/v1/resource).
	◦ Examples: Tumblr used this approach.
	◦ Pros: Simple separation of versions (v1, v2, v3 in their own domains).
	◦ Cons:
		‣ Requires a lot from the client (needs to know the specific version URL).
		‣ Can be problematic if clients rely on state or if versions aren't self-descriptive.
		‣ Ambiguity if no version is provided (default to latest? risk breaking clients).
2.	Versioning in the URI Parameters:
	◦ Method: Use a query parameter to specify the version (e.g., /api/resource?version=1).
	◦ Examples: Netflix used this approach.
	◦ Pros: Slightly easier than path versioning.
	◦ Cons: Same issues as URI path versioning regarding default versioning and potential for breaking clients if the parameter is omitted.
3.	Versioning with Content Negotiation (HTTP Headers):
	◦ Method: Specify the desired version within the Content-Type header.
	◦ Examples: GitHub API used this.
	◦ Pros: More aligned with HTTP standards for content type.
	◦ Cons:
		‣ More complicated for clients to deal with as it requires setting specific headers rather than just pasting a URL.
		‣ Some APIs might not easily expose this interface.
		‣ What happens if no version is provided?
4.	Versioning with Custom Headers:
	◦ Method: Add custom HTTP headers (e.g., X-API-Version: 1).
	◦ Examples: HTML used this for defining headers (e.g., any header starting with HTML_).
	◦ Pros: Similar to content negotiation in principle.
	◦ Cons: Same challenges as content negotiation regarding complexity for clients and handling missing headers.

Timestamp Versioning: Mentioned as another way, but not elaborated on in detail.

*Overall Recommendation: The speaker suggests that many projects might not need to deal with versioning at all.*


### Testing with Dependencies
##### Challenges of Testing with Dependencies
1. When a component (e.g., a data service) depends on external resources (e.g., a database), testing becomes complex.
2. Problem: How to test a data service communicating with a database without using a production database?
3. Issue: If tests fail during setup (e.g., adding data to a test database), cleanup might not occur, leading to a "polluted" test database.

##### Solutions for Database Testing
• Dedicated Test Database:
	◦ Use a separate database for testing that can be easily overridden or regenerated from a clean state if polluted.
• In-Memory Databases:
	◦ Example: EF (Entity Framework) Core provides an in-memory version of a database for testing.
	◦ Advantage: Tests run fast, don't persist data (which is often desirable for unit tests).

##### The Test Pyramid
Tests become more complicated and change more often the higher they are in the "paradigm" (layers of a system).
	◦ Unit Tests (Bottom): Simple to set up, fewer dependencies, run very fast (thousands in seconds).
	◦ Service/Integration Tests (Middle): More setup, involve components/features.
	◦ UI Tests (Top): Most complicated, UI changes frequently, should be done as late as possible.

##### Properties of a Good Unit Test
Tests should be:
1.	Atomic:
	◦ Runs independently without relying on other tests.
	◦ Can be run multiple times successfully.
	◦ Should run and end quickly.
2.	Fast:
	◦ Unit tests should run in seconds (thousands of tests).
	◦ Provides quick feedback during development.
3.	Independent & Isolated:
	◦ Does not rely on the state or actions of other tests.
	◦ Avoids issues when tests are run in parallel or in a different order.
4.	KISS (Keep It Simple, Stupid):
	◦ Tests should be easy to understand; no reason for unnecessary complexity.
	◦ Tests can serve as documentation for how to use parts of the code.
5.	DRY (Don't Repeat Yourself):
	◦ Avoid repetitive code in tests.
	◦ Use helper methods or test builders for common setup.
6.	Low Cyclomatic Complexity:
	◦ A test method should have only one path of execution (no if/else, loops, etc., within the test itself).
	◦ If a method under test has multiple paths, write separate tests for each path.
7.	Descriptive and Meaningful Phrases (Naming):
	◦ Test names should clearly explain what they are testing, what arguments are given, and what the expected result is.
	◦ Example: CheckBalanceWithoutNoTransactionAndKeepTheFirmZeroBalance (instead of Test1, Test37).
	◦ Benefit: Provides immediate information about test purpose and failure.
	◦ Consistency: Adopt a naming convention and stick to it.

##### Testing Structure: Arrange, Act, Assert (AAA)
• Arrange: Set up all necessary preconditions and data for the test.
• Act: Perform the action or call the method being tested.
• Assert: Verify that the expected outcome occurred.
• Clean up: (Optional) Remove any created data or resources (e.g., for integration tests).
• Behavior-Driven Development (BDD) equivalent: Given, When, Then.

##### A Common Problem with Unit Tests: Dependencies
• Issue: When testing a class that interacts with other classes, it's hard to isolate the unit being tested from its dependencies.
• Goal: Ensure that if a test fails, you know the failure is in the unit under test, not its dependencies.
• Solution: Remove dependencies to make unit tests truly "unit" tests.

##### Removing Dependencies for Testing (Mocking)
• Design for Testability:
	◦ Consider testing during design (e.g., using dependency injection).
	◦ Example: Controllers in C# can be instantiated as regular classes and tested if their dependencies are removed/replaced.
• Dependency Injection (DI):
	◦ Allows "plugging in" dummy or mock implementations of services instead of real ones.
	◦ Example: Plug a "dummy data service" (implementing the interface but doing nothing or returning fixed values) into a controller to test the controller's logic independently of the actual database.
• Mocking Tools:
	◦ Frameworks that help create "mock" objects without writing full dummy implementations.
	◦ Example: Moq (M-O-Q) in C# allows creating mocks for interfaces and setting up behavior.
	◦ Benefits:
		‣ Can simplify test setup compared to manual dummy implementations.
		‣ Can verify interactions (e.g., how many times a method was called).
	◦ Consideration: Mocking tools require configuration, which might be more complex than a simple dummy for basic cases.
• Verification with Mocks:
	◦ Mocks can include counters or flags to track how many times a method was invoked.
	◦ This allows assertions on interaction counts (e.g., "this method should be called exactly once").