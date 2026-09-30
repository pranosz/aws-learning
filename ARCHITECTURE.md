# Architecture

## Current Local Architecture

The application currently works locally as:

```text
Angular
   │
   │ HTTP
   ▼
Spring Boot
   │
   │ Spring Data JPA / Hibernate
   ▼
PostgreSQL
```

The backend uses a layered architecture:

```text
RaceController
      ↓
RaceService
      ↓
RaceRepository
      ↓
PostgreSQL
```

Database schema changes are managed by Flyway.

The current API endpoint is:

```http
GET /api/races
```

The endpoint supports text search, distance filtering, pagination and sorting.

## Current Frontend Architecture

The Angular frontend is organized by feature area rather than by generic technical folders.

Current structure:

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

The `/races` route is lazy-loaded.

The frontend uses:

* standalone Angular components
* Angular Material
* SCSS with BEM
* Angular signals for local component state
* Reactive Forms for search/filter input

Signal Store is not currently required because the existing feature does not have a demonstrated need for centralized application state.

## Frontend → Backend Boundary

The application flow is:

```text
Angular RaceList
      ↓
RaceApi
      ↓
HTTP request
      ↓
Spring Boot RaceController
      ↓
RaceService
      ↓
RaceRepository
      ↓
PostgreSQL
```

The frontend communicates with the backend through HTTP and does not contain persistence concerns.

The development frontend uses a proxy so that `/api/**` requests are forwarded to the local Spring Boot server.

The backend remains independent of the Angular implementation.

## Backend Responsibilities

### Controller

`RaceController` owns the HTTP/API boundary.

Current responsibilities:

* expose `GET /api/races`
* receive search/filter/pagination/sort parameters
* trigger validation
* call `RaceService`
* return the HTTP response

The Controller should not contain database access or the main business logic.

### Service

`RaceService` represents the application/service layer.

Current responsibilities:

* coordinate race retrieval
* combine optional filtering criteria
* pass `Pageable` to the Repository

Business rules that belong to the application/service layer should be added here when they appear.

### Repository

`RaceRepository` uses Spring Data JPA:

```java
JpaRepository<Race, Long>
JpaSpecificationExecutor<Race>
```

Current responsibilities:

* provide persistence operations
* execute dynamic specifications
* apply pagination and sorting through `Pageable`

## Database Architecture

The application uses PostgreSQL locally.

The schema is managed by Flyway rather than being created manually or relying on Hibernate to change the schema automatically.

Flyway maintains:

```text
flyway_schema_history
```

The application uses PostgreSQL through Spring Data JPA and Hibernate.

## API Query Flow

A request such as:

```http
GET /api/races?search=tatry&distanceFrom=20&distanceTo=80&page=0&size=10&sort=distance,desc
```

flows through:

```text
HTTP request
      ↓
RaceController
      ↓
RaceSearchCriteria validation
      ↓
RaceService
      ↓
RaceSpecifications
      ↓
Pageable
      ↓
RaceRepository
      ↓
Spring Data JPA / Hibernate
      ↓
PostgreSQL
```

Search and distance filtering are performed by the backend.

Pagination and sorting are also performed by the backend/database rather than by Angular.

## Security Principles

Security is a project requirement from the beginning.

In particular:

* secrets must not be hardcoded in source code
* database passwords are supplied through environment variables
* sensitive configuration should be separated from application source code
* database access should use minimum required permissions
* network access should be restricted rather than exposed unnecessarily
* authentication and authorization will be introduced deliberately when required
* production infrastructure should use managed secret storage where appropriate
* security decisions should be documented together with architectural trade-offs

The local configuration uses:

```properties
spring.datasource.password=${DB_PASSWORD}
```

The actual database password is not stored in `application.properties`.

## Target Cloud Architecture

The final AWS architecture has not yet been selected.

The project is expected to evolve toward something conceptually similar to:

```text
                         Internet
                            │
                            ▼
                    DNS / HTTPS
                            │
                            ▼
                    Frontend delivery
                    S3 / CloudFront
                            │
                            │ API requests
                            ▼
                  Gateway / Load Balancer
                            │
                            ▼
                    Backend containers
                            │
                      private network
                            │
                            ▼
                     PostgreSQL
                        RDS
```

This diagram is conceptual, not the final infrastructure design.

The exact architecture will be selected after learning and evaluating:

* VPC
* public and private subnets
* route tables
* Internet Gateway
* NAT
* Security Groups
* IAM
* ALB (Application Load Balancer)
* API Gateway
* ECS / Fargate
* ECR
* RDS
* S3
* CloudFront
* Route 53
* ACM
* CloudWatch
* secrets management

The final design must balance:

* security
* availability
* scalability
* cost
* operational complexity
* learning value

## Development Direction

The application layer is now sufficiently complete for the current learning goal.

The next architecture step is to containerize the local application:

```text
Angular
   ↓
Spring Boot container
   ↓
PostgreSQL container
```

This will provide the bridge from application development to infrastructure and AWS.

After Docker, the project will move to AWS networking and Infrastructure as Code.

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
