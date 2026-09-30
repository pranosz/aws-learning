# Architectural Decisions

## Decision 1 — Race represents one race distance

### Decision

One database record represents one race distance.

If one event offers 25 km, 50 km and 100 km, the current model treats these as three `Race` records.

### Reason

The initial MVP needs straightforward distance filtering.

Keeping one distance per `Race` makes the initial model simple and avoids introducing a separate event/distance relationship before it is necessary.

This decision can be revisited later if the domain requires a distinction between an event and its individual race distances.

---

## Decision 2 — Text search is separate from distance filtering

### Decision

The API uses one `search` parameter for text search and separate parameters for distance:

```text
search
distanceFrom
distanceTo
```

Example:

```http
GET /api/races?search=tatry&distanceFrom=20&distanceTo=50
```

### Searchable fields

`search` searches only:

* `name`
* `location`
* `currency`
* `description`
* `websiteUrl`

### Non-searchable fields

`search` does not search:

* `distance`
* `elevation`
* `price`
* `itra`
* `date`

### Reason

Text search and numerical filtering represent different types of queries.

Keeping them separate makes the API explicit and independent of the frontend UI.

---

## Decision 3 — Distance filtering uses a range

### Decision

Distance filtering uses:

```text
distanceFrom
distanceTo
```

The logic is:

```text
distance >= distanceFrom
AND
distance <= distanceTo
```

### Reason

The API exposes a numerical range rather than a UI-specific control. This keeps the API independent of how the frontend presents the filter.

---

## Decision 4 — Search and filters can be combined

### Decision

Text search and distance filtering can be used in the same request.

Example:

```http
GET /api/races?search=tatry&distanceFrom=20&distanceTo=50
```

The logic is:

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

### Reason

The API should allow users to narrow the result set using multiple independent criteria.

---

## Decision 5 — Pagination is part of the MVP API

### Decision

The API uses Spring Data pagination through:

```text
page
size
```

Example:

```http
GET /api/races?page=0&size=10
```

The response is a Spring Data `Page<Race>` and contains the page content together with pagination metadata.

### Reason

A race database can grow significantly.

Returning every race in one response does not scale well and makes the API inefficient for the frontend.

Pagination limits the amount of data returned in a single request.

The pagination is performed by the backend/database rather than by loading the complete dataset into Angular.

---

## Decision 6 — Server-side sorting

### Decision

Sorting is performed by the backend using Spring Data `Pageable`.

The API accepts Spring Data's `sort` parameter.

Example:

```http
GET /api/races?page=0&size=10&sort=distance,desc
```

The frontend does not sort the complete result set locally.

### Reason

Pagination and sorting should operate on the complete dataset, not only on the records currently loaded into the browser.

Performing sorting on the backend allows PostgreSQL to order the dataset before pagination is applied.

This also keeps the frontend simpler and prepares the application for larger datasets.

The frontend exposes only an explicit set of supported sort fields rather than accepting arbitrary values from the user.

---

## Decision 7 — Use BigDecimal for price

### Decision

Race price uses Java `BigDecimal`.

### Reason

Money should not be represented using floating-point types because floating-point arithmetic can introduce precision problems.

`BigDecimal` provides decimal arithmetic suitable for monetary values.

---

## Decision 8 — Use Double for distance

### Decision

Race distance currently uses Java `Double`.

### Reason

Distance is a measured numerical value and does not have the same monetary precision requirements as price.

The decision can be revisited if the domain later requires stricter precision rules.

---

## Decision 9 — Layered backend architecture

### Decision

The backend uses:

```text
Controller
   ↓
Service
   ↓
Repository
   ↓
PostgreSQL
```

### Reason

The layers provide separate responsibilities:

* Controller — HTTP/API concerns
* Service — application/business logic
* Repository — persistence/data access

The project deliberately avoids adding abstractions that do not solve a real problem.

---

## Decision 10 — PostgreSQL is the local persistence mechanism

### Decision

PostgreSQL is the persistence layer for the backend instead of an in-memory repository.

### Reason

The project is intended to teach real application persistence and the path toward a managed database on AWS.

Using PostgreSQL locally makes it possible to learn database schema design, SQL, JPA, transactions, indexing and later RDS without changing the fundamental application model.

