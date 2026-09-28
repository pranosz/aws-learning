# Current State

## Project

Trail Races — educational mountain running race platform.

The project is being used as a practical learning environment for Java, Spring Boot, PostgreSQL, AWS, architecture, security, Docker and CI/CD.

## Current Phase

Phase 1 — Application foundations

## Current Lesson

Angular frontend foundations and integration with the existing Spring Boot REST API, following the current Angular Style Guide and the project's KISS/DRY principles.

The backend foundation is already implemented with Spring Boot, JPA, PostgreSQL and Flyway.

## Current Goal

Build the frontend incrementally on top of the existing backend while understanding the architectural boundaries between the Angular application and the Spring Boot API. Use professional engineering and security practices rather than shortcuts whose only purpose is to make the application run.

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

## Backend Structure

The current backend contains:

```text
com.trailraces
├── TrailRacesApplication.java
└── race
    ├── Race.java
    ├── RaceController.java
    ├── RaceRepository.java
    └── RaceService.java
```

The previous `InMemoryRaceRepository` has been removed after introducing PostgreSQL persistence.

## Backend

* Java: 21.0.5 LTS
* Spring Boot: 4.1.1
* Build tool: Maven
* Packaging: Jar
* Group: `com.trailraces`
* Artifact: `trail-races`

Current dependencies include:

* Spring Web MVC
* Spring Data JPA
* PostgreSQL JDBC driver
* Spring Boot Flyway starter
* Flyway PostgreSQL database support

## Database

PostgreSQL is now implemented locally.

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

Flyway is now used for database schema migrations.

Current migrations:

```text
V1__create_races_table.sql
V2__insert_initial_races.sql
```

`V1` creates the `races` table.

`V2` inserts three initial race records.

Flyway maintains:

```text
flyway_schema_history
```

The application startup log confirms that both migrations are validated and that the schema is up to date.

## Race Entity

`Race` is now a JPA entity:

```java
@Entity
@Table(name = "races")
```

The primary key uses generated identity values:

```java
@Id
@GeneratedValue(strategy = GenerationType.IDENTITY)
private Long id;
```

The entity has a no-argument constructor required by JPA/Hibernate and a parameterized constructor for normal application use.

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

`RaceRepository` is now a Spring Data JPA repository:

```java
public interface RaceRepository extends JpaRepository<Race, Long> {
}
```

Spring Data provides the repository implementation automatically.

The application no longer uses an in-memory repository.

## Service

`RaceService` is implemented and annotated with `@Service`.

It receives `RaceRepository` through constructor injection and currently provides:

```text
getAllRaces()
```

which delegates to `raceRepository.findAll()`.

## API

The backend currently exposes:

```http
GET /api/races
```

The endpoint now reads records from PostgreSQL and returns the race data as JSON.

The endpoint has been manually verified locally after introducing JPA and Flyway.

## Initial Data

The current Flyway `V2` migration inserts three example races:

* Tatra Sky Marathon
* Beskid Ultra Trail
* Alpine Trail Run

These are development/learning data, not production race data.

## MVP API Requirements

The MVP API should support:

* list of races
* one text search
* distance range filtering
* pagination

### Search

The `search` parameter searches only these String fields:

* `name`
* `location`
* `currency`
* `description`
* `websiteUrl`

It does not search:

* `distance`
* `elevation`
* `price`
* `itra`
* `date`

### Distance filtering

The API will use:

* `distanceFrom`
* `distanceTo`

Example:

```http
GET /api/races?distanceFrom=20&distanceTo=50
```

### Combined example

```http
GET /api/races?search=tatry&distanceFrom=20&distanceTo=50&page=1&size=20
```

### Pagination

The planned response is:

```json
{
  "content": [],
  "page": 1,
  "size": 20,
  "totalElements": 47,
  "totalPages": 3
}
```

Pagination is not implemented yet.

## Domain Decision

One database record represents one race distance.

If one event offers:

* 25 km
* 50 km
* 100 km

the current model treats these as three `Race` records.

## Frontend

The Angular frontend has now been created and runs locally at:

```text
http://localhost:4200/
```

Current frontend versions:

* Angular: 22.2.0
* Angular CLI: 22.2.0
* Angular Material: 22.2.0
* Angular CDK (Component Dev Kit): 22.2.0
* Node.js: 22.22.3
* npm: 10.9.8
* TypeScript: 6.0.3
* RxJS: 7.8.2
* Vitest: 5.0.2

