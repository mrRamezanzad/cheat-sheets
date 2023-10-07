# DDD (Domain Driven Design)  
'is an approach to software development that centers the development on programming a domain model that has a rich understanding of the processes and rules of a domain.'  

Martin Fowler

Only apply it to complex domains
A way to take big messy problem and turn in into small, contained, solvable problems.  1
DDD aims to tackle business complexity, not technical complexity.

Be Prepared for Time and Effort: 
- Discuss and model the problem with domain experts.
- Isolate domain logic from other parts of the application. 

Be Prepared for a Learning Curve: 
- New principles.
- New patterns.
- New processes.

Misuses (possible overuse):
DDD is for handling complexity in business problems.
Not just:
- CRUD or data-driven applications.
- technical complexity without business domain complexity.

### Tutors: 
- John Smith (@ardalis)
- Julie Lerman (@julielerman)
  
### other userful courses
- Design Patterns Learning Path.
- DDD Learning Path.

### Core Concepts
- Interaction with domain experts.
- Model a single subdomain at a time (Divide & Conquer).
> Modeling: How you decipher and design each subdomain.
- Implementaion of subdomains.   
> Seperation of Concerns: plays an important role in implementing subdomains

### Benefits of Domain-Driven Design
- Flexible: change different parts of app without affecting other parts.
- Customer's vision / Perspective of the problem.
- Path through a very complex problem.
- Well-organized and easily tested code.
- Business logic lives in one place.
- Many greate patterns to leverage.

## Demo Example APP
In a Veterinary Office Application we have:
- Appointment scheduling is complex.
- Manage client & patient data is simple CRUD.
- Manage staff and facilities is simple CRUD.
- Other simple data management is simple CURD.
> Frontend: ASP.net Blazor Engine
> Backend: ASP.net Core API's

### Project Structure (Clean Architecture / Onion / Ports & Adapters / N-Tier / Layerd):
- Core: All business logic goes here.
- Infrustructure: Requirements of Business to implement e.g. RabbitMQ, Email Service, Logger, etc.
- API: This is presentation layer or frontend from backends perspective

> API and Infrustructure will all depend on core but Core should not be depend on any of them.

Our Domain: A Veterinary Practice
Domain Expert: Doctor Smith

Functional Requirement:
- Staff need to be able to schedule and schedule their own working shifts as well.
- Invoice for their services.
- Get payment process and in many cases send out bills.
- Store and retrieve medical records.
- Work with external labs and special vet clinics.
- Products to sale and inventory to manage the products.
- Follow ups and reminders that need to be send by mail or phone.

The app requirements are complex enough to use DDD.

### Talking To an Expert:
- Patient scheduling.
- Owner and pet data management.
- Surgery scheduling.
- Office visit data collection.
other operations that are not about caring about pets: 
- Billing(External?).
- Sales and inventory (POS / Point of Sale).
- Lab testing (schedule, result, bill).
- Prescription.
- Staff scheduling.
- CMS (External?) e.g. blog.

Identified Subdomain here are:
- Staff
- Accounting
- Client and patient records
- Visit records
- Appointment scheduling
- Sales

### Conversation with a domain expert (exploring the scheduling subdomain)
- Clients (people) schedule appointment for patients (pets).
- Appointments may be office visits or surgeries.
- Office visits may be an exam requiring a doctor, or a tech visit.
- Office visits depend on exam room availability.
- Surgeries depend on O/R and recovery space availability.
and can involve different kind of procedures.
- Different appointment types and procedures require different staff.

Questions: 
- Any chance of concurrency conflicts?
Doesn't anticipate being big enough to have this problem any time soon.

- Do you need to schedule rooms and staff when you schedule an appoinment? Dependencies?
Room + tech OR...
Room + doctor OR...
Room + doctor + tech

- Does "resources work as an umbrella term for them?
Yes!

### First pass of modeling our subdomain
Appintment Manager Model:

client schedules for Patient an Appointment (types: office visit, vaccination) or a Surgery (types: spay) which requires some resources (e.g. Exam Room, Doctor, O/R, Recovery)

