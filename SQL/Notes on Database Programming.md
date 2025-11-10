Why Programming Languages + SQL?
1. SQL is powerful but limited 
	1. Great for *declaring what data you want* (queries).
	2. Bad at *how to use* that data (loops, conditions, printing, user interaction).
2. Takeaway  — you need SQL **plus** a general-purpose language (Java, C#, Python, etc.)

APIs for Database Access
API = Application Programming Interface (a "bridge" between program and DB).

Common ones
1. JDBC → for Java
2. ODBC → for C, C++, C#, Visual Basic
3. ADO.NET → for .NET framework (C# in this course)

Steps (They are always the same)
1. Open a connection to the DB
2. Create a *statement* (SQL command holder)
3. Execute query
4. Extract results (rows/columns)
5. Close the connection
