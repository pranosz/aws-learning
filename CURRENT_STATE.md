# Current State

## Project

Trail Races — educational mountain running race platform.

The project is being used as a practical learning environment for Java, Spring Boot, PostgreSQL, AWS, networking, infrastructure, security, Docker and CI/CD.

The application itself is not the main learning goal. It provides a realistic system that can be containerized, deployed, secured, monitored and automated.

## Current Phase

Phase 1 — Application foundations is complete for the current MVP.

The next phase is Docker and containerization, followed by AWS networking and infrastructure.

## Current Status

The local application is working end-to-end:

```text
Angular
   ↓
Spring Boot REST API
   ↓
PostgreSQL
```

The frontend is sufficiently complete for the current learning goal. Further frontend features are intentionally postponed.

## Current Local Architecture

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

Database schema management:

```text
Flyway
   ↓
PostgreSQL schema
```

## Backend

* Java: 21.0.5 LTS
* Spring Boot: 4.1.1
* Build tool: Maven
* Packaging: Jar
* Group: `com.trailraces`
* Artifact: `trail-races`

Current backend capabilities:

* `GET /api/races`
* text search
* distance range filtering
* pagination
* sorting

Pagination and sorting are implemented using Spring Data `Pageable`.

The service layer combines optional search and distance specifications before passing the query and `Pageable` to the repository.

## Database

PostgreSQL is implemented locally.

Current database:

```text
trail_races
```

The local PostgreSQL version observed during application startup is 15.5.

The application connects using:

```text
jdbc:postgresql://localhost:5432/trail_races
```

The database password is not stored directly in source configuration. The application uses:

```properties
spring.datasource.password=${DB_PASSWORD}
```

The actual password is supplied through the `DB_PASSWORD` environment variable.

## Flyway

Flyway is used for database schema migrations.

The repository currently documents the original migrations:

```text
V1__create_races_table.sql
V2__insert_initial_races.sql
```

The database also contains enough development data to exercise the implemented pagination.

Flyway maintains:

```text
flyway_schema_history
```

Manual database changes are not used as a replacement for versioned migrations.

## Race Entity

`Race` is a JPA entity mapped to the `races` table.

Current fields:

* `id`
* `name`
* `distance`
* `elevation`
* `location`
* `date`
* `price`
* `currency`
* `itra`
* `description`
* `websiteUrl`

Current Java types:

* `Long` for `id`
* `String` for text fields
* `Double` for `distance`
* `Integer` for `elevation`
* `LocalDate` for `date`
* `BigDecimal` for `price`

## Repository

`RaceRepository` is a Spring Data JPA repository using:

```java
JpaRepository<Race, Long>
JpaSpecificationExecutor<Race>
```

The repository supports standard persistence operations together with dynamic specifications used by search and distance filtering.

## Service

`RaceService` is implemented and annotated with `@Service`.

It currently:

* coordinates race retrieval
* combines optional filtering criteria
* passes `Pageable` to the repository
* returns a paginated `Page<Race>`

## API

The backend exposes:

```http
GET /api/races
```

Supported query parameters:

```text
search
distanceFrom
distanceTo
page
size
sort
```

Example:

```http
GET /api/races?search=tatry&distanceFrom=20&distanceTo=80&page=0&size=10&sort=distance,desc
```

Search is applied to:

* `name`
* `location`
* `currency`
* `description`
* `websiteUrl`

Distance filtering uses inclusive boundaries:

```text
distance >= distanceFrom
distance <= distanceTo
```

The backend validates:

* `distanceFrom >= 0`
* `distanceTo >= 0`
* `distanceFrom <= distanceTo` when both values are provided

The API returns HTTP 400 for invalid search/filter input.

## Frontend

The Angular frontend runs locally at:

```text
http://localhost:4200/
```

Current frontend versions:

* Angular: 22.2.0
* Angular CLI: 22.2.0
* Angular Material: 22.2.0
* Angular CDK (Component Development Kit): 22.2.0
* Node.js: 22.22.3
* npm: 10.9.8
* TypeScript: 6.0.3
* RxJS: 7.8.2
* Vitest: 5.0.2

Frontend decisions:

* standalone Angular components
* strict TypeScript configuration
* SCSS
* BEM
* Angular Material
* Vitest
* feature-based code organization
* lazy-loaded `/races` route
* SSR (Server-Side Rendering) and SSG (Static Site Generation) disabled for the initial application
* Signal Store is not currently required

The current race list provides:

* search
* distance filtering
* server-side pagination
* server-side sorting
* loading state
* error state
* race cards

The frontend uses Angular's development proxy so `/api/**` requests are forwarded to the local Spring Boot application.

## Frontend Architecture

Current feature structure:

```text
src/app/
├── races/
│   ├── race.ts
│   ├── race-page.ts
│   ├── race-api.ts
│   ├── race-search-criteria.ts
│   ├── race-list/
│   │   ├── race-list.ts
│   │   ├── race-list.html
│   │   ├── race-list.scss
│   │   └── race-list.spec.ts
│   └── race-card/
│       ├── race-card.ts
│       ├── race-card.html
│       ├── race-card.scss
│       └── race-card.spec.ts
├── app.ts
├── app.html
├── app.scss
├── app.config.ts
├── app.routes.ts
└── app.spec.ts
```

## AWS

An AWS account exists.

No final production AWS architecture has been selected yet.

Planned infrastructure includes:

* AWS networking
* VPC
* subnets
* route tables
* Internet Gateway
* NAT
* Security Groups
* IAM
* container deployment
* managed PostgreSQL
* frontend hosting
* gateway/load balancing
* CI/CD
* monitoring

AWS infrastructure will be introduced deliberately rather than created before the underlying concepts are understood.

## CI/CD

CI/CD is not implemented yet.

Planned technology:

* GitHub Actions

Planned high-level flow:

```text
Git push
   ↓
GitHub Actions
   ↓
build / validation / tests
   ↓
Docker image
   ↓
container registry
   ↓
AWS deployment
```

## Security Status

Security is treated as a first-class project requirement.

Current security-related practice:

* database password is supplied through `DB_PASSWORD`
* the password is not stored in `application.properties`
* secrets should not be committed to Git
* production secret management will be addressed before cloud deployment
* network exposure and access permissions will be designed deliberately
* validation is applied at the API boundary

## Engineering Approach

The project is intentionally **not developed using a "byleby coś odpalić" / "make it work at any cost" approach**.

The target is to learn how a real engineering team would build and operate the application:

* use good practices appropriate to the problem
* keep the code simple, but not careless
* introduce abstractions only when they solve a real problem
* consider security from the beginning
* avoid hardcoded secrets
* understand infrastructure trade-offs
* prefer maintainable and testable solutions
* document important architectural decisions
* keep AWS costs under control

The learning project may use production-like patterns even when a simpler solution would be enough for the application itself, but every additional component must have a clear learning or engineering reason.

## Testing

The current frontend and backend have working test configurations.

The user has intentionally postponed the broader test-quality pass until the application foundation is complete. Tests should be reviewed and expanded at the end of the current application-foundation stage rather than interrupting each infrastructure learning step.

## Current Next Step

The next implementation stage is:

**Docker and containerization**

The immediate goal is to understand and implement:

* Docker image
* Dockerfile
* container
* environment variables
* container networking
* Docker Compose
* reproducible local startup

After Docker, continue with AWS networking and infrastructure.