### Bounded Context
To untangle concepts that appear to be shared.
- Define strong boundary around the concepts of each model.
- Ensure model's concept's don't leak into other models where they don't make sense.(evene if they have same name or they are literally same thing they may not make sense)

e.g.
Application Scheduling
Client:
  - Name
  - Identity

Billing
Client:
  - Name
  - Identity
  - Credit cards
  - Address
  - Billing validation
  - credit validation

> Even if we can't realy separate those in real world it's good to atleast these in mind or introduce separation through namespaces, folders, and projects

'Explicitly define the context within which a model applies... Keep the model strictly consistent within these bounds, but don't be distracted or confused by issues outside.'  

Eric Evans

### Difference of Subdomain and Bounded Context
Subdomain (is a problem space concept):
Is a view on a problem space and how you chose to break down the business or domain activity.

Bounded Context: (is a solution space concept)
How a software and development of that software has been organized 

### Problem Space vs. Solution Space

- Problem Space:
Is a bare floor of a room.

- Solution Space A:
Wall to wall carpet, same shape and size as the problem.

- Solution Space B:
Different Circular sahpe carpte and size compared to problem.

> Solution A and B are both in solution space and they both solve the problem.

Context Map: 
Demonstrates (visualize) how bounded contexts connect to one another while supporting communication between teams.


For shared concepts like customerName and Logging system goes must:
- Teams must communicate to each other and coordinate with other teams about any change to them or not change them at all.
- Usually are kept in Shared Kernel or Customers Core Domain or some set of generic subdomains or even both.

Bounded Contexts in the Application:
- Appointment scheduling
- Client and patient management
- Resource scheduling
these bounded context have isolated Shared Kernel

### Ubiquitous language of a bounded context
The language we use in key to the shared understanding we want to have with our domain experts in order to be successful.

A ubiquitous language applies to a single bounded context and is used throughout conversations and code for that context.

Clients
Person Who: 
- Makes appointments
- Pays the bills
- Brings pets to clinic

Patients
Animal for whom:
Medical records are kept

Appointment
Scheduled time for:
- Surgeries
- Any type of regular office visit

'Recognize that a change in the ubiquitous language is a change in the model.'

Eric Evans

> Be cautios that a change in the ubiquitous language is likely to be a change in the domain model.

### Core Domain
The key differentiator for the customer's business -- something they must do well and cannot outsource.

### Subdomains
Separate applications or features your software must support or interact with.

### Bounded Context
A specific responsibility, with explicit boundaries that separate it from other parts of the system.

### Context Mapping
The process of identifying bounded contexts and their relationships to one another.

### Shared Kernel
Part of the model that is shared by two or more teams. who agree not to change it without collaboration.

### Ubiquitous Language
The language using terms from a domain model that programmers and domain experts use to discuss that particular sub-system.

The ubiquitous language of a bounded context is ubiquitous throughout everything you do in that context - discuss, model, code, etc.

**In code, namespaces are helpful to quickly identify which bounded context you're working in.**
e.g. Scheduling.Notification namespace vs EmailReminder.Notification

## DDD Terms
Entity & Context are Common Software Terms  
Seeing them in diffrent areas can confuse us.

Entity in Defferent Areas:
- Entity Framework Core:
A data model class with a key that is mapped to a table in a database.

- Domain-Driven Design
A domain class with an identity for tracking.

Context in Defferent Areas:
- Entity Framework Core:
A DbContext class provides access to entities and defines how entities map to the database.

- Domain-Driven Design
A Bounded Context defines the scope and boundaries of a subset of a domain.

We must focus on Domain (firs D in DDD) which is most important. So we design based on business rules, not databse perspective.

`[The Domain Layer is] responsible for representing concepts of the business, infromation about the business situation, and business rules. State that reflects the business situation is controlled and used here, even though the technical details of storing it are delegated to the infrastructure. This layer is the heart of business software.`

Eric Evans

