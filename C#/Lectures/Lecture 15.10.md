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

