# Back End Web Development 
This document is intended to cover the aspects required to write back-end production code. It generally follows the format of:
````
//contents section
    //github repo example link
      //readme is a "cheatsheet"
      //code examples
````

Most recently updated backend project: https://github.com/Mark-Cooper-Janssen-Vooles/quoteApp


Contents: 
- [C#](#c)
- [Clean Code](#clean-code)
- [C# Unit Testing](#unit-testing-in-c)
- [REST API Standard](#rest-api-standard)
  - [API specification](#api-specification)
  - [Documentation](#documentation)
  - [HTTP requests and responses](#http-requests-and-responses)
  - [Authentication and Authorization](#authentication-and-authorization)
  - [Naming conventions](#naming-conventions)
  - [Data representation and localisation](#data-representation-and-localisation)
  - [Deploy and Release](#deploy-and-release)
  - [Operating and Monitoring](#operating-and-monitoring)
- [Hexagonal Architecture](#hexagonal-architecture)
- [Design Patterns in C# and .NET](#design-patterns-in-c-and-net)
- [Apache Kafka (eventing)](#apache-kafka)
- [Example of a basic app](#example-of-a-basic-app)
- [Getting a project set up](#getting-a-project-set-up)


Books:
- Clean Code
- https://www.amazon.com.au/Effective-Engineer-Engineering-Disproportionate-Meaningful/dp/0996128107
- https://martinfowler.com/books/refactoring.html
- https://www.amazon.com.au/Test-Driven-Development-Kent-Beck/dp/0321146530
- https://www.amazon.com.au/Domain-Driven-Design-Tackling-Complexity-Software/dp/0321125215


---
## C#
C# is one of the biggest backend languages used for web development. 
It is a typed programming language developed by Microsoft that runs on the .NET Framework. 

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
## REST API Standard

Expectations and requirements for building production ready, high quality REST APIs. 

Recommended tech stack:
- OpenAPI v3+ Spec
- C# .net 5+ 
- Some monitoring system
- Some authentication system
- Kubernetes

### Best Practices
#### API Specification
- API specification to use OpenAPI v3.0+ Specification format 
- API specification to contain name, major version, description, contact, servers fields 
- API specification to be a single, self-contained file without external references

#### Documentation
- Usage examples to be documented in the readme of the API
- Metered limits, resource limits and hard-limits to be documented in the readme of the API

#### HTTP requests and responses
- HTTPS to be used for all public or consumer-facing external traffic
- UTF-8 encoding to be used for all responses
- HTTP Request methods and common method properties to adhere to RFC 7231 standard
  - https://www.rfc-editor.org/rfc/rfc7231#section-4
- HTTP POST and PATCH methods to be idempotent (i.e. an identical request can be made once or several times in a row with the same effect - i.e. pressing the on/off buttons on a trains multiple times will be the same as pressing it once)
- All responses to use the most appropriate and specific standard HTTP status code defined in RFC 7231 and RFC 6585 stabdards 
  - https://www.rfc-editor.org/rfc/rfc7231#section-6
  - https://www.rfc-editor.org/rfc/rfc6585
- HTTP header fields to be Hyphenated-Pascal-Case format
- Stack traces must not be returned in HTTP responses 

#### Authentication and Authorization 
- Endpoints to be secured using an authentication system

#### Naming Conventions
- API hostnames should follow functional naming schema 
  - i.e. `<functional-name> => <functional-domain>- <functional-component>`
  - `<functional-domain> => managed functional group of components`
  - `<functional-component> => name of owning functional component`
  - e.g. identify-config.somedomain.com
- API basepath to be set for the root ('/') level
- URL paths to not use trailing slashes
- URL path segments to be lowercase words separated with hyphens 
- Resources to be represented with nouns 
- Resources to use domain-specific names (eg: 'sales-order' rather than 'order')
- Resource endpoints to use plural resource forms (eg: /invoices, /sales-orders)
- Resources and sub-resources in path segments to be hierarchically identified (eg: /invoices/{id}/attachments)
- Sub-resources should be limited to three levels 
- HTTP verbs should not be used in a URL path 

#### Data representation and localisation
- Data-interchange to be in RFC7159 JSON format 
  - https://www.rfc-editor.org/rfc/rfc7159
- Numeric values in JSON to be represented as numbers, not strings
- Objects storing monetary values to be using fixed-precision/decimal types instead of floating-point 
- Currency codes to be represented as upper case ISO4217 format 
  - https://www.iso.org/iso-4217-currency-codes.html
- Date and Time values to be represented as “YYYY-MM-DDThh:mm:ss[.sss]Z” format strings
- Boolean property values must not be null
- Absent and null values should be handled identically 
- Empty arrays to be represented as "[]" rather than null values
- Array names should be pluralised 
- Enumeration type values should be represented as strings 
- Conventional query-parameter names to be used for request filtering 
  - i.e. `q` , `sort`, `fields`, `embed`
  - i.e. `/sales-orders?sort=+id`
- The number of items returned by a collection must be limited and paginated 
- Locales should be represented using IETF BCP 47 Unicode format 
  - https://www.unicode.org/reports/tr35/tr35.html
  - the syntax is `"language[-script][-region][-u-extensions]"`.
  - For example, `en-NZ`, `mi-NZ`
- Errors to be represented in Problem JSON (RFC 7807) format, providing error 'type' (URI) and optionally 'title', 'status', 'detail' and other information fields 
  - https://www.rfc-editor.org/rfc/rfc7807
- Content-language header to be supplied in all responses containing localised text (ideally this is in the frontend though)

#### Deploy and Release 
- API service containers to be hosted on Kubernetes ideally 
- Breaking changes to Production APIs should be avoided
  - if unavoidable, notice must be given to clients 6 months in advance 

#### Operating and Monitoring 
- A `/ping` endpoint must be exposed 
- A `/healthcheck` endpoint must be exposed 
- Client requests should be rate limited using HTTP Status Code 429 with headers 
- Inbound and outbound requests should be logged 


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
## Apache Kafka
Notes taken here from a confluent video: https://www.youtube.com/watch?v=06iRM1Ghr1k
Confluent is a cloud-native service fo apache kafka

Apache Kafka lets us think about "events" first. 
An event is an indication in-time that an event took place using a "log", which is an ordered sequence of events. 
Logs are easy to build at scale. 

Apache Kafka is a system for managing these logs, it calls them "topics". 

A topic is an ordered collection of events stored in a durable way (written to disk + replicated).
A topic can store data for a short period of time or long period of time. It can be small or enormous. 

Each event represents a thing happening in a business - i.e. a user updates their shipping address.

Microservices can consume a message from a kafka topic, and produce that message off to another kafka topic. 


---
## Example of a basic app
This app was created using the .net core tutorial below:
- https://docs.microsoft.com/en-us/aspnet/core/tutorials/first-web-api?view=aspnetcore-3.1&tabs=visual-studio


Implementing Swagger into this app:
- Used this tutorial: https://docs.microsoft.com/en-us/aspnet/core/tutorials/getting-started-with-swashbuckle?view=aspnetcore-3.1&tabs=visual-studio
- Launch the app and navigate to ``http://localhost:<port>/swagger/v1/swagger.json``
- By default the swagger UI can be found at ``http://localhost:<port>/swagger``, note the UI is driven by the generated JSON schema
- SwaggerUI makes it easy to work with and test APIs - easier to use than postman or curl etc. 


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