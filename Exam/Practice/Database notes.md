## Database Layer
### The purpose of the Database layer
*A database is not only a storage, but an active system responsible for:* 

- enforcing data integrity
- preventing redundancy
- Supporting complex 
- acting as a stable contract for the backend

In this project, the database had to support two fundamentally different types of data:
1. Fixed external data (IMDb + OMDb)
2. item User-generated interaction data
*This distinction is central*

## Core Design Principle: Split into Two Models
### Movie Data Model (Fixed Data)
*The Movie Model stores data imported from external datasets (IMDb + OMDb)* 

This data is:
- read-heavy
- rarely changed
- authoritative

**Examples**
- titles
- individuals (actors, directors)
- genres
- episodes 
- contributions

The source IMDb schema was not suitable for direct use because it violated basic relational principles, *at least that's what we wrote in portfolio 1, page 4*.

### Framework Model (User Interaction Data)
*The Framework Model stores user behavior*: 
- ratings
- bookmarks
- searches
- visited pages

This data
- is created dynamically
- grows over time
- depends on Movie Data entities

Key idea:
	User interaction data is built on top of the movie data and is never independent of it.

*This is shown in our ER diagrams*
### Problems in the original IMDb Dataset

#### Redundancy
*Redundancy occurs when the same information is stored multiple times.* 

Examples from the source 
- `title_basics.genres` stored as comma-separated values
- `title_crew.directors` and `writers` stored as string lists

**Problems**:
- Violates First Normal Form (1NF)
- Cannot enforce foreign keys
- Hard to query reliably
- Risk of inconsistent updates

#### Incompleteness
*Incompleteness means the schema cannot represent certain relationships properly.*

**Example**
`name_basics.knownForTitles` stores multiple title IDs as a single string.

**Consequences**
Impossible to model as a real relationship
Forces string parsing or NULL hacks
Loses semantic meaning

Key idea:
	Redundancy leads to inconsistency, while incompleteness leads to loss of information.

*We used Silbertschats in our report to explain this*

## Redesign Strategy: Normalize and Make Relationships Explicit
### Central Entities 
*Our redesigned schema revolves around a few core entities*
- Title → Central entity, identified by `tconst`
- Individual → Represents people, identified by `nconst`
- Contributor → Junction table between title and individual 
	- role
	- details (e.g. character name)
	- priority

This replaces multiple string-based lists with **one normalized relation.**

### Why Contributor is a Relation, Not an Entity
*Stuble but strong design choice*

**The contributor table**
- connects two entities
- carries attributes about the *relationship itself*

This follows ER modeling best practices
	When a relationship has its own attributes, it must be modeled explicitly

This supports
- co-actor queries 
- role-based filtering
- ordered credits

### Genre and Other Junction Tables

Genres are modeled as
- `genre`
- `title_genre`

This solves: 
- multi-value fields
- future extensibility
- efficient querying

Episodes are normalized to:
- support parent-child relationships
- avoid embedding hierarchy inside attributes

## Framework Model: Modeling User Actions Correctly 
*This is an important conceptual choice in the database*

### User Actions as Relationships 
Instead of:
- Adding columns to user or title tables

We modeled actions **relationships**
- rating (user ↔ title)
- bookmarks (user ↔ page)
- visits (user ↔ page)
- searches (user ↔ `search_string`)

Why this matters
- avoids redundancy
- allows multiple actions over time
- supports timestamps