Frontend configuration decisions:

* standalone Angular components
* strict TypeScript configuration
* SCSS (Sassy Cascading Style Sheets)
* BEM (Block Element Modifier) for component styling where appropriate
* Angular Material for UI components
* Vitest for unit tests
* SSR (Server-Side Rendering) and SSG (Static Site Generation) disabled for the initial application
* Angular AI integration generated configuration for OpenAI Codex

The frontend follows the current Angular Style Guide as the baseline for code organization and implementation decisions. The planned structure is feature-based rather than organized into generic `components`, `services` or `directives` folders. Related component files and tests stay together.

The current feature structure is being prepared as:

```text
src/app/
├── races/
│   └── race-list/
│       ├── race-list.ts
│       ├── race-list.html
│       ├── race-list.scss
│       └── race-list.spec.ts
├── app.ts
├── app.html
├── app.scss
├── app.config.ts
├── app.routes.ts
└── app.spec.ts
```

Signal Store is planned but has not been added yet. It will be introduced when a real application state-management requirement exists rather than being added in advance.

The next frontend step is to connect the race list to the existing `GET /api/races` backend endpoint.

## AWS

An AWS account exists.

No AWS infrastructure has been created yet.

Planned infrastructure includes:

* AWS CDK
* Docker
* AWS networking
* container deployment
* managed PostgreSQL
* frontend hosting
* gateway/load balancing
* CI/CD

## CI/CD

CI/CD (Continuous Integration / Continuous Delivery) has not been implemented yet.

Planned technology:

* GitHub Actions

## Security Status

Security is treated as a first-class project requirement.

Current security-related practice:

* database password is supplied through `DB_PASSWORD`
* the password is not stored in `application.properties`
* secrets should not be committed to Git
* production secret management will be addressed before cloud deployment
* network exposure and access permissions will be designed deliberately rather than opened for convenience

## Engineering Approach

The project is intentionally **not developed using a "byleby coś odpalić" / "make it work at any cost" approach**.

The target is to learn how a real engineering team would build the application:

* use good practices appropriate to the problem
* keep the code simple, but not careless
* introduce abstractions only when they solve a real problem
* consider security from the beginning
* avoid hardcoded secrets
* understand trade-offs before selecting infrastructure or architectural components
* prefer maintainable and testable solutions
* document important architectural decisions
* keep AWS costs under control

The fact that this is a learning project does not mean that security and engineering quality should be ignored.

## What I Understand

* Spring Boot can expose REST endpoints.
* `@RestController` is used for REST controllers.
* `@GetMapping` maps a GET request to a controller method.
* A Controller should focus on HTTP/API concerns.
* A Service can coordinate application operations and business logic.
* A Repository is responsible for persistence/data access.
* Constructor injection can be used to provide dependencies.
* Spring Data JPA can provide repository implementations automatically.
* JPA entities map Java objects to database tables.
* Hibernate is used by Spring Data JPA for ORM (Object-Relational Mapping).
* JPA/Hibernate requires a no-argument constructor for the entity.
* Flyway manages versioned database migrations.
* PostgreSQL now stores the application data locally.
* Database passwords should not be hardcoded in source configuration.

## What I Don't Understand Yet

* How Spring Data derives or executes more complex repository queries.
* How API filtering and pagination should be implemented efficiently with PostgreSQL.
* How transactions should be used in the application.
* How validation should be implemented at the API boundary.
* How API errors should be represented consistently.
* How indexes affect PostgreSQL query performance.
* How the local architecture should evolve toward the AWS architecture.
* How authentication and authorization should be introduced securely.
* How secrets should be managed in AWS.
* How network access should be restricted in AWS.

## Known Technical Follow-up

Spring Boot currently reports that `spring.jpa.open-in-view` is enabled by default.

This warning has not been addressed yet. It should be evaluated deliberately rather than changed simply to remove the warning.

## Next Step

Continue Phase 1 by connecting the Angular frontend to the existing backend API. The next functional flow is:

```text
Angular RaceList
      ↓
HTTP (Hypertext Transfer Protocol) request
      ↓
GET /api/races
      ↓
Spring Boot
      ↓
PostgreSQL
```

The frontend should first consume the existing API before adding further state-management or architectural abstractions. After the basic integration works, the existing backend search, distance filtering and pagination capabilities will be connected to the frontend.
