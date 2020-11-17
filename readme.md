# Back End Web Development 
This document is intended to cover the aspects required to write back-end production code. It generally follows the format of:
````
//contents section
    //github repo example link
      //readme is a "cheatsheet"
      //code examples
````
Let me know what you think, if I've missed anything or represented anything incorrectly - happy to hear any feedback!


Contents: 
- C# - basic, intermediate, and advanced concepts :heavy_check_mark:
- Clean Code :heavy_check_mark:
- C# Unit Testing :heavy_check_mark:
- Hexagonal Architecture :heavy_check_mark:
- Design Patterns in C# and .NET :heavy_check_mark:
- create-xero-app ... equivilent for dotnet: https://github.dev.xero.com/oliver-cardwell/HelloKubernetes :x:
- Example of a basic app :heavy_check_mark:
  - Implement swagger into this app :x:
  - Implement database into this app => use dynamoDB :x:
- Getting a project set up :heavy_check_mark:


Future Ideas of study: 
- https://www.pluralsight.com/paths/aspnet-core 
- Authentication / integration with identity: Security, securing API, CORS? https://docs.microsoft.com/en-us/aspnet/core/security/authentication/?view=aspnetcore-3.1 (Ask Ken if I get stuck)
- Good API / Good rest design course: Domain-driven design
- Read / write about these concepts: test doubles (or test deck), YAGNI (you aint gonna need it), command query separation, event sourcing
  - CQRS: https://microservices.io/patterns/data/event-sourcing.html
- Databases / SQL stuff 
- Reflection as a concept in dotnet?!
- TDD without mocks: https://www.jamesshore.com/v2/projects/lunch-and-learn
- 12 factor app: https://12factor.net/
​- Threading
- Tinytypes (use these in the dotnet core tutorial!): https://techbeacon.com/app-dev-testing/big-benefits-tiny-types-how-make-your-codes-domain-concepts-explicit


more content ideas: https://github.com/kamranahmedse/developer-roadmap


Books:
- Clean Code
- https://www.amazon.com.au/Effective-Engineer-Engineering-Disproportionate-Meaningful/dp/0996128107
- https://martinfowler.com/books/refactoring.html
- https://www.amazon.com.au/Test-Driven-Development-Kent-Beck/dp/0321146530
- https://www.amazon.com.au/Domain-Driven-Design-Tackling-Complexity-Software/dp/0321125215


Useful guides to know what is the current best languages and frameworks:
- https://www.thoughtworks.com/radar/languages-and-frameworks  
- https://techradar.xero-support.com
​

---


## C#
C# is the main backend language used at Xero. 


C# beginners
- Intro to c# and .NET
- Primitive Types and Expressions
- Non-Primitive Types
- Control Flow
- Arrays and Lists
- Working with dates
- Working with text
- Working with files
- Debugging apps


C# intermediate
- Classes
- Association between classes
- Inheritance - second pillar of OOP
- Polymorphism - third pillar of OOP
- Interfaces


C# advanced
- Generics
- Delegates
- Lambda Expressions
- Events
- Extension Methods
- LINQ
- Nullable Types
- Dynamic
- Exception Handling
- Asynchronous Programming


C# cheat sheet with examples: 
- https://github.com/Mark-Cooper-Janssen-Vooles/c--learnings
​

---


## Clean Code
"You know that you're reading clean code, whenever every thing you read is pretty much what you expected"


Clean Coders show notes (by uncle Bob, the author of Clean Code):
https://github.com/Mark-Cooper-Janssen-Vooles/cleancoders


C# Specific Clean code:
https://github.com/Mark-Cooper-Janssen-Vooles/clean_code_mosh


---


## Unit Testing in C#
Unit testing C# Code with NUnit and Moq: Dependency injection, best practices and pitfalls to avoid. 


https://www.youtube.com/watch?time_continue=4&v=EZ05e7EMOLM&feature=emb_logo
Notes:
- Only test public methods
- Mock very sparingly - only external dependencies (like db etc)
- Recommends testing behaviors, not classes. Might span multiple classes. End-to-end testing inside a backend… test a functionality. If you're working on a new workflow - this is super important. Requires a good understanding of the whole workflow. If you're just altering an existing behaviour, this might be too difficult to apply.
- Don’t make everything public in order to test it (it should probably be private unless you need it!)
- Preserve implementation testing by having a thin API (in this case API means public methods in a class - not REST)
- Integration tests are essentially testing that you can talk to the external resource, aka the db, that you’re configured correctly etc (coverage of ports to adapters)


C# Unit Testing:
https://github.com/Mark-Cooper-Janssen-Vooles/UnitTestingCSharp


---


## Hexagonal Architecture 
https://medium.com/swlh/hexagonal-architecture-in-java-b980bfc07366 


- The purpose of hexagonal architecture is to avoid known structural pitfalls in software design, such as the pollution of UI code with business logic or undesired dependencies between layers. 
- It aims at creating loosely coupled components that can be connected to software environments using "ports" and "adapters". A port in C# is an interface, and an adapter is the implementation of that interface. i.e. so they can be "plugged in" and "plugged out".
- This makes components (UI, DB, anything that plugs into an adapter) replacable at any level and facilities testing of the single parts


---


## Design Patterns in C# and .NET
- Backend applications are usually divided into an API layer, a domain logic layer, and a database layer. 
  - the API layer is full of controllers (hopefully thin), what the external world interacts with. Think as "black box" as possible
  - the domain logic layer holds all the logic specifically for how to handle the controllers actions etc
  - the database layer handles connections to the database
  - usually the point of entry is from the API layer (maybe called via front end inputs), the API layer then consults the domain logic layer, which then consults the database layer. 


Gang of 4 Design Patterns illistrated in C#: 
- Ways to deal with different problems from creating objects, structuring classes and behavioural patterns
- https://github.com/Mark-Cooper-Janssen-Vooles/design-patterns-csharp


---


## Example of a basic app
This app was created using the .net core tutorial below:
- https://github.dev.xero.com/mark-janssen-vooles/quote-app-backend
- https://docs.microsoft.com/en-us/aspnet/core/tutorials/first-web-api?view=aspnetcore-3.1&tabs=visual-studio


Implementing Swagger into this app:
- Used this tutorial: https://docs.microsoft.com/en-us/aspnet/core/tutorials/getting-started-with-swashbuckle?view=aspnetcore-3.1&tabs=visual-studio
- Launch the app and navigate to ``http://localhost:<port>/swagger/v1/swagger.json``
- By default the swagger UI can be found at ``http://localhost:<port>/swagger``, note the UI is driven by the generated JSON schema
- SwaggerUI makes it easy to work with and test APIs - easier to use than postman or curl etc. 


Implementing a database into this app:
- DO AWS stuff first, and use dynamoDB

---


## Getting a project set up
You can create .net core apps quickily and easily now by running a few simple commands. 
- ``dotnet new webapi`` was used for the above project, which quickly scaffolds a web api.
  - in the above tutorial, it ran a series of commands that enable you to scaffold a controller. 
````
//note: you may need to play around with versions 
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet tool update -g Dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
````