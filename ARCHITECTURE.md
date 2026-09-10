# Architecture

## Current Local Architecture

The application is currently a simple Spring Boot backend.

```text
Client
   ↓
Spring Boot
   ↓
RaceController
   ↓
In-memory Race objects
```

The current implementation does not use a database.

## Planned Local Backend Architecture

The backend will evolve toward a layered structure:

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

### Controller

The Controller is responsible for handling HTTP (Hypertext Transfer Protocol) requests and responses.

It should deal with API concerns such as:

* request parameters
* request validation
* HTTP responses
* mapping requests to application operations

The Controller should not contain the main business logic.

### Service

The Service represents the application/business logic.

It should coordinate operations such as:

* retrieving races
* applying business rules
* combining filtering criteria
* coordinating repositories or other services

The Service should not be responsible for HTTP-specific details.

### Repository

The Repository is responsible for access to persistent data.

Later it will communicate with PostgreSQL.

The Repository should hide database-access details from the Service.

## API Flow

For a request such as:

```http
GET /api/races?search=tatry&distanceFrom=20&distanceTo=50&page=1&size=20
```

the intended flow is:

```text
HTTP request
     ↓
RaceController
     ↓
RaceService
     ↓
RaceRepository
     ↓
PostgreSQL
     ↓
RaceRepository
     ↓
RaceService
     ↓
RaceController
     ↓
HTTP response
```

## Target Cloud Architecture

The final architecture is expected to evolve toward a professional AWS (Amazon Web Services) architecture.

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
* scalability
* security
* operational complexity
* cost
* what concept the architecture is intended to teach

## Architectural Principles

### Programming

Use:

* KISS (Keep It Simple, Stupid)
* DRY (Don't Repeat Yourself)

Avoid abstractions that do not solve a real problem.

### Architecture

Architecture may be more sophisticated than strictly necessary for this application when the additional complexity teaches an important professional concept.

Every architectural component should have a clear reason for existing.

Alternatives and trade-offs should be considered before selecting a solution.

AWS infrastructure costs should be kept as low as reasonably possible.