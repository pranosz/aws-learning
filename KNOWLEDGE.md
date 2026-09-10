# Knowledge

## Spring Boot REST API

Spring Boot can expose HTTP (Hypertext Transfer Protocol) endpoints using controller classes.

A class annotated with `@RestController` can handle HTTP requests.

Example:

```java
@RestController
public class RaceController {

    @GetMapping("/api/races")
    public List<Race> getRaces() {
        ...
    }
}
```

`@GetMapping` maps an HTTP GET request to a Java method.

The current application exposes:

```http
GET /api/races
```

## Controller

A Controller is responsible for handling HTTP requests and returning HTTP responses.

For example:

```text
HTTP request
     ↓
Controller
     ↓
application logic
     ↓
HTTP response
```

The Controller should not contain the main business logic.

The backend will later introduce a Service layer between the Controller and Repository.

## Controller → Service → Repository

The planned backend structure is:

```text
Controller
   ↓
Service
   ↓
Repository
   ↓
PostgreSQL
```

### Controller

Responsible for the API/HTTP layer.

Typical responsibilities:

* receive HTTP requests
* read request parameters
* validate API input
* call the Service
* return the HTTP response

### Service

Responsible for application/business logic.

Typical responsibilities:

* coordinate application operations
* apply business rules
* combine filtering criteria
* call the Repository

### Repository

Responsible for data access.

Typical responsibilities:

* retrieve data
* save data
* update data
* delete data
* communicate with the database

The Service should not need to know the details of how data is stored.

## Why use layers?

Without layers, a Controller can gradually become responsible for everything:

```text
Controller
├── HTTP handling
├── business logic
├── database queries
├── validation
└── data transformation
```

This becomes difficult to understand, test and maintain.

With layers:

```text
Controller
   ↓
Service
   ↓
Repository
```

each component has a clearer responsibility.

The goal is not to create layers simply because they are a common pattern. The goal is to separate responsibilities where the separation provides a real benefit.

## Trail Races API

The application is a REST (Representational State Transfer) API for mountain running races.

The planned MVP (Minimum Viable Product) functionality is:

* list races
* text search
* distance range filtering
* pagination

## Race data

A `Race` currently contains:

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

## Search

The API will support a single `search` parameter.

Example:

```http
GET /api/races?search=tatry
```

The `search` parameter searches only String fields:

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

Conceptually:

```text
name contains search
OR location contains search
OR currency contains search
OR description contains search
OR websiteUrl contains search
```

## Distance filtering

Distance is handled separately from text search.

The API uses:

* `distanceFrom`
* `distanceTo`

Example:

```http
GET /api/races?distanceFrom=20&distanceTo=50
```

The intended logic is:

```text
distance >= distanceFrom
AND
distance <= distanceTo
```

The frontend may later display this as a slider, but the API remains based on numerical range parameters.

## Combining search and filtering

Search and distance filtering can be combined.

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

## Pagination

The API will support pagination.

Planned parameters:

* `page`
* `size`

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

The exact implementation has not been introduced yet.

## Domain Model

One database record represents one race distance.

If one event offers:

* 25 km
* 50 km
* 100 km

the current model treats these as three `Race` records.

This keeps the initial domain model simple and makes distance filtering straightforward.

This decision can be revisited later if the domain needs a separate concept of an event containing multiple race distances.

## Java Data Types

Current decisions:

* `Long` for `id`
* `Double` for distance
* `Integer` for elevation
* `LocalDate` for race date
* `BigDecimal` for price
* `String` for text fields

### BigDecimal and money

`BigDecimal` is used for monetary values because floating-point types can introduce precision problems in calculations involving decimal values.

### Double and distance

`Double` is currently used for distance because distance is a measured numerical value and does not have the same precision requirements as monetary values.

This decision can be revisited if the domain later requires stricter precision.

## Search implementation

The initial implementation can use a straightforward SQL (Structured Query Language) query containing OR conditions across the searchable String fields.

For example, conceptually:

```text
name LIKE ...
OR location LIKE ...
OR currency LIKE ...
OR description LIKE ...
OR websiteUrl LIKE ...
```

For a small MVP database, this simple approach is sufficient.

As the database grows, search performance can become a problem.

Possible future solutions include:

* database indexes
* PostgreSQL full-text search
* a dedicated search engine such as OpenSearch

The project should not introduce a dedicated search engine before there is a real reason to do so.

## Architecture principle

The application should use professional architectural patterns where they provide a learning or engineering benefit.

Programming should follow:

* KISS (Keep It Simple, Stupid)
* DRY (Don't Repeat Yourself)

Avoid unnecessary abstractions.

Architecture should explicitly consider:

* responsibility
* maintainability
* scalability
* availability
* security
* cost
* operational complexity
* alternatives and trade-offs