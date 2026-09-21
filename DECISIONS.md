# Architectural Decisions

## Decision 1 — Race represents one race distance

### Decision

One database record represents one race distance.

If one event offers 25 km, 50 km and 100 km, the current model treats these as three `Race` records.

### Reason

The initial MVP (Minimum Viable Product) needs straightforward distance filtering.

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

A user searching for `tatry` expects text matching.

A user looking for races between 20 and 50 km expects a numerical range filter.

Keeping them separate makes the API explicit and easier for the frontend to use.

---

## Decision 3 — Distance filtering uses a range

### Decision

Distance filtering uses:

```text
distanceFrom
distanceTo
```

The intended logic is:

```text
distance >= distanceFrom
AND
distance <= distanceTo
```

Example:

```http
GET /api/races?distanceFrom=20&distanceTo=50
```

### Reason

The frontend may later present the filter as a slider, but the API should expose a clear numerical range rather than a UI-specific concept.

This keeps the API independent of the frontend implementation.

---

## Decision 4 — Search and filters can be combined

### Decision

Text search and distance filtering can be used in the same request.

Example:

```http
GET /api/races?search=tatry&distanceFrom=20&distanceTo=50
```

The intended logic is:

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

The API will support pagination using:

```text
page
size
```

Example:

```http
GET /api/races?search=tatry&distanceFrom=20&distanceTo=50&page=1&size=20
```

The planned response structure is:

```json
{
  "content": [],
  "page": 1,
  "size": 20,
  "totalElements": 47,
  "totalPages": 3
}
```

### Reason

A race database can grow significantly.

Returning every race in one response does not scale well and makes the API inefficient for the frontend.

Pagination limits the amount of data returned in a single request.

The exact implementation will be decided when the querying requirements are introduced.

---

## Decision 6 — Use BigDecimal for price

### Decision

Race price uses Java `BigDecimal`.

### Reason

Money should not be represented using floating-point types because floating-point arithmetic can introduce precision problems.

`BigDecimal` provides decimal arithmetic suitable for monetary values.

---

## Decision 7 — Use Double for distance

### Decision

Race distance currently uses Java `Double`.

### Reason

Distance is a measured numerical value and does not have the same monetary precision requirements as price.

The decision can be revisited if the domain later requires stricter precision rules.

---

## Decision 8 — Layered backend architecture

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

## Decision 9 — PostgreSQL is the local persistence mechanism

### Decision

PostgreSQL is the persistence layer for the backend instead of an in-memory repository.

### Reason

The project is intended to teach real application persistence and the path toward a managed database on AWS.

Using PostgreSQL locally makes it possible to learn database schema design, SQL, JPA, transactions, indexing and later RDS without changing the fundamental application model.

The previous `InMemoryRaceRepository` was useful during the initial learning stage but was removed once persistence was introduced.

---

## Decision 10 — Use Spring Data JPA for repository persistence

### Decision

`RaceRepository` extends:

```java
JpaRepository<Race, Long>
```

### Reason

Spring Data JPA provides standard persistence operations without requiring boilerplate repository implementations.

This is appropriate for the current application and keeps the Repository layer simple.

More complex query requirements will be evaluated when search, filtering and pagination are implemented.

---

## Decision 11 — Use Flyway for database schema migrations

### Decision

Database schema changes are managed through versioned Flyway migrations.

Current migrations:

```text
V1__create_races_table.sql
V2__insert_initial_races.sql
```

### Reason

Database schema should be reproducible, versioned and reviewable through source control.

Manual changes in pgAdmin are not a substitute for migration scripts in the project.

Flyway allows the same schema changes to be applied consistently in development and later in deployment environments.

---

## Decision 12 — Do not hardcode database passwords

### Decision

The PostgreSQL password is supplied through an environment variable:

```properties
spring.datasource.password=${DB_PASSWORD}
```

The actual password is not stored in `application.properties` or source control.

### Reason

Credentials are secrets and should not be committed to the repository.

This approach also provides a path toward proper secret management when the application moves to AWS.

The exact AWS secret-management mechanism will be decided when cloud deployment is introduced.

---

## Decision 13 — Security and engineering quality are project requirements

### Decision

The application will not be developed using a "make it work at any cost" approach.

Implementation choices should follow good engineering practices appropriate for a real application, with security considered from the beginning.

### Principles

* do not hardcode secrets
* avoid unnecessary exposure of services and network resources
* use least-privilege access where applicable
* validate and handle external input deliberately
* consider authentication and authorization before exposing protected functionality
* use secure secret management for cloud deployment
* prefer maintainable and testable solutions
* document important security and architecture decisions
* do not introduce enterprise complexity without a reason

### Reason

The purpose of the project is not only to make a working application. It is to learn how a professional application can be designed, implemented and operated safely.

---
