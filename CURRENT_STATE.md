# Current State

## Project

Trail Races — educational mountain running race platform.

## Current Phase

Phase 1 — Application foundations

## Current Lesson

Spring Boot REST API and basic backend structure.

## Current Goal

Introduce a Service layer and understand the responsibility of the Controller, Service and Repository layers before implementing the next backend step.

## Current Architecture

The current local architecture is still simple:

```text
Client
   ↓
Spring Boot Controller
   ↓
Race data
```

The backend currently contains:

```text
com.trailraces
├── TrailRacesApplication.java
└── race
    ├── Race.java
    └── RaceController.java
```

The next backend step is to evolve the structure toward:

```text
Client
   ↓
Controller
   ↓
Service
   ↓
Repository
   ↓
PostgreSQL
```

The Service and Repository layers have not yet been implemented.

## Backend

* Java: 21.0.5 LTS
* Spring Boot: 4.1.1
* Build tool: Maven
* Packaging: Jar
* Group: `com.trailraces`
* Artifact: `trail-races`
* Package: `com.trailraces`
* Dependency currently used: Spring Web

The Java version in the Maven project was changed from 25 to 21 because the local environment uses Java 21.

The initial Maven build failed with:

```text
Fatal error compiling: error: release version 25 not supported
```

After changing the configured Java version to 21, the application builds and runs successfully.

## Current API

The backend currently exposes:

```http
GET /api/races
```

The endpoint currently returns two in-memory `Race` objects.

The root URL `/` currently has no controller mapping, so Spring Boot returns its default Whitelabel 404 response.

## Race Model

The current `Race` model contains:

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

There are currently no JPA (Jakarta Persistence API) annotations.

## API Requirements

The MVP (Minimum Viable Product) API should support:

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

The intended semantics are:

```text
(
    name contains search
    OR location contains search
    OR currency contains search
    OR description contains search
    OR websiteUrl contains search
)
AND
distance >= distanceFrom
AND
distance <= distanceTo
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

Angular frontend has not been created yet.

Planned frontend technology:

* Angular
* Angular Material
* SCSS (Sassy Cascading Style Sheets)
* BEM (Block Element Modifier)
* Signal Store

## Database

PostgreSQL is planned but has not been introduced yet.

## AWS

An AWS account exists.

No AWS infrastructure has been created yet.

## Infrastructure

No Infrastructure as Code has been created yet.

Planned technology:

* AWS CDK (Cloud Development Kit)
* Docker

## CI/CD

CI/CD (Continuous Integration / Continuous Delivery) has not been implemented yet.

Planned technology:

* GitHub Actions

## What I Understand

* The project will be built incrementally.
* Spring Boot controllers can expose HTTP endpoints.
* `@RestController` is used for REST (Representational State Transfer) controllers.
* `@GetMapping` maps a GET request to a controller method.
* The application currently exposes `GET /api/races`.
* Search should be separate from numerical distance filtering.
* Distance filtering should use `distanceFrom` and `distanceTo`.
* The architecture will evolve from a simple application toward a layered backend.
* AWS architecture will be introduced after understanding the local application.
* Architecture should reflect professional patterns.
* Infrastructure costs should be minimized.

## What I Don't Understand Yet

* Exact responsibilities of Controller, Service and Repository layers.
* How the Service layer should be introduced without unnecessary abstraction.
* How Repository interacts with PostgreSQL.
* How Spring Data handles database queries.
* How API filtering and pagination will be implemented with PostgreSQL.
* How the local architecture will evolve toward the AWS architecture.

## Open Questions

* What should belong in the Controller and what should belong in the Service?
* When is a Repository abstraction useful?
* How should search across multiple String fields be implemented?
* How should pagination be represented in the final API contract?
* Which AWS gateway architecture should be selected later?

## Next Step

Before changing the code, learn and understand the responsibilities of:

```text
Controller → Service → Repository
```

Then introduce the Service layer into the Trail Races backend.