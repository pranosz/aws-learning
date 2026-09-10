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

The exact implementation will be decided when PostgreSQL and the Repository layer are introduced.

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

The backend will evolve toward:

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

The goal is to understand this professional architectural pattern while avoiding unnecessary abstractions inside each layer.

The Service and Repository layers have not yet been implemented.