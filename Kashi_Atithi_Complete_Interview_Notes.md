# Cognizant GenC Next — Kashi Atithi Complete Interview Notes

**Candidate:** Suyash Giri  
**Project:** Kashi Atithi — Hotel Booking & Management System  
**Target:** Cognizant GenC Next / Software Engineering Interview  
**Basis:** Resume submitted through Superset  
**Format:** Concept → root problem → project use → interview answer → follow-up question → follow-up answer → verification/trap point  
**Primary stack:** Java, Spring Boot 3, Spring Security, JWT, React.js, MySQL, Spring Data JPA, Hibernate, AWS S3 and Maven

---

## Accuracy Labels Used in This Handbook

- **[RESUME-CONFIRMED]** — explicitly present in the submitted resume.
- **[CONCEPT]** — standard technical knowledge that may be asked because the technology is listed.
- **[VERIFY-IN-CODE]** — an implementation detail that the resume alone cannot prove. Check the repository before claiming it.
- **[IMPROVEMENT]** — a technically stronger production approach that may not exist in the current version.

> **Golden rule:** Never convert a general Spring, JWT, JPA or database concept into a personal project claim unless the implementation is actually present in your repository.

> **Speaking rule:** Give the recommended answer first. Continue to the follow-up answer only when the interviewer asks the next question.

---

## Table of Contents

