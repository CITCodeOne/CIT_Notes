### Goal of the project
- Implement an online database for movies, actors, directors etc. 
- Display (fixed) information (movie titles, years, person names, ages, etc.)
- Visualize user-generated data (history, my rating of a movie, overall rating of a movie, ect.)
- Browse movies, actors, etc.
- Search for movies, actors, etc.
- Support bookmarking items.
- Allow rating of movies.
- Keep track of users' search history.


### Source of data
1. IMBd 
2. OMDB
3. TMDB

We'll use a smaller database, that Troels will hand out to us, for us to use in the project. 
We'll use an public online API.

3-layer architecture
1. Presentation
	1. Single-page web application
	2. Runs in browser
2. Service
	1. Application logic
	2. Interface to the presentation layer through web service
3. Data
	1. Encapsulates storage and retrieval of data
	2. Provides an interface to the service layer by a set of functions and procedures.


Portfolio subproject
1. Database
	1. Design and implement a database
	2. Fixed data from IMDb and OMDb
	3. Additional structures to support users (signing up, bookmarking, rating, etc.
	4. Provide an API to the backend.
2. Backend (server)
	1. Design and implement web services to accress and manipulate the database
	2. Provide aa RESTful web service interface to the frontend.
3. Frontend (client)
	1. Design and implement a graphical user interface to the application
	2. Access data from TMDb via its API

Report
1. Analyze
2. Design
3. Implement
4. Evaluate
5. Reflect

Dont
6. Make a diary of activities
7. Don't just explain motivation or justify. 
8. Don't make a list of things
9. Avoid stating the obvious


Guide, but don't flesh it out. 


Writing style
1. Be precise
2. Be consistent
3. Be concise
4. Be formal
5. Be systematic 
6. Make the report readable
7. Write in english.
8. Use bullets, lists, tables, diagrams, sections.
9. Don't write long reports.

Format
1. Consistant format
2. Number all sections
3. Number all pages
4. Add a table of contents
5. Use a monospace front for code
6. Don't copy-paste code from IDE.
7. Never show screenshots of code
8. Have a lot of figures? But them in the appendics.