The previous `InMemoryRaceRepository` was removed once persistence was introduced.

---

## Decision 11 — Use Spring Data JPA for repository persistence

### Decision

`RaceRepository` extends:

```java
JpaRepository<Race, Long>
```

and:

```java
JpaSpecificationExecutor<Race>
```

### Reason

Spring Data JPA provides standard persistence operations without requiring boilerplate repository implementations.

`JpaSpecificationExecutor` allows dynamic search and distance filtering to be composed without creating a separate repository method for every combination of optional filters.

---

## Decision 12 — Use Flyway for database schema migrations

### Decision

Database schema changes are managed through versioned Flyway migrations.

### Reason

Database schema should be reproducible, versioned and reviewable through source control.

Manual changes in pgAdmin are not a substitute for migration scripts in the project.

Flyway provides a controlled path from local development toward automated deployment environments.

---

## Decision 13 — Do not hardcode database passwords

### Decision

The PostgreSQL password is supplied through an environment variable:

```properties
spring.datasource.password=${DB_PASSWORD}
```

The actual password is not stored in `application.properties` or source control.

### Reason

Credentials are secrets and should not be committed to the repository.

This also provides a natural path toward proper secret management when the application moves to AWS.

---

## Decision 14 — Security and engineering quality are project requirements

### Decision

The application will not be developed using a "make it work at any cost" approach.

Implementation choices should follow good engineering practices appropriate for a real application, with security considered from the beginning.

### Principles

* do not hardcode secrets
* avoid unnecessary exposure of services and network resources
* use least-privilege access where applicable
* validate and handle external input deliberately
* use secure secret management for cloud deployment
* prefer maintainable and testable solutions
* document important security and architecture decisions
* do not introduce enterprise complexity without a reason

### Reason

The purpose of the project is not only to make a working application. It is to learn how a professional application can be designed, implemented and operated safely.

---

## Decision 15 — Follow the current Angular Style Guide for frontend structure

### Decision

The Angular frontend follows the current Angular Style Guide as the baseline for code organization and implementation decisions.

The frontend is organized by feature area rather than by generic technical folders. Related files for a component remain together.

The project uses:

* standalone components
* the current Angular file naming convention
* strict TypeScript configuration
* `inject()` for dependency injection
* lazy-loaded route components where appropriate

### Reason

The project is intended to learn current Angular practices rather than preserve structures from older Angular versions.

Feature-based organization keeps related code together and avoids creating architectural categories before they are needed.

---

## Decision 16 — Use Angular Material and SCSS/BEM for the initial frontend UI

### Decision

The frontend uses Angular Material for common UI components and SCSS with BEM naming for project-specific styling.

Tailwind CSS is not part of the initial frontend stack.

### Reason

Angular Material provides Angular-native UI components. SCSS and BEM provide a predictable approach for project-specific styling while keeping the initial frontend simple.

---

## Decision 17 — Do not introduce Signal Store before a real state requirement exists

### Decision

Signal Store is planned for the frontend, but it will not be introduced during the current application foundation stage.

### Reason

The current race-list feature can be implemented with Angular signals and local component state.

Adding centralized state management before a real requirement exists would add complexity without a demonstrated benefit.

---

## Decision 18 — Stop frontend feature development at the current MVP boundary

### Decision

The frontend is considered sufficiently complete for the current learning objective.

The current frontend supports:

* race list
* text search
* distance filtering
* pagination
* sorting
* loading state
* error state

Further frontend features are postponed unless they become necessary for infrastructure, deployment or another learning objective.

### Reason

The main objective of the project is now AWS, networking, infrastructure, Docker and CI/CD.

Continuing to add frontend features would provide diminishing learning value compared with moving toward deployment and operations.

---

## Decision 19 — Move to Docker before AWS deployment

### Decision

The next implementation stage is Docker and containerization.

The project will first make the local application reproducible using containers before introducing the AWS runtime architecture.

### Reason

Docker provides a clear bridge between application development and cloud infrastructure.

The same application artifacts can then be used as the basis for AWS container deployment.

The Docker stage will also provide a practical context for learning container networking, environment configuration and service-to-service communication.
