# Architecture

## Current Local Architecture

The backend now uses a layered Spring Boot architecture with PostgreSQL persistence:

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

Database schema changes are managed by Flyway:

```text
Flyway
   ↓
PostgreSQL schema
```

The current API endpoint is:

```http
GET /api/races
```

It currently reads race records from the `races` table in PostgreSQL.

## Current Backend Responsibilities

### Controller

`RaceController` is responsible for the HTTP/API boundary.

Current responsibility:

* expose `GET /api/races`
* delegate the operation to `RaceService`
* return the result as an HTTP response

The Controller should not contain database access or the main business logic.

### Service

`RaceService` represents the application/service layer.

Current responsibility:

* coordinate retrieval of races
* call `RaceRepository`

As business rules are introduced, they should be placed here when they belong to the application/service layer rather than the HTTP or persistence layers.

### Repository

`RaceRepository` extends Spring Data JPA's `JpaRepository<Race, Long>`.

Current responsibility:

* provide persistence operations for `Race`
* delegate database access to Spring Data JPA/Hibernate

The previous `InMemoryRaceRepository` implementation has been removed because PostgreSQL is now the persistence mechanism.

## Database Architecture

The application uses PostgreSQL locally.

The database schema is managed by Flyway rather than being created manually or relying on Hibernate to change the schema automatically.

Current migrations:

```text
V1__create_races_table.sql
    ↓
creates races table

V2__insert_initial_races.sql
    ↓
inserts initial race data
```

Flyway also maintains:

```text
flyway_schema_history
```

which records applied migrations.

## Entity Mapping

`Race` is a JPA entity mapped to the PostgreSQL `races` table.

```text
Race Java entity
      ↕
Hibernate / JPA
      ↕
races PostgreSQL table
```

The entity contains a no-argument constructor required by JPA/Hibernate and a parameterized constructor for normal application use.

## API Flow

For the current request:

```text
HTTP GET /api/races
        ↓
RaceController
        ↓
RaceService
        ↓
RaceRepository
        ↓
Spring Data JPA
        ↓
Hibernate
        ↓
PostgreSQL
        ↓
Race objects
        ↓
HTTP response
```

## Security Principles

Security is a project requirement from the beginning, not a later add-on.

In particular:

* secrets must not be hardcoded in source code
* database passwords are supplied through environment variables
* sensitive configuration should be separated from application source code
* database access should use the minimum required permissions when the deployment architecture is introduced
* network access should be restricted rather than exposed unnecessarily
* authentication and authorization will be introduced deliberately when the application requires them
* production infrastructure should use managed secret storage where appropriate
* security decisions should be documented together with architectural trade-offs

The local configuration currently uses:

```properties
spring.datasource.password=${DB_PASSWORD}
```

The actual database password is therefore not stored in `application.properties`.

## Target Cloud Architecture

The final architecture is expected to evolve toward a professional AWS architecture.

The exact architecture has not yet been selected.

Expected major components include:

```text
User
  ↓
CloudFront / frontend hosting
  ↓
Gateway / Load Balancer
  ↓
Backend containers
  ↓
PostgreSQL
```

The gateway architecture will be evaluated later.

Possible approaches include:

* API Gateway
* Application Load Balancer
* API Gateway + Application Load Balancer

The final choice will be based on:

* architectural requirements
* security
* scalability
* operational complexity
* cost
* what concept the architecture is intended to teach

## Architectural Principles

### Programming

Use:

* KISS (Keep It Simple, Stupid)
* DRY (Don't Repeat Yourself)

Avoid abstractions that do not solve a real problem.

### Architecture and Engineering Quality

The project is intentionally **not developed using a "make it work at any cost" approach**.

Each implementation should aim to reflect how a real application would be built in a professional engineering environment, while keeping the scope appropriate for a learning project.

This means:

* prefer established and understandable patterns
* understand why a component exists before introducing it
* consider maintainability and testability
* consider security from the beginning
* avoid shortcuts that create technical debt without a clear reason
* evaluate alternatives and trade-offs
* do not introduce complexity only for the sake of looking enterprise-like
* keep the implementation as simple as possible without compromising sound engineering practices

Architecture may be more sophisticated than strictly necessary for this application when the additional complexity teaches an important professional concept.

Every architectural component should have a clear reason for existing.

### Cost

AWS infrastructure costs should be kept as low as reasonably possible for a learning project.

When a solution introduces additional cost, cheaper alternatives and their trade-offs should be considered.