> The domain is the heart of business software. (This is the whole point of DDD, focus on domain not technical details of how software will work)

Focus on Behaviours, Not Attributes
Event Storming and using past verbs like 'an appointment is booked' with domain experts using sticky notes on board.  
Another approach is Event Modeling.

### Comparing Anemic and Rich Domain Models
Anemic:
Model with classes focused on state management. Good for CRUD.  
Focused on the state of it's object i.e. simple CRUD. Using anemia in domain model is an anti-pattern.

Findeing anemics in our Domain:
- Looks like the real thing with objects named for nouns in the domain.
- Little or no behavior.
- Equate to property bags with getters and setters.
- All business logic has been relegated to service objects.

Rich:  
Model with logic focused on behavior, not just state. Prefered for DDD.  
Strive for rich domain models
Have an awareness of the strengths and weaknesses of those that are not so rich.
 
### Entity  
A mutable class with an identity (not tied to its property values) used for tracking and persistence.  
Entities Have Identity And Are Mutabale
It is something that we need to be able to track locate, retrieve and store and we do that with an Identity Key (ID), it's properties may change so we can't use it's properties to identify it (as a key, as in Object Value).

e.g.
Appointment: BaseEntity <Guid>
  - ClientId
  - DoctorId
  - PatientId
  - RoomId
  - StartEndTime
Appointment Entity Needs:
  - Retrieve
  - Track
  - Edit
  - Requires Unique ID   

> Client, Patient, Doctor and Room are entities with simple cruds and in this context we only need their Id's and they are Read-Only, thus we don't use DDD on them here.

**User interface should be designed to hide the existence of bounded contexts from end users.**

### Synchronizing Data Across Bounded Contexts
By Pub/Sub pattern, One of services publishes update event and the other one service that is subscribed to it will get notified.

Eventual Consistency
Systems do not need to be strictly synchronized, but the changes will eventually get to their destination.

Message queues are one way to share data across bounded contexts.

### Value Objects  
An immutable class whose identity is dependent on the combination of its values.
In order to compare two value objects we compare all of it's properties and if it all matched you consider two value objects are equal.
They are used as a property of an entity.
They are identified by the composition of their values.

Two Types of Object in DDD
- Defined by an identity (Entity)
- Defined by its values (Value Objects)

