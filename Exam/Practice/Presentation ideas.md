## End-to-end Flow
### One concrete feature across all layers 
*User rates a movie example*

1. How rating is modeled in the database
2. How backend exposes it via service + DTO
3. How frontend fetches and displays it
4. Where validation and responsibility live 

### Architectural Decisions & Trade-offs
*Why did we structure it this way?*

**Focus**
- Why was this architecture chosen?
- Why DTOs exists
- Why logic is split between DB and backend 
- Why frontend combines access + logic layer 
- *Maybe* what we would change in the production system

### Data representation & Mapping Across layers
*How data changes shape from DB → API → UI*

- Tables vs EF entities 
- Entities vs DTOs 
- DTOs vs frontend data classes
- Why mapping exists at multiple levels 

*Needs to be anchored in:*
- AutoMapper in backend
- ItemMapper + KeyAliases in frontend

### The Lifecycle of a Query
*We choose one complex query type (e.g. search, rating, co-actors)*

Example
1. User intent
2. Database function logic
3. Backend exposure
4. Frontend consumption

Draw from or talk through
- Inputs
- transformations
- outputs

### Changes in design
*How database design made later changes possible*

1. schema updates
2. backend refactors
3. frontend demands

Draw on
1. Early uncertainty
2. Decisions that paid off
3. Decisions that caused rework
4. Lessons for future projects

### Redundancy vs Incompleteness
*Focusing on one concrete example*

One IMDb issue → our redesign.

Example:
- `knownForTitles` string list
- `genres` comma-separated fields

Draw on
1. Why this is a 'bad' design
2. How we fixed it
3. What queries it now enables
4. Why this matters when looking beyond theory

### How the Frontend Exposed Database Assumptions
*Specific moments where frontend needs caused DB changes*

Examples
- page IDs
- search flexibility
- rating aggregation
- performance expectations

What we should draw on
1. Initial DB assumptions
2. Frontend reality check
3. Resulting adjustments
4. Lesson learned
