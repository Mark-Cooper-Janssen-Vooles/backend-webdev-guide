# Back End Web Development 
This document is intended to cover the aspects required to write back-end production code. It generally follows the format of:
````
//contents section
    //github repo example link
      //readme is a "cheatsheet"
      //code examples
````

Contents: 
- [C#](#c)
- [Clean Code](#clean-code)
- [C# Unit Testing](#unit-testing-in-c)
- [Database Deepdive](#database-deepdive)
- [REST API Standard](#rest-api-standard)
  - [API specification](#api-specification)
  - [Documentation](#documentation)
  - [HTTP requests and responses](#http-requests-and-responses)
  - [Authentication and Authorization](#authentication-and-authorization)
  - [Naming conventions](#naming-conventions)
  - [Data representation and localisation](#data-representation-and-localisation)
  - [Deploy and Release](#deploy-and-release)
  - [Operating and Monitoring](#operating-and-monitoring)
  - [Hateoas](#hateoas)
  - [OpenAPI spec](#openapi-spec)
- [.NET Web API Example](#net-rest-api-thorough-example)
- [SOLID design principles](#solid-design-principles)
- [Design Patterns in C# and .NET](#design-patterns-in-c-and-net)
- [Hexagonal Architecture](#hexagonal-architecture)
- [Apache Kafka (eventing)](#apache-kafka)
- [Terraform](#terraform)
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
## Database Deepdive
Covering topics such as ACID, N+1 Problem, Normalization, Transactions, Failure Modes, profiling performance and scaling databases.

- https://github.com/Mark-Cooper-Janssen-Vooles/databases-deepdive 

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
  - GET requests are used to read either a single or a collection resource
    - GET requests for individual resources will usually generate a 404 if the resource does not exist
    - GET requests for collection resources may return either 200 (if the collection is empty) or 404 (if the collection is missing)
    - GET requests must NOT have a request body payload
  - GET with body
    - APIs sometimes face the problem, that they have to provide extensive structured request information with GET, that may conflict with the size limits of clients, load-balancers, and servers. As we require APIs to be standard conform (body in GET must be ignored on server side), API designers have to check the following two options:
      - GET with URL encoded query parameters: when it is possible to encode the request information in query parameters, respecting the usual size limits of clients, gateways, and servers, this should be the first choice. The request information can either be provided distributed to multiple query parameters or a single structured URL encoded string.
      - POST with body content: when a GET with URL encoded query parameters is not possible, a POST with body content must be used. In this case the endpoint must be documented with the hint GET with Body to transport the GET semantic of this call.
  - PUT requests are used to update (in rare cases to create) entire resources - single or collection resources
    - PUT requests are usually applied to single resources, and not to collection resources, as this would imply replacing the entire collection
    - PUT requests are usually robust against non-existence of resources by implicitly creating before updating
    - on successful PUT requests, the server will replace the entire resource addressed by the URL with the representation passed in the payload (subsequent reads will deliver the same payload)
    - successful PUT requests will usually generate 200 or 204 (if the resource was updated – with or without actual content returned), and {201} (if the resource was created)
  - POST requests are idomatically used to create single resources on a collection resource endpoint
    - on a successful POST request, the server will create one or multiple new resources and provide their URI/URLs in the response 
    - successful POST requests will usually generate 200 (of resources have been updated), 201 (if resources have been created), 202 (if the request was accepted but has not been finished yet), and exceptionally 204 with Location header (if the actual resource is not returned).
  - PATCH requests are used to update parts of single resources, i.e. where only a specific subset of resource fields should be replaced.
    - PATCH requests are usually applied to single resources as patching entire collection is challenging
    - PATCH requests are usually not robust against non-existence of resource instances
    - on successful PATCH requests, the server will update parts of the resource addressed by the URL as defined by the change request in the payload
    - successful PATCH requests will usually generate 200 or 204 (if resources have been updated with or without updated content returned)
    - use PUT with complete objects to update a resource as long as feasible (i.e. do not use PATCH at all if you can get away with it)
  - DELETE requests are used to delete resources 
    - DELETE requests are usually applied to single resources, not on collection resources, as this would imply deleting the entire collection
    - successful DELETE requests will usually generate 200 (if the deleted resource is returned) or 204 (if no content is returned)
    - failed DELETE requests will usually generate 404 (if the resource cannot be found) or 410 (if the resource was already deleted before)
  - HEAD requests are used to retrieve the header information of single resources and resource collections
    - HEAD has exactly the same semantics as GET, but returns headers only (no body)
  - OPTIONS requests are used to inspect the available operations (HTTP methods) of a given endpoint 
    - OPTIONS responses are usually either return a comma separated list of methods in the Allow header or as a structured list of link templates
    - OPTIONS is rarely implemented
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

#### Hateoas 
HATEOAS is an acronym for Hypermedia As The Engine Of Application State - its the concept that when sending information over a RESTful API the document received should contain everything the client needs in order to parse and use the data 
  - i.e. they don't have to contact any other endpoint not explicitly mentioned within the Document 

#### OpenAPI Spec
OpenAPI Specification (OAS) defines a standard, language-agonostic interface to RESTful APIs which allow both humans and computers to discover and understand what the service does without access to the source code etc. 
- When defined, the consumer can understand and interact with the remote service with a minimal amount of implementation logic 
- the openAPI definition can be used by documentation generation tools to display the API
- i.e. in c# .net it uses swagger: https://swagger.io/specification/

---

## .NET Rest API (thorough example)
When building out an API you:
1. firstly need to understand the domain and create the domain models. This involves thinking thoroughly about the database schema and tables.
2. From here install entity framework core, set up a local connection to DB, connect it, and seed the DB. r
3. Create the required CRUD controllers to modify the DB Tables. 
4. Add in validations to the controllers for data sanitisation. 
5. Add authorisation / authentication. 

Code example found here: https://github.com/Mark-Cooper-Janssen-Vooles/dotnet-web-api

---
## SOLID Design Principles
SOLID is frequently referenced in design pattern literature and comprises of 5 principles.

Code examples found here: https://github.com/Mark-Cooper-Janssen-Vooles/design-patterns-csharp#SOLID-Design-Principles

### Single Responsibility
- Every class, module or function in a program should have one responsibility / purpose

### Open-Closed Principle 
- A class should be open for extension, but closed for modification 
- People should be able to extend the functionality of a class, but not by modifying that class directly
- i.e. generate new classes via inheritance of interfaces, rather than modifying existing classes

### Liskov Substitution Principle 
- You should be able to substitute a base type for a sub type 
- i.e. you should only inherit from a class if the child can be used in place of the parent. a box is a rectangle, but a box's width always equals its height so this breaks the Liskov substution principle.

### Interface Segregation Principle
- Interfaces should be segregated so that nobody implementing your interfaces has to implement functions or properties they don't actually need
- I.e. you shouldn't need to implement a function or property if you don't actually need it in your class (in this case, change the interface)

### Dependency Inversion Principle
- High-level parts of the system should not depend on low-level parts of the system directly, they should depend on abstraction
- You only need to consequently apply the Open/Closed and the Liskov Substitution principles to your code base. After you have done that, your classes also comply with the Dependency Inversion Principle


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
## Hexagonal Architecture 
https://medium.com/swlh/hexagonal-architecture-in-java-b980bfc07366 


- The purpose of hexagonal architecture is to avoid known structural pitfalls in software design, such as the pollution of UI code with business logic or undesired dependencies between layers. 
- It aims at creating loosely coupled components that can be connected to software environments using "ports" and "adapters". A port in C# is an interface, and an adapter is the implementation of that interface. i.e. so they can be "plugged in" and "plugged out".
- This makes components (UI, DB, anything that plugs into an adapter) replacable at any level and facilities testing of the single parts


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
## Terraform 

An open-source infrastructure as code software tool created by HashiCorp. 
A tool for doing provisioning on day 1 and day 2+

Terraform takes an infrastructure as code approach - you can declaratively define in tf config files what you want your infrastructure to look like. 
i.e. you might have a VPC, a SG, a VM and a load balancer. 

Terraform commands:
- Refresh => reconciles what it thinks terraform looks like (TF view) with the real world (whats actually running in AWS)
- Plan => reconciles the real world, what is actually running, with our desired config 
- Apply => starts with plan and then executes this against the real world, it also works out what is the right order this needs to be done in

The above gives us a way to do our day 1 infrastructure 

Day 2 we might want to add a DNS, a CDN, a monitoring system. So we change our TF config, and then re-run the same commands: 
- refresh (TF realises we have existing resources, but also we don't have some)
- plan (TF says existing resources don't need to change, but we must add 3 new resources)
- apply (it adds the 3 new things)

Day 1 is the same as day 2, day 2 is "forever". 
Day N is when we want to get rid of our TF. It has the command: 
- Destroy (destroys all the resources associated with this application)


How does terraform work? 
TF Config (provided by the user) => CORE
real world State (provided by TF) => CORE

TF supports many providers (i.e. AWS, azure, other "infrastructure as a service" or IAAS). It can also manage PaaS, SaaS.

Terraform is used in teams:
- Individual contributer writes TF locally => checks the plan, if that looks good, runs apply. 
- If multiple contributers working on TF, similar to how we might use github, we use "terraform enterprise". First we have a version control system (i.e. github) and then we have the "terraform enterprise" off of that.
  - this manages the state and makes sure updates are sequential
  - we can store variables and encrypt them here

Terraform has a concept of "modules" which is what you'd serve a developer, where as the early operator / devops team might create the full terraform config file, a 'module' is more of a black box of only things we need to know about. These are served from a registry. We can run private registries too.


More information and examples can be found here: 
https://github.com/Mark-Cooper-Janssen-Vooles/terraform-learnings 


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