Value Object Characteristics:
- Measures, quantifies, or describes a thing in the domain.
- Identity is based on composition of values (rather than ID).
- Immutable (once created, shouldn't be able to change value object and instead should create another instance with the new values)
- Compared using all values
- No side effects (may have methods or behaviour but no side effect, every method on value object should compute things and never change state of the value object or the system since it is immutable)

> Do not confuse DDD Entities & Value Object with .NET Value Types and Reference Types.
> - Value Types: Defined with structs
> - Reference Types: Defined with classes
> - DDD Entities & Value Objects: Typically defined with classes

Commonly used value objects
String is a Value Object: 
  - It is immutable.
  - String methods respect immutability
  Replace(StringA, StringB):
  Returns a new string
  in which all occurences StringA in the current instance are replaced with StringB.
  ToUpper():
  Returns a copy of this string converted to uppercase.
  ToLower():
  Returns a copy of this string converted to lowercase.

  Money Is a Greate Value Object:
  - Number alone doesn't mean anything.
  - Unit alone doesn't mean anything.
  e.g. $ 50,000,000 (Fifty Milion Dollars)
  They are identified when composed together.

  "Dates are a classic value object and there's all kinds of logic with them."

  Eric Evans

  DateTimeRange as Value Object
  Patient Appointment
  10:00 am Jan 4 - 11:00 am Jan 4

  Staff Meeting
  2:00 pm Feb 1 - 3:15 pm Feb 1

  "It may surprise you to learn that we should strive to model using Value Objects instead of Entities wherever possible. Even when a domain concept must be modeled as an Entity, the Entity's design should be biased toward serving as a value container rather than a child Entity container."

  Vaughn Vernon - Implementing Domain Driven Design

  So we should do this when considering Domain Objects:

  Our Instict:
  1. Probably an entity.
  2. Maybe a value object.

  Vaughn Vernon's guidance:
  1. Is this a value object?
  2. Otherwise, an entity.

  Value Objects Can Be Used for Identifiers
  using only int or Guid for client or customer makes it possible to still conflict them with each other but if we create specific Value Object for each Id then it will make them crytal clear.

  e.g.1
  ```C#
  <!-- ClientIdValueObjec.cs -->

  public class ClientId
  {
    public readonly Guid Id;
    public ClientId()
    {
      Id = Guid.NewGuid();
    }
    public ClientId(Guid id)
    {
      Id = id;
    }

    [Other methods like Equality and Hash override code]
  }
  ```
  > Helps to avoid errors in passed parameters

  Usage of ClientId object will get more clear when we are using them:
    e.g.2
    ```C#
    public class SomeService
    {
      public void CreateAppointmentFor(ClientIdValueObject clientId, PatientIdValueObject patientId)
    }
    ```
    These two Id's can never passed substitute of eachother but if we only passed `Int` they might have.

#### Hint. Extract primitives from an entity
Think of primitives in an Entity like int's and string's that can be grouped together into a value object.
It beautifully groups two properties of a Pet into one simple Value Object.
e.g.
We can have AnimalType Value Object that has:
- Species
- Breed


Domain Service
Provide a place in the model to hold behavior that doesn't belong elsewhere in the domain. 

Orchestrates Processes Across Objects.
Usese Entities and Value Objects to get a certain Result.

Features of a Domain Service:
  - Not a natural part of an entity or value object.
  We don't wanna shift our entity behaviours into our services.
  - Has an interface defined in terms of other domain model elements.
  - Stateless, but may have side effects.
  - Lives in the core of the application

#### Infrastructure
Implements interfaces defined in Domain, e.g. Send Email, Log to a File.

#### Immutable
Refers to a type whose state conot be changed once the object has been instantiated.

#### Side Effects
Changes in the state of the application or interaction with the outside world (e.g. infrastructure)
Things that changed other than the operation of the application that we are performing (like sending message to queue)
If we separate operation of querying data from those who change state of it, then if an query operation result change in state then it's considered to have side effect.

## Aggregate 
A transactional graph of objects.
Group of related objects that work with each other in a transaction.

Avoid big ball of mud models, break model up into aggregates.
Aggregate encapsulate business rules and invariants.

e.g. Appointments should not overlap with each other.
Tha aggregate root can't be 'appointment' and must be 'schedule' because in order to find overlaps we need to read other appointments and in this way appointments will be aware of each other which is wrong. 

### Aggregate Root
The entry point of an aggregate which ensures the integrity of the entire graph.

### Invariant
A condition that should always be true for the system to be in a consistent state.

### Associations
The modeled relationship between entities.

### Navigation Properties
An ORM term to describe properties that reference related objects.

### Unidirectional Relationships
Associations between two entities that can only be navigated in one direction.

Default to one-way relationships when modeling associations.

## Repository
A class that encapsulates the data persistence for an aggregate root.

## Specification Pattern
A method of encapsulating a business rule so that it can be passed to other methods which are responsible for applying it.

## Persistence Ignorance
Objects are unaware of where their data comes from or goes to.

## ACID
Atomic, Consistent, Isolated, Durable

## SOLID
A set of five software design patterns:
- Single Responsibility Principle (SOP)
- Open/Closed Principle (OPP)
- Liskov Substitution Principle (LSP)
- Interface Segregation Principle (ISP)
- Dependecy Inversion Principle (DIP)

## Domain Event
A class that captures the occurence of an event in a domain object.

## Hollywood Principle
"Don't call us, we'll call you"

## Anti-Corruption Layer
Functionality that insulates a bounded context and handles interaction with foreign systems or contexts.

## Message Queue
A tool for storing and retrieving messages, often used by applications to communicate with one another in disconnected fashion.

## Service Bus
Software responsible for managing how messages are routed between numerous applications and services.
