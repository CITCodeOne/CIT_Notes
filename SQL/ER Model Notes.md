### What is the ER Model?
- Developed to help with datebase design. 
- The schema = logical structure of the database
- Uses 3 basic concepts 
	- Entity sets (things)
	- Relationships sets (assosciations between things)
	- Attributes (properties of things)
- Visualized in an ER Diagram.

*Think of ER diagrams like a family tree*
**Entities** are the people
**Relationships** are marriages or parent/chil connections
**Attributes** are names, birthdays, etc. 

#### Entities and Entity sets
1. Entity — a distinct object (person, company event).
2. Entity set — a group of similar entities (all students, all cars)
3. Attributes — describe entities (student has `ID`, `name`, `credits`)
4. Primary key — uniquely identifies each entity. 

Notation:
- Rectangle = entity set
- Attributes inside
- Primary key underlined. 

#### Relationships 
- Relationship — an association between entities.
	- Example: Instructor advises Student. 
- Binary relationship — between 2 entities. 
- N-ary relationship — between more than 2 entities (rare)

Notation:
Diamond = relationship 
Lines connect entities 
Attributes can aslo be attatched to relationships (e.g., 'date' for when advisor started)

