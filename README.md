# Trail Races — Learning Project

Educational project for learning software architecture, Java, Spring Boot, Angular, AWS, infrastructure, security and CI/CD.

## Project

Trail Races is a web application for discovering and browsing mountain running races.

The application will provide information about races, including:

* race name
* date
* location
* organizer
* available distances
* elevation gain
* race type
* price
* currency
* ITRA (International Trail Running Association) points
* description
* website

The application is being developed incrementally, starting from a simple local application and evolving toward a professional cloud architecture.

## Technology

### Frontend

* Angular
* Angular Material
* SCSS (Sassy Cascading Style Sheets)
* BEM (Block Element Modifier)
* Signal Store

### Backend

* Java 21
* Spring Boot 4.1.1
* Maven
* Spring Data JPA
* Hibernate

### Database

* PostgreSQL
* Flyway

### Infrastructure

* AWS (Amazon Web Services)
* AWS CDK (Cloud Development Kit)
* Docker

### CI/CD

* GitHub Actions
* CI (Continuous Integration)
* CD (Continuous Delivery / Continuous Deployment)

## Learning Goals

The main goal is not only to build the application, but to understand:

* application architecture
* backend and frontend communication
* networking
* cloud infrastructure
* AWS services
* Infrastructure as Code
* containerization
* CI/CD
* security
* scalability
* observability
* architectural decision-making
* database persistence and migrations

The architecture should reflect professional patterns used in larger organizations where appropriate, while keeping infrastructure costs as low as reasonably possible for a learning project.

## Engineering and Security Principles

This project is **not a "make it work at any cost" project**.

The goal is to learn how a real application is designed, implemented and operated in a professional engineering environment.

The implementation should therefore:

* follow good engineering practices
* consider security from the beginning
* never hardcode secrets
* use appropriate access control and least privilege
* avoid unnecessary network exposure
* validate and handle external input deliberately
* prefer maintainable and testable solutions
* document important architecture and security decisions
* evaluate alternatives and trade-offs
* avoid unnecessary enterprise complexity
* keep AWS costs under control

Security is not something that will be added only at the end of the course. Security considerations should influence decisions throughout the project, especially when introducing databases, networking, authentication, secrets and AWS infrastructure.

## Learning Approach

The project is the practical learning environment.

The application will be developed incrementally and each implementation step should be used to understand the architectural concepts behind it.

A typical learning cycle is:

```text
Understand the reason
        ↓
Make one small change
        ↓
Run tests / application
        ↓
Verify the result
        ↓
Understand what happened
        ↓
Document the durable knowledge
        ↓
Continue
```

Programming should follow:

* KISS (Keep It Simple, Stupid)
* DRY (Don't Repeat Yourself)
* avoid unnecessary abstractions
* introduce design patterns only when they solve a real problem

Architecture should focus on professional patterns, trade-offs and the reasons behind architectural decisions.

For AWS and infrastructure, cost should always be considered. When a solution introduces cost, cheaper alternatives and their trade-offs should be discussed.

## Current Backend State

The local backend currently follows:

```text
Client
   ↓
RaceController
   ↓
RaceService
   ↓
RaceRepository
   ↓
Spring Data JPA / Hibernate
   ↓
PostgreSQL
```

Database schema is managed by Flyway migrations.

Current migrations:

```text
V1__create_races_table.sql
V2__insert_initial_races.sql
```

The current API is:

```http
GET /api/races
```

The endpoint reads race data from PostgreSQL.

The database password is supplied through the `DB_PASSWORD` environment variable and is not stored in `application.properties`.

## Documentation Workflow

The Markdown files in this repository are the durable source of project and learning context.

Do not invent, rename, or reinterpret existing file names or their purposes. Before updating documentation, inspect the current repository and preserve the existing file names, structure and roles.

When a learning session ends or a repository summary is requested:

1. Inspect the current repository first.
2. Identify only the Markdown files that actually need changes.
3. For every changed file, provide its complete current content.
4. Provide each changed file separately in one standalone Markdown code block.
5. The complete file content must be ready to copy using the Copy button and replace the entire existing file.
6. Never provide partial snippets, abbreviated fragments, examples presented as complete files, or only the changed section.
7. Do not provide unchanged files.
8. Do not provide unrelated files.
9. Do not put explanations, comments about changes, or other prose inside the file contents.
10. Do not create new files unless explicitly requested or genuinely required by the project.

The expected workflow is:

```text
Inspect repository
      ↓
Determine which Markdown files changed
      ↓
Show only changed files
      ↓
Each file = complete replacement content
      ↓
Copy
      ↓
Replace entire file
      ↓
Save
```

## Current API

The backend currently exposes:

```http
GET /api/races
```

The API is being developed incrementally.

The planned MVP (Minimum Viable Product) functionality is:

* list of races
* text search
* distance range filtering
* pagination

### Search

The `search` parameter searches only String fields:

* `name`
* `location`
* `currency`
* `description`
* `websiteUrl`

It does not search numerical or date fields.

### Distance filtering

Distance is filtered separately using:

* `distanceFrom`
* `distanceTo`

Example:

```http
GET /api/races?search=tatry&distanceFrom=20&distanceTo=50&page=1&size=20
```

The detailed API semantics are documented in `KNOWLEDGE.md` and `DECISIONS.md`.

## Running the Backend

### Requirements

* Java 21
* Maven
* PostgreSQL
* `DB_PASSWORD` environment variable configured for the local PostgreSQL user

The project includes Maven Wrapper, so Maven does not need to be installed separately.

### Windows

Run:

```bash
.\mvnw.cmd spring-boot:run
```

### macOS / Linux

Run:

```bash
./mvnw spring-boot:run
```

The application starts on:

```text
http://localhost:8080
```

### Build

Windows:

```bash
.\mvnw.cmd clean package
```

macOS / Linux:

```bash
./mvnw clean package
```

### Tests

Windows:

```bash
.\mvnw.cmd test
```

macOS / Linux:

```bash
./mvnw test
```
