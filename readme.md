# Back End Web Development 
This document is intended to cover the aspects required to write back-end production code. It generally follows the format of:
````
//contents section
  //gist cheatsheet  link
    //github repo example link
````
Let me know what you think, if I've missed anything or represented anything incorrectly - happy to hear any feedback!


Contents: 
- C# - basic, intermediate, and advanced concepts (DONE)
- Clean Code (document clean coders + mosh tutorial) (DONE Mosh, clean coders will take awhile - doing it asynchronously!)
- C# Unit Testing (DONE)
- Hexagonal Architecture (DONE)
- Design Patterns in C# and .NET https://www.udemy.com/course/design-patterns-csharp-dotnet/  (IN PROGRESS)
  - read this: https://hub.packtpub.com/famous-gang-of-four-design-patterns/ 
  - Possible sections on web API / domain logic / database layer? Super general design patterns? Probably covered tutorial
- Dotnet core specific tutorial?: https://docs.microsoft.com/en-us/aspnet/core/fundamentals/?view=aspnetcore-3.1&tabs=linux 
- Tinytypes (use these in the dotnet core tutorial!): https://techbeacon.com/app-dev-testing/big-benefits-tiny-types-how-make-your-codes-domain-concepts-explicit


- Authentication / integration with identity: Security, securing API, CORS? https://docs.microsoft.com/en-us/aspnet/core/security/authentication/?view=aspnetcore-3.1 (Ask Ken if I get stuck)
- Good API / Good rest design course: Domain-driven design
- Read / write about these concepts: test doubles (or test deck), YAGNI (you aint gonna need it), command query separation, event sourcing
  - CQRS: https://microservices.io/patterns/data/event-sourcing.html


- create-xero-app ... equivilent for dotnet? https://github.dev.xero.com/oliver-cardwell/HelloKubernetes
- Use events/delegates for something in this c# app... super fuzzy on them
- Example of a basic app 
- Getting a project set up

- Reflection as a concent in dotnet?!
- SQL course using jetbrains DataGrip
- TDD without mocks: https://www.jamesshore.com/v2/projects/lunch-and-learn
- 12 factor app: https://12factor.net/
​

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

TO DO NEXT:
CI/CD focused rotation? infrasturcture as code? 


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


### Design Patterns in C# and .NET
- Backend applications are usually divided into an API layer, a domain logic layer, and a database layer. 
  - the API layer is full of controllers (hopefully thin), what the external world interacts with. Think as "black box" as possible
  - the domain logic layer holds all the logic specifically for how to handle the controllers actions etc
  - the database layer handles connections to the database
  - usually the point of entry is from the API layer (maybe called via front end inputs), the API layer then consults the domain logic layer, which then consults the database layer. 
- 