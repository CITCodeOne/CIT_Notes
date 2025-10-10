What do we need to do?
We need to add a REST Web Service Layer, keep things maintainable/testable/extensible, and can use simplified architecture were BL may be merged with DAL/WSL. We need to look at DAL patterns (Repository/Data Mapper/Unity of Work) and we need to focus on decoupling (DOTs between BL and WSL).

*Skal vi bare lave user stories alle sammen, så vi har dem?*

## Architecture ideas

1. Database
	1. We already know what this does.
2. Data Access Layer (DAL)
	1. Translater between database and C# code.
	2. Needs classes called repositories.
3. Business Logic Layer (BLL)
	1. Holds the rules (logic) for how the application behaves.
	2. Needs classes called services
4. Web Service Layer (WSL)
	1. The API the frontend can talk to
	2. Classes called controllers.
5. Data Transfer Objects (DTOs)
	1. Simple objects used to send data between the backend and frontend.
6. Security Layer (cross-cutting)
	1. Keeps track of who is using the system and what they are allowed to do
	2. Protected endpoints
7. Testing Layer
	1. To make sure that everything works correctly and that changes don't break things. 


## Planning

| Role       | Start right away? | Can work in parallel? | First deliverable                  |
| ---------- | ----------------- | --------------------- | ---------------------------------- |
| 1. API     | Yes               | Works with everyone   | Project skeleton + sample routes   |
| 2. Data    | Yes               | Works alone           | EF context + repositories          |
| 3. Domain  | Partially         | After DAL exists      | Service class templates + rules    |
| 4. Auth    | Later             | After API works       | Auth middleware + protected routes |
| 5. Testing | Partially         | Parallel with all     | Testing setup + CI + seed data     |

### Planning phases

1. Setup Phase
	- API create project + routes
	- DAL connects DB + EF mappings
	- BL drafts service structure.
	- Testing sets up framework & seed data

2. Integration phase
	- DAL finalizes repositories
	- BL connects service to DAL
	- API connects controllers to BL
	- Auth starts implementing header-based login

3. Expansion phase
	- All routes working end-to-end
	- JWT added
	- Tests expanded across layers
	- Documentation updated

### Division of tasks
#### 1. API & Routing Lead (WSL)
Design and implement a RESTful Web Service that is stateless, uniform, self-descriptive, resource-centric. 
Needs to support paging and self-references in responses. 
Must include both read-only IMDb endpoints and CRUD for Framework data.

*We need clear URIs, paging, self-links, and consistent JSON output*

1. Create controllers for `- /api/movies, /api/people, /api/users, /api/bookmarks, /api/ratings, /api/history.` 
2. Add `page, pageSize, links.self/next/prev` to all lists
3. Return JSON (no HTML)
4. Include self-references in all responses.
5. Write error handling for 404, 400 and 500.

Check
⇨ 2.A.3 (outer JSON contract)
⇨ 2-C.1-2-C.6 (endpoint table, paging, links, CRUD vs read-only).

Report
⇨ Short examples of JSON responses for a few endpoints (paging + links)
⇨ Describe URI design
⇨ Table for endpoints? 
⇨ Explain implementations
⇨ Error handling and stateless design
⇨ Maybe OpenAPI snippets?

#### 2. Data & Repos (DAL)
Build a Data Access Layer using Entity Framework with repositories/services.
IMDb data = read-only, Framework = CRUD.
Prepare DAL for authentication.
Include object-relational mapping examples.

*We need to use EF entities and show how they map to tables and how data can be filtered by user*

1. Create `EF DbContext` and entities 
2. Write repositories for each data group
3. Use `.Include()` for joins and add indexes where relevant
4. Make methods user-aware.

Check
⇨ 2-A.2 (class diagram showing flow)
⇨ 2-B.1 (mapping examples)
⇨ 2-B.2 (repositories explained)
⇨ 2-B.3 (auth-ready DAL functions)

Report
⇨ Show how repositories fit in our architecture
⇨ 1 or 2 code snippets showing how entities map to tables using EF
⇨ Explain how database structure maps into EF entities (har vi ikke lavet EF? Suk).
⇨ Describe our repositories, maybe a small example query?
⇨ Explain how repositories are ready to handle user-specific data.
⇨ Mention why setup makes testing and future updates easier.

#### 3. Domain & Services
Define user cases/user stories and derive required web service requests.
Implement logic for rating rules, search history, bookmarks, and user management.

*We need service classes that handle 'rules' before data is stored and we have to describe why those services exists in terms of user stories*

1. Implement `SearchService, RatingService, BookmarkService, HistoryService.`
2. Apply rules: one rating per user, log searches, update averages.
3. Keep logic separate from controllers.

**What can be prepared**
> Write pseudo-code or class templates for services
> Define interface and plan each function returns
> Plan how rules will work
> Write unit test for these rules before implementing them (test-driven setup)

Check
⇨ 2-A.1 (user stories derived from requirements)
⇨ 2-A.3 (class diagram showing service layer)
⇨ 2-A.3 (example DTO/JSON structures)
⇨ Reference Subproject 1 (1-D, search + rate functions reused)

Report
⇨ Make a class diagram showing how services connect controllers to repositories.
⇨ Maybe show a domain object and its corresponding DTO?
⇨ Write about how our backend connects to functions already made
⇨ Explain each services depends on a repository for DB access

#### 4. Auth & Security 
Support user-specific data and keep API stateless
Security san evolve from header based to JWT authentication.

*Every request must identify the user. We need at least a header-based auth system now, ready for JWT later*

1. Accept `Username: ... header` (temporary)
2. Later replace with JWT token + `[Authorize]` attributes
3. Pass User identity down by BL and DAL
4. Protect endpoints like `/api/ratings` and `/api/bookmarks/` 

**Cant start right away, what can be prepared?**
> Decide how authentication will be handled
> Write middleware skeleton
> Document plan
> Can design and test early headers once a dummy endpoint exists.

Check
⇨ 2-D (auth flow, stateless REST, `[Authorize]` examples)
⇨ 2-B.3 (how DAL supports user filtering)
⇨ Diagram of user flow

Report
⇨ Make a diagram showing auth flow 
⇨ Show code snippets of how token validation works
⇨ Explain how this approach connects with section 2.B.3 (auth-ready DAL)
⇨ Write about limitations

#### 5. Testing & DevOps
Provide illustrative unit tests for each layer and UI tests for the Web Service Layer that check response formats, paging, and self-links.

*We need to demonstrate that every layer works and responses are formatted correctly*

1. Unit tests for repositories (DAL), services (BL), and controllers (WSL)
2. API tests checking JSON format, paging and links. 
3. Keep data reusable
4. Optional: set up GitHub Actions or another CI pipeline.

**What can be prepared**
> Create a testing project
> Set up the testing framework
> Write example dummy tests for endpoints
> Prepare mock database or local test database (a recommendation, not a must)
> Set up GitHub Actions to run builds and tests automatically
> Make a test data seeding script for quick resets.

Check
⇨ 2-E (test strategy, code examples, coverage table)

Report
⇨ Write about testing strategy
⇨ Short examples of test code
⇨ How did we made sure seed/test data is reused in tests