1. [How to Use These Notes](#1-how-to-use-these-notes)
2. [What the Interviewer Sees](#2-what-the-interviewer-sees)
3. [How Much You Should Speak](#3-how-much-you-should-speak)
4. [D-P-R-F Answer Framework](#4-d-p-r-f-answer-framework)
5. [Professional Self-Introduction](#5-professional-self-introduction)
6. [Resume Risk and Verification Checklist](#6-resume-risk-and-verification-checklist)
7. [Kashi Atithi — Interview Introduction](#7-kashi-atithi--interview-introduction)
8. [Kashi Atithi — Business Flow](#8-kashi-atithi--business-flow)
9. [Kashi Atithi — Architecture](#9-kashi-atithi--architecture)
10. [Java Fundamentals Connected to the Project](#10-java-fundamentals-connected-to-the-project)
11. [Spring Framework and Spring Boot 3](#11-spring-framework-and-spring-boot-3)
12. [Beans, IoC and Dependency Injection](#12-beans-ioc-and-dependency-injection)
13. [Spring MVC, Controllers and REST APIs](#13-spring-mvc-controllers-and-rest-apis)
14. [Spring Security Architecture](#14-spring-security-architecture)
15. [JWT Authentication](#15-jwt-authentication)
16. [Security Filter Chain and Role-Based Access](#16-security-filter-chain-and-role-based-access)
17. [Controller-Service-Repository Architecture](#17-controller-service-repository-architecture)
18. [DTOs, Validation and Exception Handling](#18-dtos-validation-and-exception-handling)
19. [Spring Data JPA](#19-spring-data-jpa)
20. [Hibernate ORM](#20-hibernate-orm)
21. [MySQL and Relational Database Design](#21-mysql-and-relational-database-design)
22. [Users, Rooms and Bookings Relationships](#22-users-rooms-and-bookings-relationships)
23. [Booking Conflict Prevention](#23-booking-conflict-prevention)
24. [Transactions, Concurrency and True Double-Booking Safety](#24-transactions-concurrency-and-true-double-booking-safety)
25. [AWS S3 Room Image Storage](#25-aws-s3-room-image-storage)
26. [React Frontend and API Integration](#26-react-frontend-and-api-integration)
27. [Maven and Project Build](#27-maven-and-project-build)
28. [The 10+ REST API Claim](#28-the-10-rest-api-claim)
29. [Ownership, Challenges, Bugs and Testing](#29-ownership-challenges-bugs-and-testing)
30. [Security, Scalability and Improvements](#30-security-scalability-and-improvements)
31. [Dangerous Resume Questions and Safe Answers](#31-dangerous-resume-questions-and-safe-answers)
32. [Rapid-Fire Question Bank](#32-rapid-fire-question-bank)
33. [What Not to Say](#33-what-not-to-say)
34. [Final Repository Verification Sheet](#34-final-repository-verification-sheet)
35. [Official References](#35-official-references)

---

# 1. How to Use These Notes

Do not memorise every paragraph word-for-word. Memorise the **logic, sequence and project connection**.

For every technology, prepare five layers:

1. **One-line meaning** — What is it?
2. **Root problem** — Why was it required?
3. **Project use** — Where was it used in Kashi Atithi?
4. **Follow-up depth** — What can the interviewer ask next?
5. **Honest limitation** — Which detail must be checked in the code?

## Example: Spring Data JPA

**One-line meaning**

> Spring Data JPA reduces repetitive database-access code by providing repository abstractions over JPA.

**Root problem**

> Without it, common database operations require repeated EntityManager or JDBC code.

**Project connection**

> In Kashi Atithi, repositories were used to access Users, Rooms and Bookings stored in MySQL.

**Follow-up depth**

> Repository interfaces, derived query methods, `JpaRepository`, entity state, lazy loading, transactions and custom queries.

**Honest limitation**

> The exact repository names, method signatures and JPQL/native queries must match the actual project.

---

# 2. What the Interviewer Sees

## 2.1 Resume-confirmed project claims

**[RESUME-CONFIRMED]**

Kashi Atithi is described as a full-stack hotel booking and management system using:

- Java;
- Spring Boot 3;
- Spring Security;
- JWT;
- React.js;
- MySQL;
- Spring Data JPA;
- AWS S3;
- Hibernate;
- Maven.

The resume further claims:

- JWT-based authentication;
- role-based access control for Admin and User;
- Spring Security filter chain;
- 10+ REST APIs;
- room management;
- booking operations;
- conflict-prevention logic intended to avoid double bookings;
- AWS S3 room-image storage;
- Users, Rooms and Bookings JPA entities;
- One-to-Many relationships;
- Controller-Service-Repository architecture;
- DTO-based request handling;
- centralised validation.

## 2.2 Why this project is a strong interview project

This project gives the interviewer multiple engineering branches:

- object-oriented Java;
- dependency injection;
- layered architecture;
- HTTP and REST;
- authentication and authorisation;
- JWT security;
- relational database modelling;
- ORM and JPA;
- transactions and concurrency;
- date-range overlap logic;
- file/object storage;
- build management.

## 2.3 Strongest question magnets

The following phrases will attract the deepest questions:

1. **“Spring Security filter chain”**
2. **“JWT-based authentication”**
3. **“conflict prevention logic to avoid double bookings”**
4. **“One-to-Many relationships”**
5. **“DTO-based request handling”**
6. **“centralized validation”**
7. **“AWS S3 for room image storage”**
8. **“Controller-Service-Repository layers”**
9. **“10+ REST APIs”**

For every such claim, expect:

- What does it mean?
- Why did you use it?
- Show the request flow.
- What happens on failure?
- What race condition exists?
- What HTTP status is returned?
- How did you test it?
- What alternative could be used?
- What would you improve for production?

---

# 3. How Much You Should Speak

| Question type | Recommended duration |
|---|---:|
| Definition | 15–25 seconds |
| Concept difference | 25–40 seconds |
| Project-use answer | 30–45 seconds |
| Request-flow explanation | 45–75 seconds |
| Complete architecture | 60–90 seconds |
| Booking-conflict explanation | 60–90 seconds |
| Challenge or bug story | 45–60 seconds |
| “Tell me about the project” | 60–90 seconds |

## The stop rule

After giving:

- the meaning;
- the project use;
- the result;

**stop speaking**.

### Weak behaviour

> “Spring Boot has IoC, DI, AOP, MVC, JPA, Security, Actuator, profiles, auto-configuration, embedded Tomcat, annotations…”

This creates too many unrequested branches.

### Better behaviour

> “Spring Boot is an opinionated framework built on Spring that simplifies application setup through auto-configuration, starter dependencies and an embedded server. In Kashi Atithi, it helped me build and run the REST backend without manually configuring a servlet container and every Spring component.”

Then stop.

---

# 4. D-P-R-F Answer Framework

Use the **D-P-R-F framework**.

## D — Definition

Explain the term in simple language.

## P — Problem

Explain the problem that exists without it.

## R — Resume/project relevance

Connect it to Kashi Atithi.

## F — Finish with the result

State the engineering benefit and stop.

## Example: DTO

> “A DTO is an object designed to transfer only the data required by a request or response. If I directly expose a JPA entity, the API can become tightly coupled to the database model and may accidentally expose fields that should remain internal. In Kashi Atithi, DTO-based request handling separated incoming booking or room data from the persistence entities. This made validation and API contracts easier to control.”

Why this answer works:

- defines DTO;
- explains the root problem;
- connects it to the project;
- explains the benefit;
- does not claim an unverified mapping library.

---

# 5. Professional Self-Introduction

## 5.1 Project-focused introduction

> Good morning, sir/ma’am. My name is Suyash Giri, and I am currently pursuing B.Tech in Computer Science and Engineering from Kalinga Institute of Industrial Technology.
>
> My main interests are backend development, full-stack development and problem solving. I have worked with both Java–Spring Boot and JavaScript-based stacks.
>
> One of my main Java projects is Kashi Atithi, a hotel booking and management system. I built its backend using Spring Boot, Spring Security, JWT, Spring Data JPA and MySQL. The system supports Admin and User roles, room-management APIs, booking operations with date-conflict checks and AWS S3-based room-image storage. I structured the backend using Controller, Service and Repository layers with DTO-based request handling.
>
> Through this project, I developed a practical understanding of authentication, authorisation, relational modelling, ORM, API design and booking-related business logic. I am looking for an opportunity where I can strengthen these fundamentals and contribute to enterprise software development.

## 5.2 Why this version works

- It presents Kashi Atithi as the strongest Java project.
- It mentions the hardest features without explaining everything.
- It creates controlled follow-up branches.
- It does not claim expert-level Spring or production scale.
- It connects the project to enterprise backend skills relevant to GenC Next.

---

# 6. Resume Risk and Verification Checklist

Before the interview, answer every item from the repository.

## 6.1 Project ownership

| Question | Exact answer required |
|---|---|
| Was it individual or team-based? | State truth clearly |
| What did you personally implement? | Modules and files |
| Was it built from a tutorial? | Explain what you understood/modified |
| What was your hardest contribution? | One genuine story |

## 6.2 Authentication and security

| Item | Verify in code |
|---|---|
| Login endpoint | Exact route |
| Registration endpoint | Exact route |
| Password hashing | BCrypt or another encoder? |
| JWT library | Exact dependency |
| JWT claims | Subject, role, expiry, etc. |
| Secret/key source | Environment/configuration |
| Token location | Authorization header/cookie/local storage |
| Filter class | Exact class name |
| Filter position | Before which Spring Security filter? |
| User lookup | `UserDetailsService` or custom logic? |
| Role representation | `ROLE_ADMIN`, `ADMIN`, enum, database value? |
| Access rules | Exact routes and roles |
| Stateless session | Configured or not? |
| Refresh token | Implemented or not? |

## 6.3 Booking conflict

| Item | Verify in code |
|---|---|
| Check-in type | `LocalDate`, `LocalDateTime`, SQL date? |
| Check-out type | Exact type |
| Overlap condition | Exact query/formula |
| Boundary rule | Same-day checkout/check-in allowed? |
| Cancelled bookings | Excluded from conflict query? |
| Room status | Used or not? |
| Transaction | `@Transactional` present? |
| Concurrency locking | Pessimistic/optimistic/none? |
| Database protection | Unique/index/constraint? |
| HTTP conflict status | 409 or another code? |

## 6.4 JPA and MySQL

| Item | Verify in code |
|---|---|
| Entity names | User, Room, Booking or exact names |
| Table names | Exact table names |
| Primary-key strategy | IDENTITY/SEQUENCE/AUTO |
| Relationships | Exact annotations |
| Owning side | Which entity has foreign key? |
| Cascade | Exact cascade settings |
| Fetch type | Exact lazy/eager configuration |
| Repository interfaces | Exact names |
| Custom queries | JPQL/native/derived methods |
| Indexes | Defined or absent |

## 6.5 AWS S3

| Item | Verify in code |
|---|---|
| AWS SDK version | v1 or v2 |
| Bucket | Configured how? |
| Object key | Naming pattern |
| Stored database value | URL, key or both |
| Upload path | Backend or presigned URL? |
| Content validation | File type/size |
| Delete old image | Implemented or not? |
| Credentials | Environment variables/IAM role/profile |
| Public access | Public URL/presigned URL/CloudFront? |

## 6.6 API and validation

| Item | Verify in code |
|---|---|
| Number of actual APIs | Count them |
| DTO classes | Exact names |
| Validation annotations | `@NotBlank`, `@Future`, etc. |
| `@Valid` usage | Exact controller parameters |
| Exception handler | `@ControllerAdvice` class |
| Error response format | Exact fields |
| Status codes | Exact codes |

---

# 7. Kashi Atithi — Interview Introduction

## 7.1 30-second answer

> Kashi Atithi is a full-stack hotel booking and management system with User and Admin roles. The backend is built using Java and Spring Boot, secured using Spring Security and JWT, and connected to MySQL through Spring Data JPA and Hibernate. Users can view rooms and create bookings, while admins manage room-related operations. I also added booking-date conflict checks to prevent overlapping reservations and used AWS S3 for room-image storage.

## 7.2 60–90 second answer

> Kashi Atithi is a full-stack hotel booking and management system designed around two roles: User and Admin.
>
> The frontend is built with React, while the backend uses Java and Spring Boot 3. Spring Security and JWT protect the APIs. After a user logs in, the backend generates or validates a token, and the Spring Security filter chain authenticates protected requests before role-based access rules are applied.
>
> The backend contains more than ten REST APIs for authentication, room management and booking operations. MySQL stores Users, Rooms and Bookings, and Spring Data JPA with Hibernate maps Java entities to relational tables. Booking logic checks whether the requested date range overlaps an existing active booking for the same room before creating a new booking.
>
> Room images are stored in AWS S3 rather than inside the database, while the database stores the related object key or URL. I organised the backend into Controller, Service and Repository layers and used DTOs and validation to keep API contracts separate from persistence entities.
>
> This project gave me practical experience in secure API development, relational database design, ORM, layered architecture and business-rule implementation.

## 7.3 What this answer intentionally does not claim

It does not automatically claim:

- refresh-token rotation;
- payment gateway integration;
- pessimistic database locking;
- distributed transactions;
- automated test coverage;
- production-level S3 access policy;
- millions of users;
- microservices.

---

# 8. Kashi Atithi — Business Flow

## 8.1 User flow

A typical User flow may be:

1. Register an account.
2. Log in and receive authentication state/token.
3. Browse available rooms.
4. View room details and image.
5. Select check-in and check-out dates.
6. Submit a booking request.
7. Backend validates dates and checks overlap.
8. Booking is created when no conflict exists.
9. User views or cancels bookings, if implemented.

**[VERIFY-IN-CODE]** Confirm registration, cancellation, filtering and booking-history features.

## 8.2 Admin flow

A typical Admin flow may be:

1. Log in as Admin.
2. Pass authentication and admin-role checks.
3. Create a room.
4. Upload room image to S3.
5. Update room details.
6. Delete/deactivate a room.
7. View room or booking management data.

**[VERIFY-IN-CODE]** Prepare the exact admin permissions.

## 8.3 Business-flow answer

> “The User role handles customer operations such as browsing rooms and creating bookings. The Admin role handles management operations such as adding or updating rooms. The important backend rule is that role checks protect management APIs and booking creation is allowed only after validating the requested dates and checking for overlap with active bookings for the selected room.”

---

# 9. Kashi Atithi — Architecture

## 9.1 High-level architecture

```text
User / Admin
     ↓
React Frontend
     ↓ HTTP + JSON
Spring Security Filter Chain
     ↓
JWT Authentication Filter
     ↓
Controller Layer
     ↓
DTO Validation
     ↓
Service Layer / Business Logic
     ↓
Repository Layer
     ↓
Spring Data JPA + Hibernate
     ↓
MySQL

Room image operation:
Service → AWS SDK → S3 Bucket
```

## 9.2 Request flow: protected room creation

```text
Admin sends POST /rooms
        ↓
Security filter reads JWT
        ↓
Token verified
        ↓
Authentication stored in SecurityContext
        ↓
Authorisation rule checks ADMIN role
        ↓
Controller receives validated DTO/file
        ↓
Service applies business logic
        ↓
Image uploaded to S3
        ↓
Room entity saved through repository
        ↓
Response DTO returned
```

## 9.3 Request flow: booking creation

```text
User sends roomId + checkIn + checkOut
        ↓
JWT authentication and USER access check
        ↓
DTO validation
        ↓
Room existence check
        ↓
Date validity check
        ↓
Repository searches overlapping bookings
        ↓
Conflict found? ── Yes → Reject request
        │
        No
        ↓
Create Booking entity
        ↓
Save in transaction
        ↓
Return booking response
```

## 9.4 Architecture answer

> “The React client communicates with a Spring Boot REST backend. Protected requests first pass through Spring Security’s filter chain, where the JWT is extracted and verified. Once the authenticated user is available in the SecurityContext, route-level rules decide whether User or Admin access is allowed. Controllers receive HTTP input through DTOs, the Service layer applies business rules such as room availability checks, and repositories access MySQL through Spring Data JPA and Hibernate. S3 is used separately for room-image objects.”

---

# 10. Java Fundamentals Connected to the Project

## 10.1 Why Java for the backend?

### Recommended answer

> “Java provides strong typing, object-oriented design, mature libraries and a large enterprise ecosystem. In Kashi Atithi, Java classes modelled entities, DTOs, services, repositories and security components. Spring Boot then provided the framework around those classes for dependency injection, REST APIs, security and database integration.”

## 10.2 What does strong/static typing mean?

Java checks declared types during compilation.

```java
int roomCapacity = 2;
roomCapacity = "two"; // compilation error
```

Benefits:

- catches many mistakes earlier;
- clear method contracts;
- safer refactoring;
- useful IDE support.

## 10.3 Object-Oriented Programming in Kashi Atithi

### Encapsulation

Keep data and operations inside classes with controlled access.

Project examples:

- private entity fields;
- service methods that apply booking rules;
- DTOs exposing only required API data.

### Abstraction

Expose essential behaviour without requiring the caller to know internal details.

Example:

```java
bookingService.createBooking(request, currentUserId);
```

The controller does not need to know every repository query and date check.

### Inheritance

Possible framework examples include extending provided classes or implementing interfaces. However, application entities should not use inheritance without a real domain reason.

### Polymorphism

A repository interface can be used through a Spring-generated implementation. Service dependencies can be expressed through interfaces, and different implementations can satisfy the same contract.

## 10.4 Interface vs class

### Interface

Defines a contract.

```java
public interface StorageService {
    String upload(MultipartFile file);
}
```

### Class

Provides state and/or implementation.

```java
public class S3StorageService implements StorageService {
    // implementation
}
```

### Project answer

> “Interfaces are useful when I want a clear contract and the ability to replace an implementation. For example, a storage interface could allow S3 storage today and another provider later. I should only claim this abstraction if the project actually uses it.”

## 10.5 Checked vs unchecked exceptions

### Checked exception

Must be caught or declared, such as some I/O exceptions.

### Unchecked exception

Extends `RuntimeException`; commonly used for business or validation failures in Spring applications.

Potential project exceptions:

- RoomNotFoundException;
- BookingConflictException;
- UnauthorizedOperationException.

**[VERIFY-IN-CODE]** Prepare exact custom exceptions.

## 10.6 `equals()` and `hashCode()` with JPA entities

This can become tricky because entity IDs may be null before persistence and relationships may be lazily loaded.

Safe answer:

> “Entity equality must be designed carefully. Automatically including all fields and relationships in `equals`, `hashCode` or `toString` can cause recursion, lazy loading or unstable behaviour. I would verify how Lombok or manual methods are used in the actual entities.”

## 10.7 Collections used in relationships

One user may have many bookings, commonly represented as:

```java
List<Booking> bookings;
```

Possible follow-ups:

- `List` vs `Set`;
- duplicate handling;
- ordering;
- lazy collection;
- bidirectional relationship recursion.

---

# 11. Spring Framework and Spring Boot 3

## 11.1 What is the Spring Framework?

Spring is an application framework for Java. Its core provides an IoC container and dependency injection. Its modules support web applications, data access, security, testing and other enterprise concerns.

## 11.2 What is Spring Boot?

Spring Boot builds on Spring and simplifies configuration and application startup.

It commonly provides:

- auto-configuration;
- starter dependencies;
- embedded web server;
- production-oriented configuration conventions;
- executable JAR packaging;
- externalised configuration.

### Interview answer

> “Spring is the broader Java application framework, while Spring Boot simplifies building and running Spring applications through auto-configuration, starter dependencies and an embedded server. In Kashi Atithi, Spring Boot reduced manual setup for REST controllers, security, JPA and the web server.”

## 11.3 What is auto-configuration?

Auto-configuration means Spring Boot attempts to configure common components based on:

- dependencies available on the classpath;
- properties;
- existing beans;
- application type.

Example:

If web starter dependencies exist, Spring Boot configures common MVC and server infrastructure. If JPA and a database driver exist with datasource properties, it configures database-related components.

### Important correction

Auto-configuration does not mean “Spring magically knows everything.” It provides conditional defaults that can be overridden.

### Interview answer

> “Auto-configuration creates sensible default beans when the required classes and properties are present and the developer has not already provided an alternative. It reduces boilerplate but remains customisable.”

## 11.4 What is a starter dependency?

A starter is a curated group of related dependencies.

Examples:

- `spring-boot-starter-web`;
- `spring-boot-starter-data-jpa`;
- `spring-boot-starter-security`;
- `spring-boot-starter-validation`.

### Root problem

Without starters, developers would manually choose many compatible libraries and versions.

### Answer

> “A starter groups commonly required dependencies for one capability. For example, the web starter brings the components needed for a Spring MVC application, while the data JPA starter provides JPA integration.”

## 11.5 Embedded server

A traditional deployment might package an application for an external application server.

Spring Boot can package an executable JAR with an embedded server.

```bash
java -jar application.jar
```

### Project relevance

> “The backend could run as a standalone application without manually deploying a WAR to a separate Tomcat installation.”

**[VERIFY-IN-CODE]** Check packaging and deployment command.

## 11.6 `@SpringBootApplication`

It combines important configuration behaviour, commonly including:

- configuration declaration;
- component scanning;
- auto-configuration.

### Component scanning

Spring searches configured packages for components such as:

- `@Controller`;
- `@RestController`;
- `@Service`;
- `@Repository`;
- `@Component`.

### Common follow-up

Why should the main application class be near the root package?

> “Because component scanning begins from its package by default. Placing it above application packages allows Spring to discover controllers, services and repositories.”

---


## 11.7 Interview Follow-Up Ladder — Spring vs Spring Boot

### Follow-up 1: If Spring Boot is built on Spring, what exactly does Spring Boot add?

> “Spring provides the core container and programming model, including dependency injection, beans, MVC and transaction support. Spring Boot adds opinionated defaults, auto-configuration, starter dependencies, embedded-server support and production-oriented conventions. It does not replace Spring; it reduces the setup required to use Spring effectively.”

### Follow-up 2: What does “opinionated” mean?

> “Opinionated means the framework selects sensible default choices when multiple configurations are possible. For example, when the correct web dependencies are present, Spring Boot can configure a typical web application setup automatically. I can override those defaults when the project requires different behaviour.”

### Follow-up 3: Does Spring Boot generate all code automatically?

> “No. It automates configuration, not business logic. I still write entities, DTOs, controllers, services, repositories, security rules and booking-conflict logic. Spring Boot mainly reduces infrastructure boilerplate.”

### Follow-up 4: What happens when you run a Spring Boot application?

> “The `main` method calls `SpringApplication.run`. Spring Boot creates the application context, scans configured packages, creates eligible beans, applies auto-configuration and starts the embedded server for a web application.”

### Follow-up 5: What is auto-configuration based on?

> “It is conditional. Spring Boot examines the classpath, existing beans, application properties and application type. It configures something only when the required conditions are satisfied.”

### Follow-up 6: Can auto-configuration be overridden?

> “Yes. I can define my own bean, change application properties or exclude a particular auto-configuration. Spring Boot defaults are convenient, but they are not compulsory.”

### Follow-up 7: Why use Spring Boot 3 specifically?

> “The project was built on Spring Boot 3 according to my resume. A practical implication is that the ecosystem uses modern Spring APIs and Jakarta namespaces. In the interview I would avoid claiming a specific minor-version feature unless I verify the project’s `pom.xml`.”

### Follow-up 8: What is an embedded server?

> “The web server is packaged and started as part of the application, instead of requiring me to deploy the project manually into a separately installed server. This made the Kashi Atithi backend runnable through the application’s main class.”

### Follow-up 9: Is Spring Boot only for REST APIs?

> “No. It can support web applications, scheduled jobs, messaging, batch processing and other application types. In Kashi Atithi, its main role was building the REST backend.”

### Follow-up 10: What would break if component scanning did not find a class?

> “Spring would not create that class as a managed bean. A dependent controller or service could then fail during application startup because the required dependency was unavailable.”

---

# 12. Beans, IoC and Dependency Injection

## 12.1 What is a Spring bean?

A bean is an object created, configured and managed by the Spring container.

Examples:

- controller;
- service;
- repository proxy;
- password encoder;
- security filter;
- S3 client.

## 12.2 What is IoC?

IoC means Inversion of Control.

Without IoC, application code manually creates and controls dependencies:

```java
BookingRepository repo = new BookingRepositoryImpl();
BookingService service = new BookingService(repo);
```

With Spring, the container creates the objects and wires them together.

### Simple meaning

> “The responsibility for creating and connecting application objects is inverted from my business classes to the Spring container.”

## 12.3 What is dependency injection?

Dependency injection is the mechanism through which an object receives the dependencies it requires instead of constructing them itself.

```java
@Service
public class BookingService {
    private final BookingRepository bookingRepository;

    public BookingService(BookingRepository bookingRepository) {
        this.bookingRepository = bookingRepository;
    }
}
```

Spring provides the repository dependency.

## 12.4 Why constructor injection?

Benefits:

- required dependencies are explicit;
- fields can be final;
- easier unit testing;
- prevents partially initialised objects;
- avoids hidden field injection.

### Answer

> “I prefer constructor injection because the dependencies required by the class are visible in its constructor and can be supplied easily in tests. It also allows immutable final references.”

**[VERIFY-IN-CODE]** State the injection style actually used.

## 12.5 Bean scope

Default Spring bean scope is singleton within the application context.

### Important

Singleton does not automatically make mutable fields thread-safe.

Controllers and services should generally avoid request-specific mutable instance state.

### Follow-up answer

> “Because a service bean can be shared across requests, I keep request data inside method-local variables rather than mutable service fields.”

## 12.6 `@Component`, `@Service`, `@Repository`, `@RestController`

All register managed components, but communicate purpose:

| Annotation | Intended role |
|---|---|
| `@Component` | General component |
| `@Service` | Business/service logic |
| `@Repository` | Persistence/data access |
| `@RestController` | HTTP REST controller |

`@Repository` also participates in persistence exception translation.

---


## 12.7 Interview Follow-Up Ladder — Beans, IoC and DI

### Follow-up 1: What is the simplest definition of a Spring bean?

> “A Spring bean is an object whose creation and lifecycle are managed by the Spring container.”

### Follow-up 2: Why not create dependencies using `new` inside every class?

> “Direct object creation tightly couples a class to a specific implementation and makes testing or replacement harder. Dependency injection provides the required object from outside, so the class focuses on its own responsibility.”

### Follow-up 3: Give a Kashi Atithi example of dependency injection.

> “A `BookingController` depends on a booking service, and the booking service depends on booking and room repositories. Spring provides these dependencies rather than each layer constructing them manually.”

**[VERIFY-IN-CODE]** Use the actual class and interface names from the repository.

### Follow-up 4: Why is constructor injection preferred?

> “Constructor injection makes required dependencies explicit, supports immutable fields and allows straightforward unit testing. It also prevents creating a valid object without its required dependencies.”

### Follow-up 5: What is IoC?

> “Inversion of Control means object creation and wiring are controlled by the framework container instead of being manually controlled throughout the application code.”

### Follow-up 6: IoC and dependency injection—are they the same?

> “IoC is the broader principle of transferring control to the framework. Dependency injection is the mechanism Spring commonly uses to provide objects and implement that principle.”

### Follow-up 7: What happens if two beans implement the same interface?

> “Spring may not know which implementation to inject. I can resolve that with a primary bean, a qualifier or a more specific configuration.”

### Follow-up 8: Why annotate services and repositories separately when all are beans?

> “The stereotypes communicate architectural intent. `@Service` represents business logic, `@Repository` represents persistence access and can participate in persistence-exception translation, while `@RestController` represents the web API boundary.”

### Follow-up 9: Are Spring singleton beans thread-safe automatically?

> “No. Singleton means one bean instance per application context by default; it does not guarantee thread safety. Service beans should generally avoid mutable request-specific fields.”

### Follow-up 10: Where should request-specific data be stored?

> “Usually in method-local variables, DTOs or request/security context—not in mutable fields of a shared singleton service.”

---

# 13. Spring MVC, Controllers and REST APIs

## 13.1 What is Spring MVC?

Spring MVC is the web framework used to map HTTP requests to controller methods and generate responses.

## 13.2 What is `@RestController`?

`@RestController` marks a class whose handler methods return response data, commonly JSON, rather than resolving a server-rendered view.

### Example

```java
@RestController
@RequestMapping("/api/rooms")
public class RoomController {

    @GetMapping
    public List<RoomResponse> getRooms() {
        return roomService.getRooms();
    }
}
```

### Project answer

> “REST controllers received room, booking and authentication requests, delegated business logic to services and returned response DTOs with suitable status codes.”

## 13.3 Mapping annotations

| Annotation | HTTP method |
|---|---|
| `@GetMapping` | GET |
| `@PostMapping` | POST |
| `@PutMapping` | PUT |
| `@PatchMapping` | PATCH |
| `@DeleteMapping` | DELETE |

## 13.4 `@PathVariable`, `@RequestParam`, `@RequestBody`

### `@PathVariable`

Identifies a resource:

```text
GET /api/rooms/42
```

### `@RequestParam`

Supplies filters or optional values:

```text
GET /api/rooms?capacity=2&available=true
```

### `@RequestBody`

Maps JSON request data to a DTO:

```json
{
  "roomId": 42,
  "checkIn": "2026-08-10",
  "checkOut": "2026-08-13"
}
```

## 13.5 `ResponseEntity`

`ResponseEntity` allows explicit control over:

- status;
- headers;
- body.

Example:

```java
return ResponseEntity.status(HttpStatus.CREATED).body(response);
```

### Interview answer

> “I use `ResponseEntity` when the endpoint needs explicit status or headers, such as returning 201 after a booking is created or 409 when a date conflict exists.”

## 13.6 Common status codes for Kashi Atithi

| Code | Meaning | Project example |
|---:|---|---|
| 200 | Success | Room details returned |
| 201 | Created | Room or booking created |
| 204 | Success with no body | Delete operation |
| 400 | Invalid request | Invalid date order |
| 401 | Authentication missing/invalid | Invalid JWT |
| 403 | Authenticated but forbidden | User calls Admin API |
| 404 | Not found | Room ID does not exist |
| 409 | Conflict | Overlapping booking |
| 500 | Unexpected server failure | Unhandled error |

---


## 13.7 Interview Follow-Up Ladder — Spring MVC and REST

### Follow-up 1: What happens after a request reaches a Spring Boot REST API?

> “The embedded server receives the HTTP request. It passes through configured filters, including security filters. Spring MVC then finds the matching controller method, converts request data into Java values or DTOs, invokes the method and serialises the returned object into the HTTP response.”

### Follow-up 2: Why should a controller remain thin?

> “A controller should translate HTTP input and output, not contain complex booking rules. Keeping business logic in the service layer makes it reusable, testable and independent of the transport layer.”

### Follow-up 3: What is the difference between `@Controller` and `@RestController`?

> “`@Controller` is commonly used for MVC views. `@RestController` combines controller behaviour with response-body serialisation, so returned objects are written directly to the HTTP response, usually as JSON.”

### Follow-up 4: How does JSON become a Java DTO?

> “Spring uses an HTTP message converter, commonly backed by Jackson, to deserialize the JSON request body into the declared Java type. Validation can then run on that DTO.”

### Follow-up 5: What if JSON contains an invalid date or number?

> “Deserialization can fail before the controller logic completes. A global exception handler should convert that failure into a clear client error response rather than exposing an internal stack trace.”

### Follow-up 6: Why use `ResponseEntity`?

> “It gives explicit control over the status code, headers and body. For example, a successful room creation can return 201, while a booking date conflict can return 409.”

### Follow-up 7: Path variable or query parameter for room ID?

> “A path variable is appropriate when the value identifies the target resource, such as `/rooms/15`. Query parameters are better for optional filtering or pagination, such as `/rooms?city=Varanasi&page=0`.”

### Follow-up 8: What is content negotiation?

> “It is the process of selecting the response representation based on HTTP headers and supported formats. In this project the APIs primarily exchanged JSON.”

### Follow-up 9: Why should an API not always return 200?

> “HTTP status codes communicate the result semantically. Returning 200 for authentication failure, validation error and conflict makes clients harder to build and debug.”

### Follow-up 10: How do you make REST APIs consistent?

> “Use consistent resource naming, DTOs, validation, status codes, error-body structure, pagination conventions and authentication behaviour across all controllers.”

---

# 14. Spring Security Architecture

## 14.1 What is Spring Security?

Spring Security is a framework for authentication, authorisation and protection against common web attacks.

### Project answer

> “In Kashi Atithi, Spring Security protected backend APIs. A JWT authentication filter examined protected requests, and role-based rules restricted management endpoints to Admin while allowing appropriate booking operations for authenticated Users.”

## 14.2 Authentication vs authorisation

### Authentication

Who are you?

Example: verifying username/password or a JWT.

### Authorisation

What are you allowed to do?

Example: only Admin can create a room.

### Final answer

> “Login and JWT verification establish identity. Role rules then decide whether that authenticated identity can access a particular endpoint.”

## 14.3 SecurityContextHolder

Spring Security stores the current authenticated principal in a `SecurityContext`, accessed through `SecurityContextHolder` during request processing.

Conceptual flow:

```text
JWT verified
    ↓
Authentication object created
    ↓
Stored in SecurityContext
    ↓
Controllers/security rules access current user
```

## 14.4 Authentication object

It typically represents:

- principal/current user;
- credentials during authentication;
- granted authorities/roles;
- authenticated status.

## 14.5 GrantedAuthority

A `GrantedAuthority` represents an authority assigned to the authenticated principal, such as:

```text
ROLE_USER
ROLE_ADMIN
```

**[VERIFY-IN-CODE]** Prepare the exact naming convention.

## 14.6 UserDetails and UserDetailsService

### `UserDetails`

Represents user information required by Spring Security.

### `UserDetailsService`

Loads user details, commonly by username or email.

Possible flow:

```text
Email from JWT subject
    ↓
UserDetailsService.loadUserByUsername(email)
    ↓
User loaded from database
    ↓
Authorities attached
```

**[VERIFY-IN-CODE]** Some implementations put claims directly into authentication without querying every request.

## 14.7 PasswordEncoder

Passwords should not be stored as plain text.

Spring Security commonly uses a password encoder such as BCrypt.

### Hashing vs encryption

- Encryption is reversible with a key.
- Password hashing is designed to be one-way.

### Salting

A salt ensures identical passwords do not necessarily result in identical stored hashes.

### Answer

> “During registration, the password should be encoded before storage. During login, the provided password is checked against the stored hash through the password encoder rather than decrypting the password.”

**[VERIFY-IN-CODE]** Confirm the encoder.

---

# 15. JWT Authentication

## 15.1 What is JWT?

JWT stands for JSON Web Token.

It is a compact, URL-safe representation of claims that can be digitally signed or integrity-protected.

A common signed JWT has three encoded sections:

```text
header.payload.signature
```

## 15.2 Header

Describes token metadata such as:

- token type;
- signing algorithm.

## 15.3 Payload

Contains claims.

Common claims:

- `sub` — subject, often user identity;
- `iat` — issued-at time;
- `exp` — expiration time;
- role/authorities — custom claims, if used.

### Important security fact

The payload is encoded, not automatically secret. Sensitive data should not be placed in it merely because it appears unreadable.

## 15.4 Signature

The signature allows the server to detect token tampering.

If a user modifies the payload without the signing key, verification should fail.

### Signed does not mean encrypted

A signed JWT provides integrity/authenticity. It does not by itself hide the payload.

## 15.5 Typical Kashi Atithi flow

```text
User submits email + password
        ↓
AuthenticationManager verifies credentials
        ↓
JWT service generates signed token
        ↓
Frontend stores/uses token
        ↓
Frontend sends Authorization: Bearer <token>
        ↓
JWT filter extracts and validates token
        ↓
Authentication stored in SecurityContext
        ↓
Protected endpoint continues
```

**[VERIFY-IN-CODE]** Match the actual implementation.

## 15.6 Why JWT?

### Suggested answer

> “JWT can support stateless API authentication because the server verifies a signed token on each request instead of relying on a server-side HTTP session. This fits a separate React client and Spring REST backend.”

### Important nuance

Stateless does not mean the overall system stores no state. Users, roles, revoked tokens and other application data still exist.

## 15.7 Token extraction

A common request header is:

```http
Authorization: Bearer eyJ...
```

The filter typically:

1. checks the header;
2. verifies the `Bearer ` prefix;
3. extracts the token;
4. validates signature and expiry;
5. obtains user identity;
6. sets authentication if valid.

## 15.8 Token validation

Validation should consider:

- correct signature;
- expiration;
- expected issuer/audience, if used;
- subject existence;
- token type;
- current user/account state, depending on design.

## 15.9 Access token vs refresh token

### Access token

- short-lived;
- sent to protected APIs;
- greater exposure frequency.

### Refresh token

- used to obtain a new access token;
- should be protected more carefully;
- often stored and rotated/revoked according to the architecture.

**[VERIFY-IN-CODE]** Do not claim refresh tokens unless implemented.

## 15.10 Where should the frontend store the token?

This involves trade-offs.

### Local storage

- easy to use;
- accessible to JavaScript;
- XSS can expose it.

### HttpOnly secure cookie

- unavailable to frontend JavaScript;
- reduces direct token theft through XSS;
- requires CSRF and cookie configuration considerations.

### Safe answer

> “The best storage depends on the threat model and architecture. I will describe what Kashi Atithi actually uses, and for production I would evaluate HttpOnly secure cookies, CSRF protection, token lifetime and refresh-token rotation.”

## 15.11 JWT logout problem

With purely stateless tokens, deleting the token from the browser does not invalidate a stolen token before expiry.

Possible improvements:

- short expiry;
- refresh-token revocation;
- token denylist;
- key rotation;
- account/session version checks.

---

# 16. Security Filter Chain and Role-Based Access

## 16.1 What is a filter?

A Servlet filter runs before or around the controller and can inspect or modify a request/response.

Spring Security integrates through filters.

## 16.2 What is `SecurityFilterChain`?

A `SecurityFilterChain` bean defines how HTTP requests are secured.

It may configure:

- public routes;
- authenticated routes;
- role restrictions;
- CSRF behaviour;
- session policy;
- authentication providers;
- custom JWT filter position;
- exception handling.

### Interview answer

> “The SecurityFilterChain defines security rules for request paths and places the custom JWT filter into Spring Security’s processing chain. Public authentication endpoints are permitted, while room-management or booking endpoints require authentication and role-specific access.”

## 16.3 Why filter order matters

The authorisation step needs an authenticated principal.

Therefore, the JWT filter should establish authentication before the framework makes final access decisions.

A common configuration is:

```java
http.addFilterBefore(jwtFilter, UsernamePasswordAuthenticationFilter.class);
```

**[VERIFY-IN-CODE]** Memorise actual filter and position.

## 16.4 `OncePerRequestFilter`

A custom JWT filter often extends `OncePerRequestFilter` to run once for each request dispatch under its intended behaviour.

Possible steps:

1. read header;
2. skip when no Bearer token;
3. parse subject;
4. validate token;
5. load authorities;
6. set `SecurityContext`;
7. continue chain.

## 16.5 Stateless session management

For JWT API security, applications commonly configure stateless session management.

Meaning:

- server does not rely on HTTP session authentication state;
- each protected request supplies authentication proof.

### Answer

> “With stateless JWT authentication, each request carries the token and the server reconstructs authentication for that request. It does not depend on a stored server-side login session.”

## 16.6 Role-based access control

Example rules:

- `/api/auth/**` — public;
- room listing — public or authenticated, depending on implementation;
- booking creation — User;
- room creation/update/delete — Admin;
- management endpoints — Admin.

**[VERIFY-IN-CODE]** Do not invent route matchers.

## 16.7 `hasRole` vs `hasAuthority`

`hasRole("ADMIN")` commonly expects an authority with `ROLE_ADMIN` prefix.

`hasAuthority("ADMIN")` checks the exact authority string.

### Common bug

Database stores `ADMIN`, but configuration and conversion create mismatched prefixes.

### Answer

> “I need to keep role naming consistent across the database, `GrantedAuthority` conversion and security rules. Otherwise, a valid Admin can incorrectly receive 403.”

## 16.8 401 vs 403

- **401 Unauthorized:** authentication is missing or invalid.
- **403 Forbidden:** identity is authenticated but lacks permission.

### Project example

- expired token → 401;
- valid User token calling Admin room-creation API → 403.

## 16.9 Method-level security

Annotations such as `@PreAuthorize` can protect individual methods.

Example:

```java
@PreAuthorize("hasRole('ADMIN')")
```

**[VERIFY-IN-CODE]** State whether URL-based rules, method security or both are used.

---


## 16.10 Interview Follow-Up Ladder — Spring Security, JWT and Filter Chain

### Follow-up 1: Explain the complete protected-request flow.

> “The client sends the JWT, commonly in the `Authorization` header. A custom authentication filter reads it, validates its signature and expiry, obtains the user identity and authorities and places an authenticated object in the `SecurityContext`. The authorisation rules then decide whether that role can access the endpoint. If permitted, the request reaches the controller.”

### Follow-up 2: Why is the filter placed before `UsernamePasswordAuthenticationFilter`?

> “For token-based authentication, the application wants the security context established before later authorisation processing. The exact placement ensures the JWT filter runs at the intended stage of the chain.”

**[VERIFY-IN-CODE]** Name the actual filter class and its configured position.

### Follow-up 3: What does stateless authentication mean?

> “The server does not keep a traditional login session for each request. Every protected request carries the token required to reconstruct the authenticated identity.”

### Follow-up 4: Is JWT encrypted?

> “Not by default. A standard signed JWT protects integrity, but its payload can be decoded. Sensitive secrets should not be placed in the payload.”

### Follow-up 5: What does the JWT signature prove?

> “It helps prove that the token content has not been changed and that it was signed by a party holding the expected key. It does not hide the payload.”

### Follow-up 6: What claims should you verify?

> “At minimum, the application should verify the signature and expiration. Depending on the design, it may also verify issuer, audience and other constraints.”

### Follow-up 7: Why do you still load a user after parsing the token?

> “Loading current user details allows the application to obtain current roles, account status and other security information instead of trusting every detail indefinitely from an old token. The exact approach depends on the implementation.”

### Follow-up 8: What is `SecurityContext`?

> “It stores the authenticated principal and authorities for the current security processing context, allowing later layers to know who is making the request.”

### Follow-up 9: `hasRole("ADMIN")` vs `hasAuthority("ROLE_ADMIN")`?

> “`hasRole` commonly applies the `ROLE_` prefix convention, while `hasAuthority` compares the authority string more directly. The stored and configured values must be consistent.”

### Follow-up 10: What happens when a token is missing?

> “A public endpoint may continue anonymously. A protected endpoint should ultimately return an authentication failure, normally 401.”

### Follow-up 11: What happens when the user is authenticated but has the wrong role?

> “The request should be rejected as forbidden, normally with 403.”

### Follow-up 12: How is password authentication different from token validation?

> “At login, credentials are verified and a token may be issued. On later requests, the password is not sent again; the token is validated to authenticate the request.”

### Follow-up 13: How should passwords be stored?

> “Passwords should be stored using a one-way adaptive password hash such as BCrypt, never as plaintext or reversible encryption.”

### Follow-up 14: How do you log out with JWT?

> “For a simple short-lived access-token design, the client removes the token, but that does not revoke a stolen valid token. Stronger designs use short expiration, refresh-token rotation and server-side revocation or token-version strategies.”

### Follow-up 15: Where should a browser store JWT?

> “There is a trade-off. JavaScript-accessible storage is exposed to XSS, while cookies require secure flags and CSRF considerations. I should explain the project’s actual choice and acknowledge its security trade-off rather than claim one location is universally perfect.”

### Follow-up 16: Can frontend role checks secure an admin API?

> “No. They only control visible UI. The Spring Security backend must enforce the role because a user can send an HTTP request without using the frontend.”

### Follow-up 17: Why use Spring Security instead of writing checks in every controller?

> “It centralises authentication and authorisation in a consistent filter and policy system, reducing duplicated and easily forgotten security checks.”

### Follow-up 18: What security details must be verified from your code?

> “The token header format, signing algorithm, secret/key source, expiry, claims, filter class, exception handling, password encoder, public routes, role strings and CORS configuration.”

---

# 17. Controller-Service-Repository Architecture

## 17.1 Why layers?

Without layers, one controller can contain:

- HTTP parsing;
- validation;
- business rules;
- SQL/JPA queries;
- S3 upload;
- response formatting.

This creates tightly coupled, difficult-to-test code.

## 17.2 Controller layer

Responsibilities:

- map HTTP endpoints;
- receive DTOs/path parameters;
- trigger validation;
- call service methods;
- return status and response.

It should avoid containing complex booking rules.

## 17.3 Service layer

Responsibilities:

- business rules;
- transactions;
- coordinating repositories;
- conflict checks;
- mapping between DTO and entity, depending on design;
- coordinating S3 and database work.

Example:

```text
createBooking()
  → verify room
  → validate dates
  → detect overlap
  → create booking
  → save booking
```

## 17.4 Repository layer

Responsibilities:

- database access;
- entity retrieval;
- save/delete;
- custom/derived queries.

Repositories should not decide business permissions or HTTP status codes.

## 17.5 Entity/model layer

Represents persistent database data and relationships.

Entities are not automatically the best API request/response models.

## 17.6 Final interview answer

> “The Controller layer handled HTTP concerns, the Service layer contained booking and room business rules, and the Repository layer handled persistence through Spring Data JPA. This separation made each layer easier to understand and test and prevented the controller from becoming responsible for database and domain logic.”

## 17.7 Is it MVC?

A REST backend may use Spring MVC for request handling, while internally following Controller-Service-Repository layering.

Safe answer:

> “Spring MVC handles the web request model, and my backend additionally separates controller, service and repository responsibilities. I would not describe the repository as the MVC view.”

## 17.8 Modular monolith or microservices?

Unless the project has independently deployed services:

> “It is a layered modular monolithic backend, not microservices. All modules run in one Spring Boot application and one deployment.”

This is a strong honest answer.

---

# 18. DTOs, Validation and Exception Handling

## 18.1 What is a DTO?

DTO stands for Data Transfer Object.

It represents data exchanged across a boundary, such as:

- incoming room creation request;
- booking request;
- login request;
- room response;
- booking response.

## 18.2 Why not expose JPA entities directly?

Problems can include:

- accidental exposure of password/hash/internal fields;
- tight coupling between API and database schema;
- recursive JSON because of bidirectional relationships;
- lazy-loading exceptions;
- unwanted entity updates through request binding;
- API breaking when persistence model changes.

### Interview answer

> “DTOs allowed the API contract to contain only the required fields. For example, a booking request needs room ID and dates, not an entire nested User and Room entity graph. This improves validation, security and maintainability.”

## 18.3 Request DTO vs response DTO

### Request DTO

Contains fields accepted from the client.

```java
public record BookingRequest(
    Long roomId,
    LocalDate checkIn,
    LocalDate checkOut
) {}
```

### Response DTO

Contains fields safe and useful to return.

Possible fields:

- booking ID;
- room summary;
- dates;
- status;
- user-visible price.

**[VERIFY-IN-CODE]** Check whether records or classes are used.

## 18.4 Bean Validation

Common annotations:

| Annotation | Purpose |
|---|---|
| `@NotNull` | Must not be null |
| `@NotBlank` | Non-null string containing non-whitespace |
| `@Email` | Email shape |
| `@Size` | Length/collection constraints |
| `@Positive` | Positive number |
| `@FutureOrPresent` | Today or future date |
| `@Valid` | Trigger nested/request validation |

### Example

```java
public record BookingRequest(
    @NotNull Long roomId,
    @NotNull @FutureOrPresent LocalDate checkIn,
    @NotNull @Future LocalDate checkOut
) {}
```

### Important

Annotations alone cannot express every business rule. `checkOut > checkIn` and overlap detection often require service-level or custom validation.

## 18.5 Validation layers

### Field validation

- blank room name;
- invalid email;
- negative price.

### Cross-field validation

- checkout after check-in;
- maximum guest count relative to room capacity.

### Database/business validation

- room exists;
- room active;
- no conflicting booking;
- user has permission.

## 18.6 Centralised exception handling

`@ControllerAdvice` or `@RestControllerAdvice` can catch exceptions across controllers.

Conceptual mapping:

| Exception | Status |
|---|---:|
| Validation error | 400 |
| Room not found | 404 |
| Booking conflict | 409 |
| Access denied | 403 |
| Unexpected error | 500 |

### Response shape

```json
{
  "timestamp": "...",
  "status": 409,
  "error": "BOOKING_CONFLICT",
  "message": "Room is unavailable for the selected dates",
  "path": "/api/bookings"
}
```

**[VERIFY-IN-CODE]** Prepare actual fields.

## 18.7 Why centralised validation/handling?

> “It keeps controllers concise and gives clients a consistent error format. Instead of writing different try-catch responses in every controller, known exceptions are mapped centrally to appropriate HTTP statuses.”

---


## 18.10 Interview Follow-Up Ladder — Layering, DTOs, Validation and Errors

### Follow-up 1: Explain Controller-Service-Repository in one example.

> “For a booking request, the controller receives and validates the DTO, the service applies date and availability rules, and the repository performs database queries and persistence. Each layer has one main responsibility.”

### Follow-up 2: Why not call the repository directly from the controller?

> “That mixes HTTP handling with business logic and makes booking rules harder to reuse or test. A service layer provides a transaction and business boundary.”

### Follow-up 3: Is the service layer only a pass-through?

> “It should not exist only to forward calls. In Kashi Atithi it is the natural place for booking validation, overlap checks, price calculation, entity mapping and transaction control.”

### Follow-up 4: Why use DTO instead of entity in request body?

> “A DTO exposes only permitted fields and keeps the API contract separate from persistence. It prevents clients from setting internal fields such as ID, role, owner, status or entity associations unexpectedly.”

### Follow-up 5: Request DTO vs response DTO?

> “A request DTO represents fields accepted from the client. A response DTO represents fields intentionally returned. They can differ because not every stored field should be accepted or exposed.”

### Follow-up 6: Where should validation happen?

> “Basic structural validation belongs at the API boundary through DTO constraints. Business validation—such as room availability or allowed booking state transitions—belongs in the service layer. Database constraints provide the final integrity layer.”

### Follow-up 7: What is centralised exception handling?

> “A common exception handler maps application exceptions to consistent HTTP status codes and error bodies. For example, room-not-found maps to 404 and date conflict maps to 409.”

### Follow-up 8: Why create custom exceptions?

> “A custom exception expresses the business meaning of a failure. `BookingConflictException` communicates more than a generic runtime exception and can be mapped consistently.”

**[VERIFY-IN-CODE]** Use actual exception names.

### Follow-up 9: Should internal exception messages be returned directly?

> “Not always. Internal details may leak implementation information. The API should return safe, useful messages and log technical details server-side.”

### Follow-up 10: What is mass assignment?

> “It occurs when clients can bind fields they should not control. DTOs and explicit mapping reduce this risk.”

### Follow-up 11: Manual mapping or MapStruct?

> “Both are valid. Manual mapping is explicit and simple for small projects; mapping libraries reduce repeated code in larger projects. I should state what the project actually used.”

### Follow-up 12: What does `@Valid` do?

> “It triggers validation of the annotated request object according to its declared constraints before normal business processing continues.”

### Follow-up 13: Can DTO validation prevent double booking?

> “No. DTO validation can check that checkout is after check-in, but availability depends on current database state and must be checked in the service/repository transaction.”

---

# 19. Spring Data JPA

## 19.1 What is JPA?

JPA, now Jakarta Persistence, is a specification for mapping Java objects to relational database data and managing their persistence.

It defines concepts and APIs such as:

- entities;
- entity manager;
- relationships;
- persistence context;
- JPQL;
- transactions.

## 19.2 Is JPA a framework or implementation?

JPA is a specification/API. Hibernate is a common implementation/provider.

### Perfect answer

> “JPA defines the persistence contract and annotations, while Hibernate is the ORM provider that implements that contract in my project. Spring Data JPA adds repository abstractions above JPA to reduce data-access boilerplate.”

## 19.3 What is Spring Data JPA?

Spring Data JPA simplifies repository creation.

Example:

```java
public interface RoomRepository extends JpaRepository<Room, Long> {
}
```

This provides common operations such as:

- `save`;
- `findById`;
- `findAll`;
- `deleteById`;
- pagination/sorting variants.

## 19.4 Derived query methods

Spring Data can derive queries from method names.

Conceptual examples:

```java
List<Room> findByActiveTrue();
List<Booking> findByUserId(Long userId);
boolean existsByRoomIdAndCheckInLessThanAndCheckOutGreaterThan(...);
```

**[VERIFY-IN-CODE]** Use exact methods from the repository.

## 19.5 JPQL vs native SQL

### JPQL

Queries entity names and fields.

```java
SELECT b FROM Booking b WHERE b.room.id = :roomId
```

### Native SQL

Queries actual tables and columns.

```sql
SELECT * FROM bookings WHERE room_id = ?
```

### Answer

> “JPQL is portable across supported relational databases because it targets the entity model. Native SQL provides database-specific control but increases coupling to the schema and database.”

## 19.6 `JpaRepository` vs `CrudRepository`

`CrudRepository` provides basic CRUD operations.

`JpaRepository` builds on repository abstractions and adds JPA-specific and list/pagination-oriented capabilities.

## 19.7 Persistence context

The persistence context tracks managed entity instances.

When an entity is managed inside a transaction, changes can be detected and flushed to the database without an explicit update method for every field change.

This is called **dirty checking**.

### Interview answer

> “Hibernate tracks managed entities in the persistence context. If a managed Room entity is modified inside a transaction, Hibernate can detect the change and generate the required SQL during flush.”

## 19.8 Entity states

Common conceptual states:

- transient — new object not managed/persisted;
- managed/persistent — tracked in persistence context;
- detached — was managed but no longer tracked;
- removed — scheduled for deletion.

## 19.9 `save()` behaviour

Spring Data JPA may persist a new entity or merge an existing/detached one depending on entity state and ID strategy.

Avoid oversimplifying it as “save always runs INSERT.”

---

# 20. Hibernate ORM

## 20.1 What is ORM?

ORM stands for Object-Relational Mapping.

It maps:

- Java class ↔ table;
- object ↔ row;
- field ↔ column;
- object reference ↔ foreign key relationship.

## 20.2 What is Hibernate?

Hibernate is an ORM framework and a JPA provider.

### Project answer

> “Hibernate translated JPA entity operations into SQL for MySQL and managed relationships, persistence context and entity state. Spring Data JPA provided the repository interface above it.”

## 20.3 Advantages

- less repetitive JDBC mapping;
- object-oriented entity model;
- relationship handling;
- dirty checking;
- transaction integration;
- JPQL and criteria support.

## 20.4 Disadvantages/trade-offs

- generated SQL may be inefficient if not understood;
- lazy loading can create unexpected queries;
- N+1 query problem;
- entity graphs can create recursive serialisation;
- abstraction does not remove the need for SQL knowledge.

### Strong answer

> “ORM reduces mapping boilerplate but does not eliminate database design or query analysis. I still need to understand generated SQL, indexes and fetch strategy.”

## 20.5 Lazy vs eager loading

### Lazy

Related data is loaded when accessed.

### Eager

Related data is loaded immediately with the owning entity, although the actual SQL strategy can vary.

### Project example

Loading a Room does not always require loading every historical Booking.

Therefore, a large bookings collection is often better treated lazily.

**[VERIFY-IN-CODE]** Know the actual fetch type.

## 20.6 N+1 problem

Example:

1. Query 10 rooms.
2. Access each room’s bookings.
3. Hibernate sends one additional query for each room.
4. Total becomes 1 + 10 queries.

Possible solutions:

- fetch join;
- entity graph;
- DTO projection;
- batch fetching;
- query redesign.

### Interview answer

> “The N+1 problem occurs when one initial query is followed by a separate query for each result’s relationship. It can be reduced by fetching only the required associations in a controlled query or projection.”

## 20.7 Cascade

Cascade determines whether an entity operation propagates to related entities.

Examples:

- `PERSIST`;
- `MERGE`;
- `REMOVE`;
- `ALL`.

### Warning

Using `CascadeType.ALL` everywhere can accidentally delete important related data.

Example:

Deleting a User should not automatically delete a Room merely because both relate through bookings.

## 20.8 Orphan removal

`orphanRemoval = true` can remove a child when it is removed from the parent relationship collection, depending on mapping.

Use only when the child lifecycle is genuinely owned by the parent.

## 20.9 Recursive JSON problem

Bidirectional relationships can lead to:

```text
User → bookings → user → bookings → ...
```

Solutions:

- return DTOs;
- JSON annotations where appropriate;
- avoid exposing entities directly;
- design one-directional API responses.

DTOs are usually the cleanest API-level answer.

---

# 21. MySQL and Relational Database Design

## 21.1 What is MySQL?

MySQL is a relational database management system.

Data is organised into tables with:

- columns;
- rows;
- primary keys;
- foreign keys;
- constraints;
- indexes.

### Project answer

> “MySQL was suitable because hotel bookings have clear relationships among Users, Rooms and Bookings, and relational constraints and transactions are valuable for maintaining consistent reservation data.”

## 21.2 Why SQL instead of MongoDB?

> “The core booking domain is relational. A Booking belongs to a User and a Room, and date availability must be queried consistently. MySQL provides relational integrity, joins, transactions and indexing that fit this domain.”

Do not say MongoDB cannot support bookings. Explain fit rather than absolute superiority.

## 21.3 Primary key

Uniquely identifies a row.

Examples:

- `user_id`;
- `room_id`;
- `booking_id`.

## 21.4 Foreign key

Connects one table to another.

Bookings may contain:

- `user_id` → Users;
- `room_id` → Rooms.

Foreign keys can prevent bookings from referencing nonexistent users or rooms, depending on schema rules.

## 21.5 Constraints

Potential constraints:

- email unique;
- price non-negative, where supported/defined;
- role not null;
- check-in/check-out not null;
- user and room foreign keys;
- booking status valid.

**[VERIFY-IN-CODE/SCHEMA]** Prepare actual constraints.

## 21.6 Indexes

Indexes speed up selected queries.

Important booking-query candidates:

- room ID;
- user ID;
- booking status;
- date columns;
- a composite index beginning with room ID.

### Trade-off

Indexes improve reads but require storage and maintenance during writes.

### Answer

> “For overlap searches, the database commonly filters by room first and then dates/status, so indexing should reflect the actual query pattern. I would confirm the execution plan rather than add indexes blindly.”

## 21.7 Normalisation

Normalisation reduces unnecessary duplication and update anomalies.

Example:

Do not repeat complete user data and complete room data inside every booking row. Store foreign keys and retrieve related data as required.

## 21.8 ACID

### Atomicity

A transaction completes fully or rolls back.

### Consistency

Database moves between valid states according to constraints and rules.

### Isolation

Concurrent transactions should not interfere in unacceptable ways.

### Durability

Committed data should survive system failure according to database guarantees.

### Project relevance

Booking creation is where atomicity and isolation become important.

## 21.9 JOIN example

To retrieve booking summary:

```sql
SELECT b.id, b.check_in, b.check_out,
       u.name AS guest_name,
       r.room_number
FROM bookings b
JOIN users u ON u.id = b.user_id
JOIN rooms r ON r.id = b.room_id;
```

ORM may generate similar SQL based on entity relationships or projections.

---

# 22. Users, Rooms and Bookings Relationships

## 22.1 Core domain relationship

Common model:

- One User can create many Bookings.
- One Room can have many Bookings over time.
- One Booking belongs to one User.
- One Booking belongs to one Room.

Diagram:

```text
User 1 ───────< Booking >─────── 1 Room
        many              many over time
```

Another way:

```text
User (1) ----- (N) Booking
Room (1) ----- (N) Booking
```

## 22.2 Why Booking is a separate entity

Booking contains relationship-specific data:

- check-in;
- check-out;
- status;
- booking timestamp;
- total price;
- guest count.

This data belongs neither only to User nor only to Room.

## 22.3 JPA mapping example

Conceptual Booking side:

```java
@ManyToOne(fetch = FetchType.LAZY)
@JoinColumn(name = "user_id", nullable = false)
private User user;

@ManyToOne(fetch = FetchType.LAZY)
@JoinColumn(name = "room_id", nullable = false)
private Room room;
```

Conceptual User side:

```java
@OneToMany(mappedBy = "user")
private List<Booking> bookings;
```

Conceptual Room side:

```java
@OneToMany(mappedBy = "room")
private List<Booking> bookings;
```

**[VERIFY-IN-CODE]** Match annotations, fetch and cascade settings.

## 22.4 Owning side

In a bidirectional relationship, the side containing the foreign key mapping is generally the owning side.

Here, Booking commonly owns both `ManyToOne` relationships because the Bookings table contains `user_id` and `room_id`.

`mappedBy` points to the owning field.

### Interview answer

> “The Booking entity commonly owns the relationships because the bookings table contains the user and room foreign keys. The collections in User and Room use `mappedBy` to describe the inverse side.”

## 22.5 Why not Many-to-Many directly?

Users and Rooms do have a many-to-many relationship over time, but Booking contains important additional fields.

Therefore, model Booking as an association entity rather than a direct `@ManyToMany`.

### Strong answer

> “A direct many-to-many mapping would hide relationship attributes such as dates, status and total price. Booking must be a first-class entity.”

## 22.6 Bidirectional helper methods

When using bidirectional relationships, helper methods can keep both object sides consistent.

```java
public void addBooking(Booking booking) {
    bookings.add(booking);
    booking.setRoom(this);
}
```

However, the database is updated based on the owning side.

## 22.7 Deletion decisions

Before deleting a Room with historical bookings, decide:

- block deletion;
- soft-delete/deactivate room;
- retain historical bookings;
- cascade carefully.

Production systems often prefer room deactivation to destroying booking history.

---


## 22.10 Interview Follow-Up Ladder — JPA, Hibernate, MySQL and Relationships

### Follow-up 1: JPA, Hibernate and Spring Data JPA—what is the difference?

> “JPA defines the standard persistence API and annotations. Hibernate is a common implementation of that standard. Spring Data JPA adds repository abstractions and integration on top, reducing repetitive data-access code.”

### Follow-up 2: Is JPA a database?

> “No. JPA is a Java persistence specification/API. MySQL is the database, Hibernate performs ORM work and Spring Data JPA simplifies repository access.”

### Follow-up 3: What is ORM?

> “ORM maps Java objects and their relationships to relational tables and foreign keys. It reduces manual row-to-object conversion but does not remove the need to understand SQL and database behaviour.”

### Follow-up 4: What is a persistence context?

> “It is the context in which JPA manages entity instances. Managed entities can be tracked for changes, identity and lifecycle operations within a transaction.”

### Follow-up 5: What is dirty checking?

> “When a managed entity changes inside a transaction, Hibernate can detect the difference and generate an update during flush without an explicit update method call.”

### Follow-up 6: What are entity states?

> “An entity can be transient, managed, detached or removed. The important interview point is whether the entity is currently tracked by the persistence context.”

### Follow-up 7: What is lazy loading?

> “A lazy association is loaded when it is accessed rather than automatically with the parent. It can reduce unnecessary data retrieval but may cause extra queries or access errors outside an active context.”

### Follow-up 8: What is eager loading?

> “An eager association is intended to be loaded with the parent. It can be convenient but may retrieve more data than required and create heavy queries.”

### Follow-up 9: What is the N+1 query problem?

> “The application first executes one query for a list and then one additional query for each item’s association. For example, fetching rooms and then lazily fetching bookings for every room can create many queries.”

### Follow-up 10: How do you solve N+1?

> “Use query-specific fetch joins, entity graphs, projections or redesigned queries. Making every association eager is not a good universal solution.”

### Follow-up 11: What does `mappedBy` mean?

> “It identifies the inverse side of a bidirectional relationship and points to the field that owns the foreign-key mapping on the other entity.”

### Follow-up 12: Which entity should own Room–Booking?

> “Usually the many-side, Booking, owns the relationship through the room foreign key. Room can expose a collection mapped by the booking’s room field.”

### Follow-up 13: Why is Booking an entity instead of direct User–Room many-to-many?

> “Because the relationship has its own attributes such as check-in, checkout, total amount and status. That makes Booking a business entity, not a simple join table.”

### Follow-up 14: What does cascade mean?

> “Cascade propagates selected persistence operations from one entity to associated entities. It must be selected carefully; deleting a room should not accidentally delete data contrary to business or audit requirements.”

### Follow-up 15: Why can returning JPA entities directly cause problems?

> “It can expose internal fields, trigger lazy loading, create recursive JSON through bidirectional relationships and tightly couple the API to the database model. Response DTOs avoid these issues.”

### Follow-up 16: What is a transaction?

> “A transaction groups database operations into a unit that commits together or rolls back on failure, according to configured rules.”

### Follow-up 17: What does `@Transactional` not guarantee?

> “It does not automatically guarantee correct concurrency, prevent every race condition or make a poorly designed query safe. Isolation and locking still matter.”

### Follow-up 18: Why use `BigDecimal` for room price?

> “Binary floating-point values can introduce precision errors. `BigDecimal` gives controlled decimal arithmetic suitable for money.”

### Follow-up 19: What MySQL indexes would be useful?

> “Indexes should follow actual query patterns. Booking-conflict queries commonly filter by room, active status and date range. User email or another unique login identifier may also need a unique index. I must confirm actual indexes from the schema.”

### Follow-up 20: Does an index always improve performance?

> “It improves selected reads but consumes storage and adds write-maintenance cost. An index should be justified by queries and measured, not added to every column.”

### Follow-up 21: What is the owning-side update rule in bidirectional relations?

> “The owning side controls the foreign-key update. Helper methods should keep both Java object references consistent, but the owning-side field must be set correctly for persistence.”

### Follow-up 22: What is `orphanRemoval`?

> “It can remove a child entity when it is removed from the parent’s managed collection. It is powerful and should be used only when the child’s lifecycle is truly owned by the parent.”

---

# 23. Booking Conflict Prevention

This is the most important section of the project.

## 23.1 What is a booking conflict?

A conflict occurs when a new requested stay overlaps an existing active booking for the same room.

Suppose an existing booking is:

```text
Existing: 10 August → 15 August
```

Requested examples:

| Requested dates | Conflict? |
|---|---|
| 8 → 11 August | Yes |
| 12 → 14 August | Yes |
| 14 → 18 August | Yes |
| 10 → 15 August | Yes |
| 5 → 10 August | Depends on checkout boundary rule |
| 15 → 18 August | Depends on checkout boundary rule |
| 16 → 18 August | No |

## 23.2 Half-open date interval model

A clean hotel model treats a stay as:

```text
[checkIn, checkOut)
```

Meaning:

- check-in date is occupied;
- check-out date is not occupied after checkout;
- one guest can check out on 15 August and another can check in on 15 August.

Under this model:

```text
Existing: [10, 15)
New:      [15, 18)
```

No conflict.

**[VERIFY-IN-CODE]** Confirm the business rule actually implemented.

## 23.3 Universal overlap condition

Two half-open intervals overlap when:

```text
newCheckIn < existingCheckOut
AND
newCheckOut > existingCheckIn
```

Equivalent non-overlap condition:

```text
newCheckOut <= existingCheckIn
OR
newCheckIn >= existingCheckOut
```

Overlap is the negation of non-overlap.

## 23.4 Why this formula works

There are only two ways for bookings not to overlap:

1. New booking ends before or exactly when existing booking begins.
2. New booking begins when or after existing booking ends.

If neither is true, the intervals overlap.

## 23.5 Repository query concept

JPQL example:

```java
@Query("""
    SELECT COUNT(b) > 0
    FROM Booking b
    WHERE b.room.id = :roomId
      AND b.status IN :activeStatuses
      AND b.checkIn < :requestedCheckOut
      AND b.checkOut > :requestedCheckIn
""")
boolean existsConflict(...);
```

Derived method names may also express this but can become difficult to read.

**[VERIFY-IN-CODE]** Prepare the actual query.

## 23.6 Service-level flow

```text
Validate check-in/check-out present
        ↓
Check check-out > check-in
        ↓
Check room exists and can be booked
        ↓
Search active overlapping bookings
        ↓
Conflict exists?
    Yes → throw BookingConflictException
    No  → calculate price and save booking
```

## 23.7 Booking status

Conflict checks should usually ignore statuses that no longer occupy the room, such as cancelled bookings.

Possible statuses:

- PENDING;
- CONFIRMED;
- CANCELLED;
- COMPLETED.

**[VERIFY-IN-CODE]** Know exact enum/status rules.

## 23.8 Date validation

At minimum:

- check-in not null;
- check-out not null;
- check-out after check-in;
- check-in not in unacceptable past;
- stay length within business limits, if any.

## 23.9 Total price calculation

Conceptual calculation:

```text
numberOfNights = DAYS.between(checkIn, checkOut)
totalPrice = numberOfNights × roomPricePerNight
```

Potential issues:

- use `BigDecimal` for money;
- snapshot room price in booking;
- taxes/fees;
- discounts;
- timezone not relevant when using pure local hotel dates, but check-in time may be.

**[VERIFY-IN-CODE]** Prepare actual calculation.

## 23.10 409 Conflict

A valid request can still conflict with current resource state.

Therefore, 409 is a meaningful status for an overlapping booking.

### Answer

> “The request shape may be valid, but the selected room is unavailable for that date range, so I would map the domain conflict to HTTP 409.”

## 23.11 Controlled interview answer

> “Before saving a booking, the service validates that checkout is after check-in and queries active bookings for the same room. The central overlap condition is `newCheckIn < existingCheckOut` and `newCheckOut > existingCheckIn`. If a match exists, the service rejects the request as a booking conflict; otherwise, it creates the booking. I also need to distinguish this logical check from full concurrency safety, because two simultaneous requests can still race without transaction-level protection.”

This last sentence is the difference between an average and strong candidate.

---

# 24. Transactions, Concurrency and True Double-Booking Safety

## 24.1 The race condition

Suppose two users book the same room for the same dates at nearly the same time.

```text
Transaction A checks conflict → none
Transaction B checks conflict → none
Transaction A inserts booking
Transaction B inserts booking
```

Both requests passed the availability check before either booking became visible in the required manner.

Result: double booking.

## 24.2 Why a normal “check then save” may be insufficient

A service method can be logically correct for sequential requests but unsafe under concurrency.

This is called a **time-of-check to time-of-use race**.

## 24.3 What does `@Transactional` do?

`@Transactional` groups database operations into a transaction boundary.

Potential benefits:

- operations succeed or roll back together;
- persistence context remains consistent;
- transaction isolation and locking can be applied.

### Important correction

`@Transactional` alone does not automatically prevent every double-booking race. The isolation level and locking strategy still matter.

## 24.4 Pessimistic locking

Pessimistic locking locks a relevant database row while a transaction completes.

Possible approach:

1. Lock the Room row with `PESSIMISTIC_WRITE`.
2. Check overlapping bookings.
3. Insert booking.
4. Commit and release lock.

Second request waits, then sees the new booking and fails.

Conceptual repository method:

```java
@Lock(LockModeType.PESSIMISTIC_WRITE)
@Query("SELECT r FROM Room r WHERE r.id = :roomId")
Optional<Room> findByIdForUpdate(Long roomId);
```

**[IMPROVEMENT / VERIFY-IN-CODE]** Do not claim unless implemented.

## 24.5 Optimistic locking

Optimistic locking uses a version field:

```java
@Version
private Long version;
```

If two transactions update the same versioned entity, one can fail due to a version mismatch.

### Limitation for date-range insertion

If both transactions only insert Booking rows without updating the same Room row, a Room version may not change. The design must intentionally cause a protected shared version/update or use another concurrency control.

## 24.6 Isolation levels

Common isolation levels:

- READ UNCOMMITTED;
- READ COMMITTED;
- REPEATABLE READ;
- SERIALIZABLE.

Higher isolation can reduce anomalies but may reduce concurrency and increase locking/deadlock risk.

### MySQL nuance

InnoDB's default is commonly REPEATABLE READ, but exact behaviour depends on queries and locks.

### Strong answer

> “I would not claim that the default isolation alone guarantees no overlapping range insert. I would test the exact query and use explicit locking or a database design that serialises booking creation per room.”

## 24.7 Database constraints

A simple unique constraint can prevent duplicate values such as:

```text
(room_id, check_in, check_out)
```

But it does **not** prevent partially overlapping different ranges.

Example:

- booking A: 10–15;
- booking B: 12–14.

The tuple is different but still conflicts.

Therefore, range overlap needs query/locking logic or a specialised reservation-slot model.

## 24.8 Alternative slot model

For each occupied night, store a row with a unique constraint:

```text
(room_id, occupied_date) UNIQUE
```

Creating a five-night booking inserts five slot rows. A conflicting insert violates uniqueness.

Trade-offs:

- strong database protection;
- more rows;
- booking updates/cancellations require slot management.

## 24.9 Distributed locking

For multiple application instances, a distributed lock could serialise booking per room, but it adds complexity and still requires careful failure handling.

Database transaction-based protection is often preferable when the booking data already resides in the same relational database.

## 24.10 Idempotency

Even without a date conflict, a client may retry the same booking request due to a network timeout.

An idempotency key can ensure the same logical request is not created twice.

**[IMPROVEMENT]** Useful for production booking/payment flows.

## 24.11 Deadlocks

Locks can create deadlocks when transactions acquire resources in different orders.

Mitigation:

- lock resources in consistent order;
- keep transactions short;
- index lock queries;
- retry deadlock victims safely.

## 24.12 Best honest answer for your current resume

> “My implemented logic checked for overlapping active bookings before insertion. That protects normal sequential flows. For production-grade concurrency, I would ensure the check and insert execute inside a transaction with an explicit strategy such as locking the room row, because two simultaneous requests can otherwise both pass the initial check.”

This answer protects you if the current code does not implement locking.

---


## 24.13 Interview Follow-Up Ladder — Booking Logic, Transactions and Concurrency

### Follow-up 1: Explain the overlap condition in plain language.

> “Two stays overlap when the requested stay starts before the existing stay ends and the requested stay ends after the existing stay starts.”

```text
requestedCheckIn < existingCheckOut
AND
requestedCheckOut > existingCheckIn
```

### Follow-up 2: Why use strict `<` and `>` instead of `<=` and `>=`?

> “For hotel-night semantics, a guest can often check out on the same date another guest checks in. Strict comparisons treat touching boundaries as non-overlapping. The exact rule must match the business definition.”

### Follow-up 3: Give an overlapping example.

> “Existing booking is August 10 to August 15. A request for August 12 to August 14 overlaps because it starts before August 15 and ends after August 10.”

### Follow-up 4: Give a non-overlapping boundary example.

> “Existing booking ends on August 15 and the new booking starts on August 15. Under checkout-exclusive date semantics, they do not overlap.”

### Follow-up 5: Which booking statuses should block availability?

> “Usually active statuses such as confirmed—and possibly pending, depending on the business rule—should block. Cancelled or rejected bookings should not. I need to state the exact enum and rules in the code.”

### Follow-up 6: What validations happen before the conflict query?

> “Both dates must exist, checkout must be after check-in, the room must exist and be bookable, and the requested dates must satisfy business rules such as not being in an invalid past period.”

### Follow-up 7: Why return HTTP 409?

> “The request format is valid, but it conflicts with the current state of the room’s reservations. That is a domain conflict rather than a syntax error.”

### Follow-up 8: How do you calculate the number of nights?

> “For date-only checkout-exclusive semantics, use the number of days between check-in and checkout. A stay from August 10 to August 11 represents one night.”

### Follow-up 9: Why snapshot the price in Booking?

> “If the room price changes later, an existing booking should preserve the amount agreed when it was created. Therefore the booking should store its calculated amount or price snapshot.”

### Follow-up 10: Why can check-then-save still fail?

> “Two concurrent requests can both check before either insert becomes visible and both conclude that the room is available. This is a race condition.”

### Follow-up 11: Is `@Transactional` enough?

> “Not by itself. It provides a transaction boundary, but concurrency correctness also depends on isolation, locks and the query design.”

### Follow-up 12: What is your strongest production solution?

> “One practical approach is to serialise bookings per room by locking the room row inside a transaction, then checking overlap and inserting before releasing the lock. Another database-oriented design stores unique room-night slots.”

### Follow-up 13: Pessimistic vs optimistic locking?

> “Pessimistic locking blocks competing operations on a selected row until the transaction finishes. Optimistic locking detects conflicting updates through a version field. For independent booking inserts, optimistic locking works only if the design updates a shared versioned record or otherwise creates a conflict.”

### Follow-up 14: Why does a unique `(room_id, check_in, check_out)` constraint not solve overlap?

> “It prevents only the exact same tuple. Different ranges such as 10–15 and 12–14 still overlap while having different values.”

### Follow-up 15: What is transaction isolation?

> “Isolation controls how concurrent transactions observe one another’s changes. Stronger isolation reduces some anomalies but can reduce concurrency and increase locking.”

### Follow-up 16: What is an idempotency key?

> “It identifies a logical create request so a retry caused by timeout does not create a second booking. This addresses duplicate retries, which is different from date overlap.”

### Follow-up 17: What happens when a transaction fails after modifying entities?

> “The database transaction should roll back according to configured rules. External side effects, such as S3 uploads, are not automatically rolled back by a database transaction and need compensation.”

### Follow-up 18: How would you test double-booking safety?

> “I would test sequential overlapping dates, boundary dates, cancelled statuses and multiple concurrent requests for the same room and range. The concurrency test should verify that at most one booking succeeds.”

### Follow-up 19: What is a deadlock?

> “Two transactions can each hold a lock and wait for the other. Keeping transactions short, indexing lock queries and acquiring resources consistently reduces risk; applications may also retry deadlock victims safely.”

### Follow-up 20: What should you say if the current code has only a conflict query?

> “My implemented logic prevents conflicts in normal sequential requests by querying overlapping active bookings. For production concurrency, I would add transaction-level locking because simultaneous requests can otherwise race.”

---

# 25. AWS S3 Room Image Storage

## 25.1 What is Amazon S3?

Amazon S3 is an object-storage service.

An object contains:

- file data;
- metadata;
- an object key;
- and belongs to a bucket.

## 25.2 Why not store images in MySQL?

Storing image binary data directly in relational rows can:

- enlarge database size;
- increase backup and query overhead;
- mix structured business data with media delivery;
- make CDN/object delivery harder.

### Project answer

> “I used S3 to store room-image objects and kept only the object key or URL with the Room data in MySQL. This kept the relational database focused on structured hotel data.”

## 25.3 Bucket and object key

### Bucket

Container for S3 objects.

### Object key

Unique identifier/path-like name inside the bucket.

Example:

```text
rooms/42/550e8400-room.jpg
```

## 25.4 Upload flow

```text
Admin sends multipart image
        ↓
Backend validates type/size
        ↓
Generate unique object key
        ↓
AWS SDK uploads object to bucket
        ↓
S3 returns success/metadata
        ↓
Store object key or URL in Room entity
```

**[VERIFY-IN-CODE]** Confirm multipart flow and SDK version.

## 25.5 Why unique keys?

If two uploads use the same key, a later upload may replace or version the object depending on bucket configuration.

A unique key avoids accidental collision.

Possible key components:

- UUID;
- room ID;
- timestamp;
- safe extension.

## 25.6 URL vs object key

### Storing full URL

Simple to return, but tightly couples database data to endpoint/domain style.

### Storing key

More flexible. Application can generate:

- public URL;
- presigned URL;
- CloudFront URL.

### Strong answer

> “Storing the key gives more delivery flexibility, while storing the URL is simpler. I should describe what the project actually stores.”

## 25.7 Public object vs presigned URL

### Public object

Anyone with URL may access it, depending on policy.

### Presigned URL

Temporary URL granting limited access without making the bucket public.

For public hotel images, CDN/public-read architecture may be acceptable with secure upload rules. For private documents, presigned URLs are more appropriate.

## 25.8 AWS credentials

Do not hard-code:

- access-key ID;
- secret-access key.

Use:

- environment variables;
- AWS credential provider chain;
- IAM role in AWS-hosted environments;
- local profile for development.

### Answer

> “Secrets should remain outside source control. The SDK should obtain credentials from secure configuration or an IAM role rather than hard-coded Java strings.”

## 25.9 IAM least privilege

The application should receive only required permissions, such as upload/read/delete within the intended bucket or prefix.

Avoid broad administrator access.

## 25.10 File validation

Validate:

- MIME type;
- extension, but not extension alone;
- size;
- allowed image dimensions if required;
- filename sanitisation;
- malware scanning for higher-risk systems.

## 25.11 Update and cleanup

When a room image changes:

1. upload new object;
2. update database reference;
3. safely delete old object after success.

If database save fails after upload, an orphaned S3 object may remain.

Possible solutions:

- compensating deletion;
- background cleanup;
- staged upload lifecycle.

## 25.12 S3 and database transaction problem

MySQL transaction cannot automatically roll back an S3 upload because they are different systems.

### Strong answer

> “Database and S3 operations are not one ACID transaction. If S3 succeeds and the database fails, I need compensation or cleanup. I would design the service to minimise orphaned objects and log/retry cleanup.”

---


## 25.13 Interview Follow-Up Ladder — AWS S3

### Follow-up 1: What is object storage?

> “Object storage stores each file as an object with data, metadata and a unique key inside a bucket. It differs from a relational table or ordinary hierarchical application file system.”

### Follow-up 2: Why S3 instead of storing image bytes in MySQL?

> “S3 is designed for durable object storage and media delivery. Keeping only the object key or URL in MySQL keeps relational rows smaller and separates media from structured booking data.”

### Follow-up 3: What is a bucket?

> “A bucket is the top-level S3 container that holds objects and applies settings such as access policy, versioning and lifecycle rules.”

### Follow-up 4: What is an object key?

> “It is the unique name of an object inside the bucket. A key can include prefixes such as `rooms/42/uuid-image.jpg`, but S3 fundamentally treats it as an object identifier.”

### Follow-up 5: How did the upload request flow?

> “The admin sends multipart data, the backend validates it, creates a unique key, uploads through the AWS SDK and stores the returned key or URL against the Room entity.”

**[VERIFY-IN-CODE]** Confirm whether the frontend uploaded through the backend or used a presigned URL.

### Follow-up 6: Why use a unique object key?

> “It prevents accidental overwrite and makes room-image lifecycle management predictable.”

### Follow-up 7: Store URL or key?

> “A full URL is simple but couples data to the current delivery endpoint. A key is more flexible because the application can later produce a public, CloudFront or presigned URL. I should state the project’s actual choice.”

### Follow-up 8: What is a presigned URL?

> “It is a temporary signed URL that grants limited access to an S3 operation without exposing permanent AWS credentials.”

### Follow-up 9: Where should AWS credentials be stored?

> “Outside the source code, using environment configuration, a credential provider or an IAM role. They must never be committed to GitHub or sent to the browser.”

### Follow-up 10: What is IAM least privilege?

> “The application receives only the S3 actions and bucket/prefix access it actually needs, instead of broad administrator permissions.”

### Follow-up 11: What if the uploaded file is malicious or too large?

> “The backend should validate size and allowed content type, generate its own safe key and avoid trusting the original filename. For stronger security, file content may also require inspection.”

### Follow-up 12: What if S3 upload succeeds but database save fails?

> “The database rollback does not delete the S3 object automatically. The service should perform a compensating delete or run cleanup for orphaned objects.”

### Follow-up 13: What if database save succeeds but S3 upload fails?

> “The application should not save a successful room-image reference before the upload has succeeded, or it should track the image state and recover. The operation ordering and error handling must be explicit.”

### Follow-up 14: What happens when a room image is replaced?

> “Upload the new object, update the database reference and then delete the old object safely. Failure handling should avoid losing both the old image and the new reference.”

### Follow-up 15: S3 vs Cloudinary?

> “S3 is general-purpose object storage. Cloudinary provides media-focused upload, transformation and delivery features. S3 offers infrastructure flexibility, while Cloudinary offers higher-level media functionality.”

---

# 26. React Frontend and API Integration

The resume lists React.js but does not specify its state-management or HTTP library for Kashi Atithi.

## 26.1 Safe React explanation

> “React provided the component-based frontend for screens such as room listings, login, booking forms and admin room management. The client sent JSON or multipart requests to the Spring Boot APIs and rendered loading, success and error states.”

**[VERIFY-IN-CODE]** Prepare actual pages/components.

## 26.2 Possible component structure

```text
App
├── Navbar
├── RoomList
│   └── RoomCard
├── RoomDetails
├── BookingForm
├── MyBookings
├── Login/Register
└── AdminDashboard
    ├── RoomForm
    └── RoomTable
```

Do not claim components not present.

## 26.3 Form handling

A booking form may collect:

- room ID;
- check-in;
- check-out;
- guest count.

Frontend validation improves usability, but backend validation remains authoritative.

### Answer

> “The client can reject obviously invalid dates early, but the backend must repeat the checks because frontend code can be bypassed.”

## 26.4 Sending JWT

A protected request commonly includes:

```http
Authorization: Bearer <token>
```

The frontend may centralise this in:

- fetch wrapper;
- Axios interceptor;
- API client utility.

**[VERIFY-IN-CODE]** Name the actual approach.

## 26.5 Axios explanation, only if used

Axios is an HTTP client library.

It can:

- send GET/POST/PATCH/DELETE requests;
- attach headers;
- send multipart forms;
- use interceptors;
- transform responses.

Do not mention Axios as a project tool if the code uses native `fetch`.

## 26.6 Interceptor

An Axios request interceptor can attach the token to outgoing requests.

A response interceptor can centralise handling for 401 responses.

### Risk

Poor interceptor design can create retry loops or attach stale tokens.

## 26.7 CORS

React and Spring Boot may run on different origins during development/deployment.

The backend must permit only intended origins/methods/headers.

CORS is not authentication.

## 26.8 Role-based frontend UI

Frontend can:

- hide Admin menu for normal User;
- redirect unauthenticated users;
- display role-specific dashboard.

But backend SecurityFilterChain remains the actual security boundary.

---


## 26.9 Interview Follow-Up Ladder — React and Backend Integration

### Follow-up 1: How does the React frontend communicate with Spring Boot?

> “It sends HTTP requests to the REST API, receives JSON responses and updates component state or shared application state. Protected requests include the authentication token according to the implemented design.”

### Follow-up 2: What is a controlled form?

> “The form input values are stored in React state and updated through event handlers. This makes validation and request construction predictable.”

### Follow-up 3: How should frontend and backend validation differ?

> “Frontend validation improves immediate user experience, but it can be bypassed. The backend must repeat all security and business-critical validation.”

### Follow-up 4: How should an API error be displayed?

> “The frontend should distinguish validation errors, authentication failures, forbidden actions, missing resources and booking conflicts, then display a clear user-facing message rather than a generic failure.”

### Follow-up 5: What does CORS have to do with React?

> “If React and Spring Boot run on different origins, the browser applies CORS. The backend must explicitly allow the trusted frontend origin and required headers/methods.”

### Follow-up 6: Does CORS secure the backend from Postman or attackers?

> “No. CORS is enforced by browsers. Authentication and authorisation must secure the API independently.”

### Follow-up 7: How does the UI use roles?

> “It can show or hide admin functionality based on the authenticated role, but that is only presentation. The backend remains the final authority.”

### Follow-up 8: How would you prevent duplicate booking-button submissions?

> “Disable the button while the request is in progress and handle retries carefully. For production reliability, the backend should also support idempotency because frontend prevention alone is not enough.”

### Follow-up 9: How do you send multipart room-image data?

> “The browser creates `FormData` containing the file and required fields, then sends it with a multipart request. The browser or client library should set the correct boundary.”

### Follow-up 10: What frontend detail must be verified?

> “The HTTP client actually used, token storage, token attachment method, route protection, form library, state-management choice, error handling and image-upload flow.”

---

# 27. Maven and Project Build

## 27.1 What is Maven?

Maven is a build and dependency-management tool for Java projects.

### Project answer

> “Maven managed Spring Boot, Security, JPA, MySQL, JWT and AWS SDK dependencies and provided a standard build lifecycle for compiling, testing and packaging the backend.”

## 27.2 What is `pom.xml`?

POM means Project Object Model.

It can define:

- project coordinates;
- Java version;
- dependencies;
- plugins;
- build configuration;
- profiles;
- packaging.

## 27.3 Maven coordinates

```text
groupId
artifactId
version
```

Together identify an artifact.

## 27.4 Dependency scope

Common scopes:

| Scope | Meaning |
|---|---|
| compile | Needed for compile and runtime by default |
| runtime | Needed at runtime, not direct compile API |
| test | Needed only for tests |
| provided | Supplied by runtime/container |

Example: MySQL driver may be runtime-scoped in many Spring Boot projects.

## 27.5 Transitive dependencies

If dependency A depends on B, Maven can bring B transitively.

### Risk

Version conflicts or unnecessary dependencies.

Useful command:

```bash
mvn dependency:tree
```

## 27.6 Maven lifecycle

Important phases:

```text
validate → compile → test → package → verify → install → deploy
```

Calling a later phase executes preceding phases in that lifecycle.

### Commands

```bash
mvn clean test
mvn clean package
mvn verify
```

## 27.7 Package vs install vs deploy

- `package` — creates JAR/WAR.
- `install` — puts artifact in local Maven repository.
- `deploy` — publishes artifact to remote repository; not the same as deploying the web application to Render/AWS.

### Excellent trap answer

> “Maven’s `deploy` lifecycle phase publishes a built artifact to a remote Maven repository. It is different from deploying the running hotel application to a hosting platform.”

## 27.8 Spring Boot Maven plugin

It can package an executable Spring Boot JAR containing application classes and required structure for `java -jar` execution.

**[VERIFY-IN-CODE]** Prepare plugin configuration and actual command.

## 27.9 Maven vs Gradle

> “Both manage builds and dependencies. Maven uses a conventional XML POM and a defined lifecycle, while Gradle uses a programmable DSL and can offer flexible, incremental builds. I used Maven because the project was configured around its standard Spring Boot workflow.”

---


## 27.10 Interview Follow-Up Ladder — Maven

### Follow-up 1: What problem does Maven solve?

> “It standardises dependency management, project structure, compilation, testing and packaging. It allows the project to declare required libraries and build steps in one configuration.”

### Follow-up 2: What is `pom.xml`?

> “It is the Project Object Model file containing project coordinates, dependencies, plugins, properties and build configuration.”

### Follow-up 3: What are group ID, artifact ID and version?

> “They uniquely identify a Maven artifact. Group ID represents the organisation or namespace, artifact ID is the project/module name and version identifies the release.”

### Follow-up 4: What is a transitive dependency?

> “A dependency required by one of my direct dependencies can be brought into the project transitively. Maven’s dependency tree helps inspect conflicts and versions.”

### Follow-up 5: Why use Spring Boot starter dependencies?

> “A starter groups compatible dependencies needed for a capability, reducing the need to select each low-level library manually.”

### Follow-up 6: `compile`, `test` and `runtime` scopes?

> “Compile dependencies are available broadly, test dependencies only for tests, and runtime dependencies are needed to execute but not necessarily compile main source. I should name the exact scopes from the POM when asked.”

### Follow-up 7: `mvn package` vs `mvn install`?

> “Package compiles, tests and creates the artifact. Install also places that artifact in the local Maven repository for other local projects to use.”

### Follow-up 8: What does `mvn deploy` mean?

> “In Maven lifecycle terminology it publishes the built artifact to a configured remote repository. It does not mean deploying the running application to a cloud server.”

### Follow-up 9: What is the Spring Boot Maven plugin?

> “It supports Spring Boot build tasks such as creating an executable packaged application with its dependencies in the expected layout.”

### Follow-up 10: How do you investigate a dependency conflict?

> “Inspect the Maven dependency tree, identify competing transitive versions and use dependency management or exclusions deliberately rather than randomly changing versions.”

### Follow-up 11: Why should you know the POM before interview?

> “It proves the exact Spring Boot version, Java version, database driver, JWT library, validation starter, AWS SDK and test dependencies actually used.”

---

# 28. The 10+ REST API Claim

## 28.1 What the interviewer expects

For each important endpoint, know:

- HTTP method;
- route;
- public/User/Admin access;
- request DTO;
- response DTO;
- service method;
- repository query;
- validation;
- status codes;
- errors.

## 28.2 API inventory template

Replace every placeholder with actual routes.

| Domain | Method | Route | Access | Purpose |
|---|---|---|---|---|
| Auth | POST | `/api/auth/register` | Public | Register user |
| Auth | POST | `/api/auth/login` | Public | Authenticate and return JWT |
| Room | GET | `/api/rooms` | Public/User | List rooms |
| Room | GET | `/api/rooms/{id}` | Public/User | Get room details |
| Room | POST | `/api/rooms` | Admin | Create room |
| Room | PUT/PATCH | `/api/rooms/{id}` | Admin | Update room |
| Room | DELETE | `/api/rooms/{id}` | Admin | Delete/deactivate room |
| Booking | POST | `/api/bookings` | User | Create booking |
| Booking | GET | `/api/bookings/me` | User | View own bookings |
| Booking | GET | `/api/bookings/{id}` | Owner/Admin | Get booking |
| Booking | PATCH/DELETE | `/api/bookings/{id}/cancel` | Owner/Admin | Cancel booking |
| Admin | GET | `/api/admin/bookings` | Admin | View/manage bookings |

**[VERIFY-IN-CODE]** These routes are examples, not resume-confirmed.

## 28.3 Example: create booking endpoint

### Request

```http
POST /api/bookings
Authorization: Bearer <token>
Content-Type: application/json
```

```json
{
  "roomId": 7,
  "checkIn": "2026-08-10",
  "checkOut": "2026-08-13"
}
```

### Processing

1. Authenticate user.
2. Validate DTO.
3. Load room.
4. Validate date order.
5. Check active overlap.
6. Calculate booking values.
7. Save booking.
8. Return response.

### Outcomes

- 201 — booking created;
- 400 — invalid date input;
- 401 — invalid/missing JWT;
- 404 — room does not exist;
- 409 — room unavailable for selected dates.

## 28.4 Example: create room endpoint

Potential multipart request:

- room data;
- image file.

Processing:

1. Authenticate Admin.
2. Validate metadata and file.
3. Upload image to S3.
4. Create Room entity with image reference.
5. Save room.
6. Return 201.

### Failure concern

If S3 upload succeeds and DB save fails, clean up the uploaded object.

## 28.5 API versioning

Example:

```text
/api/v1/rooms
```

**[IMPROVEMENT]** Use if future incompatible API changes are expected. Do not claim unless routes include it.

---


## 28.6 Interview Follow-Up Ladder — The 10+ API Claim

### Follow-up 1: Name your APIs without guessing.

Prepare the exact endpoint inventory from the controllers. For every endpoint know:

- method;
- path;
- role;
- request DTO;
- response DTO;
- service method;
- validation;
- status codes.

### Follow-up 2: How did you organise the APIs?

> “I organised endpoints around domains such as authentication, users, rooms and bookings instead of placing unrelated operations in one controller.”

### Follow-up 3: Give one complete room endpoint explanation.

> “For room creation, an authenticated admin sends a validated room DTO and image data if supported. Security verifies the admin role, the service applies business rules, S3 stores the image, the repository saves the Room entity and the controller returns a created response.”

**[VERIFY-IN-CODE]** Match the actual request shape and operation ordering.

### Follow-up 4: Give one complete booking endpoint explanation.

> “For booking creation, an authenticated user submits room ID and dates. The service validates the dates, fetches the room, checks active overlapping bookings, calculates the amount and saves the booking or returns a conflict.”

### Follow-up 5: How did you protect “get my bookings”?

> “The backend should derive the current user from authenticated security context and query only that user’s bookings. It should not trust an arbitrary user ID supplied by the client.”

### Follow-up 6: How do you cancel a booking securely?

> “The service verifies that the booking exists and that the current user owns it or has an authorised admin role. It then checks whether the current status and cancellation policy allow the transition.”

### Follow-up 7: DELETE room or soft-delete room?

> “Hard deletion can conflict with historical bookings and audit needs. A production design often marks the room inactive or unavailable. I should state the implemented behaviour and explain soft deletion as an improvement when relevant.”

### Follow-up 8: Why API versioning?

> “It creates a controlled namespace for breaking changes, such as `/api/v1`, so future clients can migrate rather than unexpectedly breaking.”

### Follow-up 9: How do you document APIs?

> “At minimum through clear DTOs and Postman collections; a stronger system can use OpenAPI/Swagger. I should claim only what exists.”

### Follow-up 10: How many APIs exactly?

> “I will count actual controller mappings before the interview and give the exact number. ‘10+’ is a resume summary, not a substitute for knowing the routes.”

---

# 29. Ownership, Challenges, Bugs and Testing

## 29.1 “Did you build it individually?”

### Individual version

> “I built the project independently, including backend architecture, security integration, database mapping and frontend integration. I used official documentation for Spring Security, JPA and AWS SDK integration, and I can explain the implementation decisions.”

### Team version

> “It was a team project. My primary responsibility was ____. I also understood how my module interacted with ____. I will not claim another teammate’s implementation as my own.”

## 29.2 “Why did you build this project?”

> “I wanted to build a Java backend where business correctness matters, not only basic CRUD. Hotel booking required authentication, role-based operations, relational modelling, date-overlap logic, external image storage and layered architecture.”

## 29.3 Best challenge choices

Choose one genuine challenge.

### Option A — Booking overlap

> “The challenging part was expressing date-range overlap correctly, including boundary cases where one guest checks out on the same date another checks in. I modelled stays as half-open intervals and tested multiple overlap scenarios.”

### Option B — Security filter chain

> “The challenge was making JWT authentication and role authorisation work in the correct order. The filter had to validate the token and establish authentication before route access rules evaluated the role.”

### Option C — JPA relationships

> “The challenge was mapping Users, Rooms and Bookings without recursive JSON or accidental cascade behaviour. DTOs separated API output from bidirectional entity relationships.”

### Option D — S3 + database consistency

> “Uploading an image and saving the Room record involved two systems. I had to consider what happens when one succeeds and the other fails and ensure errors were handled cleanly.”

## 29.4 Bug story framework: S-R-F-T-L

- **S — Symptom:** What was visible?
- **R — Reproduce:** How did you reproduce it?
- **F — Find:** What was the root cause?
- **T — Treatment:** What change fixed it?
- **L — Learning:** What did you learn?

### Example structure

> “The symptom was that a valid Admin received 403 on room creation. I reproduced it with a fresh token and inspected the authorities stored in the SecurityContext. The database value was `ADMIN`, but the security rule expected `ROLE_ADMIN`. I normalised authority creation and route checks, then tested both User and Admin tokens. I learned that role prefixes must remain consistent across storage, token claims and Spring Security.”

Use only when true.

## 29.5 Testing strategy

### Unit tests

Test service logic with mocked repositories:

- valid booking;
- overlap rejection;
- invalid dates;
- room missing;
- unauthorised operation.

### Repository tests

Verify overlap query and mappings against a test database.

### Controller/security integration tests

- public route works;
- missing token returns 401;
- User calling Admin route returns 403;
- valid Admin request succeeds;
- validation returns 400.

### Concurrency test

Send parallel booking requests for the same room/date range and verify only one succeeds under the intended locking design.

### S3 integration

Mock S3 client for unit tests; use an integration environment/emulator/test bucket for controlled integration tests.

## 29.6 If you only used Postman/manual testing

> “I tested successful and failure API cases using Postman and UI flows. I checked invalid tokens, wrong roles, missing fields, invalid date order and overlapping bookings. Automated unit and integration tests are a clear next improvement.”

Do not invent JUnit/Mockito coverage.

---

# 30. Security, Scalability and Improvements

## 30.1 Security checklist

Potential controls:

- BCrypt password hashing;
- short-lived JWT access token;
- server-side role enforcement;
- DTO validation;
- limited error details;
- restricted CORS;
- secrets outside source control;
- least-privilege S3 IAM;
- upload type and size validation;
- SQL injection protection through parameterised JPA queries;
- rate limiting for login;
- HTTPS in production.

**[VERIFY-IN-CODE]** Claim only implemented controls.

## 30.2 SQL injection and JPA

JPA parameter binding reduces SQL-injection risk compared with string concatenation.

However, unsafe native query construction can still be vulnerable.

### Answer

> “I avoid concatenating untrusted input into SQL or JPQL. Repository parameters are bound separately, and validation still applies because parameterisation prevents injection but not invalid business input.”

## 30.3 Mass assignment/over-posting

If the API directly binds a Room entity, a client might submit internal fields such as role, owner or status.

DTOs allow an explicit allow-list of accepted fields.

## 30.4 Scaling read operations

- pagination for room/booking listings;
- suitable indexes;
- DTO projections;
- avoid N+1;
- cache stable room catalog data where useful;
- CDN for room images.

## 30.5 Scaling booking writes

- correct database locking;
- short transactions;
- idempotency keys;
- queue only when business flow allows;
- measure contention per popular room;
- separate read replicas cannot decide write availability safely without consistency considerations.

## 30.6 Horizontal scaling

A stateless JWT API can run multiple instances behind a load balancer.

Shared dependencies:

- MySQL;
- S3;
- central logs/metrics;
- shared refresh-token/revocation store if required.

## 30.7 Observability

Production improvements:

- structured logs with request/booking IDs;
- metrics for booking conflicts and latency;
- error tracking;
- database query monitoring;
- health checks;
- audit log for Admin actions.

## 30.8 Best improvement answer

> “My first improvements would be automated service and security tests, explicit concurrency protection for booking creation, pagination and indexing for growing data, consistent error responses and improved observability. I would also strengthen token lifecycle and S3 cleanup depending on the current implementation.”

## 30.9 Why not microservices immediately?

> “The current domain can remain a well-structured modular monolith. Microservices would add network, deployment, consistency and monitoring complexity. I would separate services only when scale, team ownership or independent deployment provides a clear benefit.”

---

# 31. Dangerous Resume Questions and Safe Answers

## 31.1 “How exactly did you prevent double booking?”

### Safe code-aligned structure

> “The service checked existing active bookings for the selected room using the overlap condition `requestedCheckIn < existingCheckOut` and `requestedCheckOut > existingCheckIn`. If a conflict existed, it rejected the request. I will describe the transaction or locking level exactly as implemented; without explicit concurrency control, this protects sequential requests but not every simultaneous race.”

## 31.2 “So your resume says double bookings are impossible?”

> “The implemented conflict logic prevents normal overlapping booking attempts. A production guarantee under simultaneous requests requires transaction and locking verification. I would avoid claiming an absolute guarantee unless the concurrency path has been tested.”

## 31.3 “Why JWT instead of sessions?”

> “A separate React client and REST API fit token-based stateless authentication well because each request carries its authentication proof. Sessions are also valid and may simplify revocation in some architectures. The choice involves storage, CSRF, revocation and scalability trade-offs.”

## 31.4 “Can anyone read a JWT payload?”

> “Yes, a normal signed JWT payload is only encoded, not encrypted. It should not contain secrets. The signature protects integrity, provided verification is correct.”

## 31.5 “How did you log out a JWT user?”

Choose truth:

- frontend token deletion only;
- denylist;
- refresh-token revocation;
- session version.

Safe version when simple:

> “The current client logout removed its token, but a stolen access token would remain valid until expiry. Production hardening would use short-lived access tokens and a revocable refresh-token design.”

## 31.6 “Why both JPA and Hibernate?”

> “They are different layers. JPA is the persistence specification, Hibernate is the provider implementing it, and Spring Data JPA creates repository abstractions above JPA.”

## 31.7 “What happens when a Room has thousands of Bookings?”

> “I should not serialise the entire bookings collection with every Room. I would keep the association lazy, use pagination or targeted projections and index booking queries by room and dates/status.”

## 31.8 “Why One-to-Many and not Many-to-Many?”

> “User and Room are indirectly many-to-many over time, but Booking has its own dates, status and price. Therefore, Booking is an association entity with two many-to-one relationships.”

## 31.9 “What happens if S3 upload succeeds but MySQL save fails?”

> “They do not share one ACID transaction. The service should perform compensating cleanup of the uploaded object or record it for background cleanup. I will state whether that exists in the current implementation.”

## 31.10 “What does centralised validation mean in your resume?”

> “Input constraints are applied through DTO validation, and known exceptions are mapped consistently through a central handler rather than writing unrelated validation/error formats in every controller. I will name the actual annotations and handler class from the repository.”

## 31.11 “How many APIs exactly?”

Do not say “approximately ten” if asked exactly.

Prepare a counted list grouped by:

- authentication;
- rooms;
- bookings;
- admin/user.

## 31.12 “Did you deploy it?”

The resume shows GitHub but does not show a Live link for Kashi Atithi.

Safe answer if not deployed:

> “The repository is complete, but the resume does not claim a live deployment. I tested the frontend and backend locally and can explain the deployment steps I would use.”

Do not imply a public production deployment if none exists.

---


## 31.13 Interview Follow-Up Decision Trees

This section shows how interview questioning can deepen. Do not answer every level automatically. Answer the current level and stop.

### Decision Tree A — “Tell me about JWT”

**Level 1: What is JWT?**

> “JWT is a compact token format used to carry claims between parties. In Kashi Atithi, it was used in the authentication flow for protected APIs.”

**Level 2: Structure?**

> “It contains header, payload and signature sections.”

**Level 3: Can payload be read?**

> “Yes, a normal signed JWT payload is encoded, not encrypted.”

**Level 4: What do you validate?**

> “Signature, expiration and any configured issuer or audience constraints.”

**Level 5: Logout/revocation?**

> “A client-side delete alone does not revoke a stolen token; production designs use short-lived access tokens and refresh/revocation strategies.”

### Decision Tree B — “How did you prevent double booking?”

**Level 1: Basic logic**

> “I queried active bookings for the same room using a date-overlap condition before saving.”

**Level 2: Formula**

> “New check-in is before existing checkout and new checkout is after existing check-in.”

**Level 3: Boundary**

> “Same-day checkout and next check-in can be non-overlapping under checkout-exclusive semantics.”

**Level 4: Concurrency challenge**

> “Two simultaneous requests can both pass a normal check-then-save flow.”

**Level 5: Stronger solution**

> “Use a transaction with explicit per-room locking or a database reservation-slot model, and test concurrent requests.”

### Decision Tree C — “Explain JPA”

**Level 1: Meaning**

> “JPA is the Java persistence specification used to map entities and perform persistence operations.”

**Level 2: Hibernate?**

> “Hibernate is the implementation used to perform ORM work.”

**Level 3: Spring Data JPA?**

> “It adds repository abstractions and query conventions on top.”

**Level 4: Lazy loading?**

> “Associations can be loaded only when accessed, which can save data but cause N+1 queries.”

**Level 5: Transaction/persistence context?**

> “Managed entities are tracked inside the persistence context and changes can be flushed transactionally.”

### Decision Tree D — “Why S3?”

**Level 1: Reason**

> “To separate room-image objects from relational booking data.”

**Level 2: What is stored in MySQL?**

> “The object key or URL and relevant metadata, not necessarily the binary image.”

**Level 3: Security?**

> “Credentials remain server-side and IAM follows least privilege.”

**Level 4: Failure consistency?**

> “An S3 upload and MySQL transaction are not one atomic transaction, so compensation or cleanup is required.”

### Decision Tree E — “Explain layered architecture”

**Level 1: Layers**

> “Controller handles HTTP, service handles business logic and repository handles persistence.”

**Level 2: Why service?**

> “It keeps booking rules reusable and transaction-aware.”

**Level 3: Why DTO?**

> “It separates API contracts from entities and restricts fields.”

**Level 4: Error flow?**

> “Domain exceptions are mapped centrally into consistent status codes and bodies.”

---

# 32. Rapid-Fire Question Bank

## 32.1 Java

1. Why Java for backend development?
2. JDK vs JRE vs JVM.
3. Compilation and bytecode.
4. Class vs object.
5. Encapsulation.
6. Abstraction.
7. Inheritance.
8. Polymorphism.
9. Interface vs abstract class.
10. Method overloading vs overriding.
11. Checked vs unchecked exception.
12. `final`, `finally`, `finalize`.
13. String immutability.
14. `==` vs `equals()`.
15. `hashCode()` contract.
16. List vs Set.
17. ArrayList vs LinkedList.
18. HashMap working.
19. `Optional`.
20. Stream API.
21. `LocalDate` vs `LocalDateTime`.
22. Why `BigDecimal` for money?
23. Record vs class.
24. Thread safety.
25. Race condition.

## 32.2 Spring and Spring Boot

1. Spring vs Spring Boot.
2. What is IoC?
3. What is DI?
4. What is a bean?
5. Bean lifecycle.
6. Default bean scope.
7. Constructor vs field injection.
8. `@Component` vs `@Service`.
9. `@Repository` purpose.
10. `@SpringBootApplication`.
11. Component scanning.
12. Auto-configuration.
13. Starter dependency.
14. Embedded server.
15. Externalised configuration.
16. Profiles.
17. `application.properties` vs YAML.
18. `CommandLineRunner`.
19. Actuator.
20. Why package main class at root?

## 32.3 Spring MVC and REST

1. `@Controller` vs `@RestController`.
2. `@RequestMapping`.
3. `@GetMapping`/`@PostMapping`.
4. `@PathVariable` vs `@RequestParam`.
5. `@RequestBody`.
6. `ResponseEntity`.
7. JSON serialisation.
8. 200 vs 201.
9. 400 vs 409.
10. 401 vs 403.
11. PUT vs PATCH.
12. Idempotency.
13. Pagination.
14. API versioning.
15. CORS.
16. Multipart request.
17. Content-Type.
18. Controller-Service separation.
19. Global exception handler.
20. Validation error response.

## 32.4 Spring Security and JWT

1. Authentication vs authorisation.
2. What is Spring Security?
3. What is a filter?
4. What is `SecurityFilterChain`?
5. What is `FilterChainProxy`?
6. What is `SecurityContextHolder`?
7. What is `Authentication`?
8. What is `GrantedAuthority`?
9. `UserDetailsService`.
10. `AuthenticationManager`.
11. PasswordEncoder.
12. BCrypt.
13. Hashing vs encryption.
14. JWT structure.
15. Claim.
16. Signature.
17. Signed vs encrypted token.
18. Token expiry.
19. Bearer token.
20. Custom JWT filter.
21. `OncePerRequestFilter`.
22. Filter order.
23. Stateless session.
24. Access vs refresh token.
25. Token storage.
26. JWT logout/revocation.
27. `hasRole` vs `hasAuthority`.
28. Role prefix.
29. Method-level security.
30. CSRF and JWT/cookies.

## 32.5 JPA and Hibernate

1. JPA vs Hibernate.
2. Spring Data JPA.
3. `JpaRepository`.
4. Entity.
5. `@Id`.
6. ID generation strategies.
7. `@Column`.
8. `@Table`.
9. `@OneToMany`.
10. `@ManyToOne`.
11. Owning side.
12. `mappedBy`.
13. `@JoinColumn`.
14. Lazy vs eager.
15. Cascade types.
16. Orphan removal.
17. Persistence context.
18. Dirty checking.
19. Entity states.
20. JPQL vs SQL.
21. Derived query.
22. `@Query`.
23. Native query.
24. N+1 problem.
25. Fetch join.
26. DTO projection.
27. `LazyInitializationException`.
28. Open Session in View.
29. Transaction boundary.
30. Optimistic vs pessimistic locking.

## 32.6 MySQL and booking logic

1. Primary key.
2. Foreign key.
3. Unique constraint.
4. Index.
5. Composite index.
6. Normalisation.
7. JOIN types.
8. ACID.
9. Transaction.
10. Isolation level.
11. Deadlock.
12. Race condition.
13. Date-overlap formula.
14. Boundary check-in/check-out rule.
15. Cancelled booking effect.
16. Prevent duplicate request.
17. Why a unique date tuple is insufficient.
18. Pessimistic lock.
19. Optimistic lock.
20. `@Transactional` limitation.
21. Why Booking is a separate entity.
22. Money calculation.
23. SQL injection.
24. Query execution plan.
25. Pagination.

## 32.7 AWS S3

1. What is object storage?
2. Bucket.
3. Object key.
4. Metadata.
5. Why S3 instead of MySQL BLOB?
6. Public URL vs presigned URL.
7. AWS SDK.
8. IAM.
9. Least privilege.
10. Credential provider chain.
11. Multipart upload.
12. File validation.
13. Unique object key.
14. Same-key overwrite/versioning.
15. S3 upload failure.
16. DB failure after upload.
17. Delete old image.
18. CDN/CloudFront.
19. Server-side encryption.
20. Bucket policy vs IAM policy.

## 32.8 Maven

1. What is Maven?
2. POM.
3. Coordinates.
4. Dependency.
5. Transitive dependency.
6. Scope.
7. Plugin.
8. Lifecycle.
9. Phase vs goal.
10. `mvn clean`.
11. `mvn test`.
12. `mvn package`.
13. `mvn install`.
14. `mvn deploy`.
15. Executable JAR.
16. Maven repository.
17. Dependency conflict.
18. `dependency:tree`.
19. Maven vs Gradle.
20. Spring Boot parent/BOM.

## 32.9 Project ownership and HR crossover

1. Why this project?
2. What was your contribution?
3. Hardest bug.
4. Most important learning.
5. One design decision you would change.
6. How did you test?
7. What did you learn from documentation?
8. How would you deploy it?
9. What would fail at high scale?
10. Why should Cognizant consider you for GenC Next?

---

# 33. What Not to Say

## Weak Spring Boot answer

> “Spring Boot is used to make APIs.”

Better:

> “Spring Boot simplifies building Spring applications through auto-configuration, starter dependencies and an embedded server. I used it to structure and run the REST backend.”

## Weak JPA answer

> “JPA connects Java to MySQL.”

Better:

> “JPA defines the object-relational persistence contract, Hibernate implements it and Spring Data JPA provides repository abstractions.”

## Weak conflict answer

> “I checked whether the room was available.”

Better:

> “I searched active bookings for the same room using a date-overlap condition before insertion. I also recognise that simultaneous requests require transaction-level concurrency control.”

## Weak JWT answer

> “JWT encrypts user data.”

Correct:

> “A typical signed JWT protects integrity but does not encrypt the payload. The payload can be decoded, so secrets should not be placed in it.”

## Weak role answer

> “Admin page was hidden from users.”

Correct:

> “The UI can hide Admin actions, but the backend SecurityFilterChain must reject a User token on Admin endpoints.”

## Dangerous ORM answer

> “Hibernate means I do not need SQL.”

Correct:

> “Hibernate reduces mapping boilerplate, but I still need SQL and database knowledge to understand indexes, joins, transactions and generated-query performance.”

## Dangerous double-booking claim

> “Double booking can never happen.”

Correct:

> “The overlap logic prevents normal conflicts. An absolute concurrency guarantee depends on the actual transaction and locking implementation.”

## Dangerous S3 answer

> “S3 is a database for images.”

Better:

> “S3 is object storage. MySQL stores structured room data and an S3 object reference.”

## Dangerous project label

> “It is microservices.”

Correct unless independently deployed:

> “It is a modular layered monolith.”

## Avoid filler

Reduce:

- basically;
- actually;
- like;
- something;
- etcetera;
- I think, when certain.

Use:

- “The request flow was…”
- “The service validated…”
- “The repository queried…”
- “The security filter established…”
- “The trade-off was…”
- “The current limitation is…”

---

# 34. Final Repository Verification Sheet

Fill this section directly from the code.

## 34.1 Project identity

- Repository URL:
- Individual/team:
- My exact contribution:
- Development duration:
- Main problem solved:
- Target users:
- Hardest module:
- Best learning:

## 34.2 Java and Spring Boot

- Java version:
- Spring Boot version:
- Main application class:
- Package structure:
- Injection style:
- Important beans:
- Profiles:
- Main configuration files:

## 34.3 Security

- Security configuration class:
- `SecurityFilterChain` bean method:
- JWT filter class:
- Filter base class:
- Filter placement:
- JWT library/dependency:
- JWT subject:
- Custom claims:
- Expiry:
- Signing algorithm/key handling:
- `UserDetailsService` class:
- Password encoder:
- Session policy:
- Public endpoints:
- User endpoints:
- Admin endpoints:
- 401 handler:
- 403 handler:
- Refresh token:
- Logout implementation:

## 34.4 Controllers and APIs

- Auth controller:
- Room controller:
- Booking controller:
- Admin controller:
- Exact total API count:
- Request DTOs:
- Response DTOs:
- Multipart endpoints:
- Pagination endpoints:

## 34.5 Service layer

- Authentication service:
- Room service:
- Booking service:
- S3 service:
- Mapping method/library:
- Transactional methods:
- Important business-rule method:

## 34.6 Validation and errors

- Validation annotations:
- Cross-field date validation:
- Global handler class:
- Custom exception classes:
- Error response fields:
- Booking conflict status:

## 34.7 JPA entities

### User

- Table:
- Primary key:
- Fields:
- Role type:
- Booking relationship:
- Cascade/fetch:

### Room

- Table:
- Primary key:
- Fields:
- Image field:
- Booking relationship:
- Cascade/fetch:

### Booking

- Table:
- Primary key:
- User mapping:
- Room mapping:
- Check-in/check-out type:
- Status:
- Total price type:
- Created timestamp:

## 34.8 Repositories

- User repository methods:
- Room repository methods:
- Booking repository methods:
- Overlap method/query:
- Lock annotation:
- Custom JPQL:
- Native SQL:
- Pagination:

## 34.9 Booking concurrency

- Date interval rule:
- Exact overlap expression:
- Same-day checkout/check-in allowed:
- Active statuses:
- Cancelled booking handling:
- `@Transactional`:
- Isolation:
- Pessimistic lock:
- Optimistic `@Version`:
- Database constraint:
- Parallel-request test result:
- Current limitation:

## 34.10 AWS S3

- SDK version:
- Client bean/config:
- Bucket property:
- Object key format:
- Upload method:
- File validation:
- Stored URL or key:
- Public/presigned delivery:
- Delete method:
- Compensation on DB failure:
- Credential source:

## 34.11 MySQL

- MySQL version:
- Database name:
- Driver:
- DDL mode/migrations:
- Foreign keys:
- Unique constraints:
- Indexes:
- Isolation setting:
- Sample join query:

## 34.12 Maven

- `groupId`:
- `artifactId`:
- Java compiler version:
- Parent/BOM:
- Important dependencies:
- JWT dependency:
- AWS SDK dependency:
- Test dependencies:
- Build plugin:
- Package command:
- Run command:

## 34.13 React

- Pages/routes:
- API client:
- Axios or fetch:
- Token storage:
- Auth state:
- Protected route:
- Admin UI:
- Booking form:
- Image upload UI:
- CORS development origin:

## 34.14 Testing and deployment

- Postman collection:
- Unit tests:
- Integration tests:
- Security tests:
- Concurrency test:
- Local ports:
- Deployment status:
- Production environment variables:
- Hardest production/local bug:

---

# 35. Official References

These references support general technical explanations. They do not prove that a feature exists in Kashi Atithi; the repository remains the source of truth for implementation claims.

## Spring Boot and Spring Framework

1. Spring Boot Reference Documentation  
   https://docs.spring.io/spring-boot/reference/

2. Spring Beans and Dependency Injection  
   https://docs.spring.io/spring-boot/reference/using/spring-beans-and-dependency-injection.html

3. Running a Spring Boot Application  
   https://docs.spring.io/spring-boot/reference/using/running-your-application.html

4. Spring `@RestController` API  
   https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/bind/annotation/RestController.html

## Spring Security

5. Spring Security Reference  
   https://docs.spring.io/spring-security/reference/

6. Servlet Security Architecture  
   https://docs.spring.io/spring-security/reference/servlet/architecture.html

7. Servlet Authentication Architecture  
   https://docs.spring.io/spring-security/reference/servlet/authentication/architecture.html

## JWT

8. IETF RFC 7519 — JSON Web Token  
   https://datatracker.ietf.org/doc/html/rfc7519

## Jakarta Persistence and Hibernate

9. Jakarta Persistence Specification  
   https://jakarta.ee/specifications/persistence/

10. Jakarta Persistence `OneToMany`  
    https://jakarta.ee/specifications/persistence/3.2/apidocs/jakarta.persistence/jakarta/persistence/onetomany

11. Jakarta Persistence `ManyToOne`  
    https://jakarta.ee/specifications/persistence/3.2/apidocs/jakarta.persistence/jakarta/persistence/manytoone

12. Hibernate ORM Documentation  
    https://hibernate.org/orm/documentation/

## MySQL

13. MySQL Reference Manual  
    https://dev.mysql.com/doc/refman/en/

14. InnoDB Transaction Isolation Levels  
    https://dev.mysql.com/doc/refman/8.4/en/innodb-transaction-isolation-levels.html

## AWS S3

15. Amazon S3 — Uploading Objects  
    https://docs.aws.amazon.com/AmazonS3/latest/userguide/upload-objects.html

16. AWS SDK for Java 2.x Developer Guide — S3  
    https://docs.aws.amazon.com/sdk-for-java/latest/developer-guide/examples-s3.html

## Maven

17. Maven Dependency Mechanism  
    https://maven.apache.org/guides/introduction/introduction-to-dependency-mechanism.html

18. Maven Build Lifecycle  
    https://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html

19. Maven Getting Started Guide  
    https://maven.apache.org/guides/getting-started/

## React

20. React Documentation  
    https://react.dev/learn

---

# Final Interview Principle

Your confidence should come from **clear reasoning and code-backed truth**, not from speaking continuously.

For each answer:

> **Meaning → root problem → project implementation → benefit → stop.**

For a deeper follow-up:

> **Explain one internal layer → connect it to an actual class/query → state the limitation honestly.**

The strongest Kashi Atithi answer is not:

> “I completely prevented every possible double booking.”

It is:

> “I implemented date-overlap validation for active bookings, and I understand that a production-level guarantee under simultaneous requests requires verified transaction and locking behaviour.”

That answer demonstrates both project ownership and engineering maturity.
