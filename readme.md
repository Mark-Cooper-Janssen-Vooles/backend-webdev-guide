# Back End Web Development 
This document is intended to cover the aspects required to write back-end production code. It generally follows the format of:
````
//contents section
  //gist cheatsheet  link
    //github repo example link
````
Let me know what you think, if I've missed anything or represented anything incorrectly - happy to hear any feedback!


Contents: 
- C# - basic, intermediate, advanced (DONE)
- Clean Code (document clean coders + mosh tutorial) (DONE Mosh, clean coders will take awhile - doing it asynchronously!)
- Testing => nunit or xunit, MOQ (Started) - maybe XUnit too?
- HTTP fundamentals: https://www.pluralsight.com/courses/xhttp-fund (more value in doing networking - leave for now)
- Design Patterns in C# and .NET https://www.udemy.com/course/design-patterns-csharp-dotnet/ 
  - read this: https://hub.packtpub.com/famous-gang-of-four-design-patterns/ 
  - Possible sections on web API / domain logic / database layer? Super general design patterns? Probably covered tutorial
- Dotnet core specific tutorial?: https://docs.microsoft.com/en-us/aspnet/core/fundamentals/?view=aspnetcore-3.1&tabs=linux 

- Authentication / integration with identity: Security, securing API, CORS? https://docs.microsoft.com/en-us/aspnet/core/security/authentication/?view=aspnetcore-3.1 
- Good API / Good rest design course?  
- Read / write about these concepts: test doubles (or test deck), YAGNI (you aint gonna need it), command query separation

- create-xero-app ... equivilent for dotnet? https://github.dev.xero.com/oliver-cardwell/HelloKubernetes
- Example of a basic app 
- Getting a project set up
​

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

