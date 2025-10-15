## Conceptual Overview of ASP.NET MVC

ASP.NET MVC is an architectural design pattern and a framework for building web applications. It separates an application into three main components.

1. Model: Represents the data and business logic of the application. It should be a simple object with properties to support a view. 
2. View: The user interface (UI) that displays the data from the model. It should focus on standards-based markup with minimal logic, limited to user interaction.
3. Controller: handles user input, interacts with the model, and selects a view to render. It acts as a coordinator between the Model and the View. 

This separation of concerns offers several advantages over traditional ASP.NET Web Forms, including the elimination of view state, the generation of cleaner, standards-compliant HTML, The removal of the complex page life features—like view engines (e.g. Razor), model binders, HTML helpers, and filters to streamline development. 


| AJAX                                                                                                                                              | ASP.NET MVC                                                                                                                                                                               |
| ------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Asynchronous web requests<br>Lets part of a page update without a full reload<br>Uses jQuerry's `$.ajax` with parameters—<br>url, dataType, type. | A Microsoft web framework<br>Employs a three-part design pattern<br>Data (model)<br>Presentation (view)<br>Control logic (controller)<br>Used to improve testability and maintainability. |
| **Client-side Validation**                                                                                                                        | **Design Pattern**                                                                                                                                                                        |
|                                                                                                                                                   |                                                                                                                                                                                           |


### Building a Basic Application
Creating a simple MVC application involves defining a model, creating a controller action to populate it, and passing it to view for display. 

**Models**
A model is typically a plain C# class that defines the data structure. 
> for instance, a `GreetingModel` might have `string` properties for a `name` and a `Greeting`

**Controllers and Actions**
A controller action is a method that prepares data. It create an instance of the model, populates its properties, and passes it to the view using `return View(model);`

**Views and the Razor Engine**
The Razor view engine uses a streamlined syntax to embed server-side code within HTML

1. `@model` and `@Model` 
the `@model` directive at the top of a view file declares the data type of the model it expects.
the `@Model` property (with a capital M) is used to access the instance of the model passed from the controller (e.g. `@Model.Greeting`)

2. HTML Helpers
Like `@Html.LabelFor()` and `@Html.TextBoxFor()` are methods that generate a standard HTML elements. They are strongly typed using lambda expressions (e.g. m => m.UserName), which provides compile-time checking and refactoring support. 

3. Layouts
(`_Layout.cshtml`) provide a consistent template for all pages. The `@RenderBody()` method within the layout files specifies where the content of individual views will be rendered. 

4. Sections
Allow views to inject content into specific named loctions in the layout file, such as a "scripts" section at the bottom of the page.

### Handling User Input and Forms
Web applications require two-way communication. MVC facilitates this through model binding and editor templates. 

**Model Binding**
When a user subits a form, the MVC framework automatically populates an action method's parameter (e.g., `public ActionResult Create(ItineraryItem)`) with the submitted form values. The framework matches form field names to the model's property names. 

**Filters**
Attributes like `[HttpPost]` can be applied to action methods to restrict them to specific HTTP request types, preventing ambiguity and enhancing security. 

**Editor Templates** 
The `@Html.EditorFor()` helper is a powerful feature that renders the most appropriate HTML input control for a given model property. 

1. Based on Data Type: It renders a text box for a `string`, a number input for an `int`, a checkbox for `bool`, and a dropdown list for a nullable `bool(bool?)`.
2. Based on Attributes: You can influence the choice of editor by decorating model properties with attributes from `System.ComponentModel.DataAnnotations`. For example, `[Datatype(DataType.MultiLineText)]` will render a `<textarea>`.
3. Custom Templates: You can create custom editor templates by placing a `.cshtml` file in the `Views/Shared/EditorTemplates` folder. For instance creating `DateTime.cshtml` allows you to define a custom editor (like a date picker) that will be used for all `DateTime` properties throughout the application when `EditorFor` is called. The `UIHint` attribute can also be used to specify a custom template by name.

### Validation 
MVC provides a "defense in depth" approach to validation, where rules are defined once on the model and enforced on both the client (via JavaScript) and the server. 

**Data Annotation Attributes**
Validation rules are applied to model properties using attributes. Common validations include
> `[Required]`: Ensures a value is provided 
> `[MaxLength(140)]`: Sets a maximum string length.
> `[Range(1, 120)]`: Defines a numeric range.
> `[RegularExpression(@\d{1,3}")]`: Validates against regular expression. The expression `\d1,3` matches a string containing one to three digits. 

**Controller Logic**
In the `[HttpPost]` action, you have to check `ModelState.IsValid` before processing the data. If it's false, you should return the view with the model so that validation errors can be displayed to the user. 

**Displaying Errors**
The `@Html.ValidationMessageFor()` helper displays validation errors for a specific property, while `@Html.ValidationSummary()` can display all errors in a single list. 

**Remote Validation**
For complex server-side validation (e.g., checking if a username is already taken), the `[Remote]` attribute can be used. This attribute points to a controller action that the client-side will call asynchronously to perform validation as the user types.

### Client-Side Interactivity with jQuerry
MVC intergrates seamlessly with jQuerry to create dynamic and responsive user experience without full-page postbacks. Controller actions serve as natural endpoints for AJAX calls. Best practices include making AJAX endpoints `[HttpPost]` actions that return a `JsonResult`.

Common uses casses include:
1. Asynchronously Loading Content: Using `$.ajax()` in a `$(document).read()` function to load secondary content (like advertisements or menus) after the main page has rendered, improving initial load time. 
2. User-Triggered Events: Attaching click handlers to elements to fetch data on demand, such as loading help text when a user clicks a help icon. 
3. Partial Page Updates: Allowing users to perform actions like deleting an item from a list. An AJAX call is made to a controller action to update the data in the database, and upon success, jQuerry is used to remove the corresponding element from the UI without a page refresh.

### Scaffolding
Visual Studio provides scaffolding features to auto-generate boilerplate code for controllers and views based on a model. This is a "one-time-only" code generation process designed to provide a starting point for development. You can choose templates to create controllers with full CRUD (Create, Read, Update, Delete) actions and corresponding views. The templates themselves are T4 (`.tt`) files, which can be customized to change the generated code to match specific architectural patterns or coding standards.