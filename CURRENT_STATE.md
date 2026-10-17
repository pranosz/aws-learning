# Current State

## Project

Trail Races — educational mountain running race platform.

The project is being used as a practical learning environment for Java, Spring Boot, PostgreSQL, AWS, networking, infrastructure, security, Docker and CI/CD.

The application itself is not the main learning goal. It provides a realistic system that can be containerized, deployed, secured, monitored and automated.

## Current Phase

Phase 3 — Docker and containerization is implemented at the initial local-development level.

The application runs end-to-end using Docker Compose with three services:

- Angular frontend served by Nginx
- Spring Boot backend
- PostgreSQL database

The next step is to review and verify the Docker implementation, document any remaining improvements, and then continue with AWS networking and infrastructure.

## Current Status

The application works locally both through the original development setup and through Docker Compose.

Docker Compose architecture:

```text
Browser
   |
   | http://localhost:8080
   v
Frontend container
Angular static build + Nginx
   |
   | /api/** reverse proxy
   v
Backend container
Spring Boot + embedded Tomcat
   |
   | JDBC over Docker network
   v
PostgreSQL container
   |
   v
Named persistent volume
```

The frontend is exposed on the host through port mapping `8080:8080`.

The backend and PostgreSQL do not publish their application ports to the host in the current Compose configuration. They communicate through the Compose network.

The frontend is sufficiently complete for the current learning goal. Further frontend features are intentionally postponed.

## Current Local Architecture

The application layer follows:

```text
Angular
   ↓
Spring Boot REST API
   ↓
Spring Data JPA / Hibernate
   ↓
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

Database schema management:

```text
Flyway
   ↓
PostgreSQL schema
```

## Backend

- Java: 21.0.5 LTS
- Spring Boot: 4.1.1
- Build tool: Maven
- Packaging: JAR
- Group: `com.trailraces`
- Artifact: `trail-races`

Current backend capabilities:

- `GET /api/races`
- text search
- distance range filtering
- pagination
- sorting

Pagination and sorting are implemented using Spring Data `Pageable`.

The service layer combines optional search and distance specifications before passing the query and `Pageable` to the repository.

The backend is containerized using a multi-stage Docker build. Maven and JDK tooling are used in the build stage, while the runtime stage uses a Java 21 JRE image. The application runs as a non-root user.

## Database

The application uses PostgreSQL.

Current database:

```text
trail_races
```

The local PostgreSQL version observed during application startup is 15.5. The Docker environment uses the `postgres:15` image.

The database contains the `races` table and Flyway's `flyway_schema_history` table. The Docker database was populated by restoring a backup of the existing development database.

The database contains 31 race records in the verified Docker environment.

### Database persistence

The PostgreSQL container uses the named Docker volume:

```text
postgres_data
```

The volume is mounted at:

```text
/var/lib/postgresql/data
```

The volume preserves database files when the PostgreSQL container is recreated. Removing the container does not by itself remove the named volume.

Do not use `docker compose down -v` casually: the `-v` option can remove the named volume and its database data.

### Database users

The database has separate credentials for administration and application access:

- `postgres` — administrative account used for database administration.
- `trail_races_app` — application account used by the backend.

The application account has been created and granted connection access, schema usage, and table/sequence permissions needed for the current application.

The backend should not use the PostgreSQL superuser account.

The current grants cover existing tables and sequences. Future migrations and objects may require a deliberate permissions strategy. The application account is not intended to be a database administrator.

### Database configuration and secrets

The database password is not stored directly in `application.properties`.

The local backend configuration uses:

```properties
spring.datasource.password=${DB_PASSWORD}
```

For Docker Compose, datasource settings are overridden using Spring Boot environment variables:

```text
SPRING_DATASOURCE_URL
SPRING_DATASOURCE_USERNAME
SPRING_DATASOURCE_PASSWORD
```

Inside Compose, the backend connects using the PostgreSQL service name:

```text
jdbc:postgresql://postgres:5432/trail_races
```

The `postgres` hostname resolves through Docker Compose service discovery. Port `5432` is the PostgreSQL container's listening port; it is not a host port mapping.

The Compose `.env` file contains local configuration and secrets. It must not be committed to Git. `.env.example` documents the required variable names without containing real credentials.

`docker compose config --quiet` was used to validate the Compose configuration without printing resolved secrets.

The actual `.env` contents must not be copied into documentation or shared logs.

## Flyway

Flyway is used for database schema migrations.

The existing database has the following verified migration history:

```text
V1 - create races table
V2 - insert initial races
V3 - insert more races
```

Flyway maintains:

```text
flyway_schema_history
```

Manual database changes are not a replacement for versioned migrations. Existing applied migrations should not be edited retroactively.

The database backup was restored into the Docker PostgreSQL instance. The existing Flyway history was preserved as part of that restore.

## Race Entity

`Race` is a JPA entity mapped to the `races` table.

Current fields:

- `id`
- `name`
- `distance`
- `elevation`
- `location`
- `date`
- `price`
- `currency`
- `itra`
- `description`
- `websiteUrl`

Current Java types:

- `Long` for `id`
- `String` for text fields
- `Double` for `distance`
- `Integer` for `elevation`
- `LocalDate` for `date`
- `BigDecimal` for `price`

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

- coordinates race retrieval
- combines optional filtering criteria
- passes `Pageable` to the repository
- returns a paginated `Page<Race>`

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

- `name`
- `location`
- `currency`
- `description`
- `websiteUrl`

Distance filtering uses inclusive boundaries:

```text
distance >= distanceFrom
distance <= distanceTo
```

The backend validates:

- `distanceFrom >= 0`
- `distanceTo >= 0`
- `distanceFrom <= distanceTo` when both values are provided

The API returns HTTP 400 for invalid search/filter input.

## Frontend

The Angular frontend runs locally at:

```text
http://localhost:4200/
```

Current frontend versions:

- Angular: 22.2.0
- Angular CLI: 22.2.0
- Angular Material: 22.2.0
- Angular CDK (Component Development Kit): 22.2.0
- Node.js: 22.22.3
- npm: 10.9.8
- TypeScript: 6.0.3
- RxJS: 7.8.2
- Vitest: 5.0.2

Frontend decisions:

- standalone Angular components
- strict TypeScript configuration
- SCSS
- BEM
- Angular Material
- Vitest
- feature-based code organization
- lazy-loaded `/races` route
- SSR (Server-Side Rendering) and SSG (Static Site Generation) disabled for the initial application
- Signal Store is not currently required

The current race list provides:

- search
- distance filtering
- server-side pagination
- server-side sorting
- loading state
- error state
- race cards

The development frontend uses the Angular development proxy to forward `/api/**` requests to the local Spring Boot server.

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

## Docker

The infrastructure repository is:

```text
C:\DyskD\aws-learning-repos\trail-races-infrastructure
```

It contains the Docker Compose configuration, `.env`, `.env.example`, and `.gitignore`.

The frontend and backend remain in their own repositories:

```text
C:\DyskD\aws-learning-repos\trail-races-frontend
C:\DyskD\aws-learning-repos\trail-races-backend
```

### Dockerfiles

Frontend Dockerfile:

```text
trail-races-frontend/docker/Dockerfile
```

The frontend image uses a multi-stage build:

1. Build Angular with Node.js.
2. Serve the resulting static files using `nginxinc/nginx-unprivileged:alpine`.

The custom Nginx configuration is:

```text
trail-races-frontend/docker/nginx.conf
```

It listens on port `8080`, serves Angular static files, falls back to `index.html` for client-side routes, and forwards `/api/` requests to the Compose service `backend`.

The Nginx configuration preserves the `/api/` path when forwarding requests to Spring Boot.

Backend Dockerfile:

```text
trail-races-backend/docker/Dockerfile
```

The backend image uses a multi-stage Maven build and a Java 21 JRE runtime image. The application runs as a non-root user.

### Compose services

The current Compose services are:

- `frontend`
- `backend`
- `postgres`

The frontend and backend images are built from the corresponding sibling repositories using relative build-context paths.

Compose creates the containers and connects the services to:

```text
trail-races-network
```

Service discovery allows the frontend to reach `backend:8080` and the backend to reach `postgres:5432`.

The frontend port is published to the host:

```text
8080:8080
```

The backend and PostgreSQL ports are not published to the host in the current Compose configuration.

The PostgreSQL service has a health check using `pg_isready`. The backend uses `depends_on` with `condition: service_healthy` to wait for PostgreSQL readiness during startup.

This is a startup-order safeguard, not a guarantee that the database can never become unavailable later.

### Current containerized entry point

Open the frontend at:

```text
http://localhost:8080/races
```

The API can be reached through the frontend reverse proxy at:

```text
http://localhost:8080/api/races
```

The complete application has been started successfully with Docker Compose, and the frontend and API have been verified through the published frontend port.

### Docker operational commands

Validate the Compose configuration without printing resolved values:

```powershell
docker compose config --quiet
```

Build and start the environment:

```powershell
docker compose up -d --build
```

Inspect service status:

```powershell
docker compose ps
```

Inspect backend logs:

```powershell
docker compose logs --tail 100 backend
```

List local images:

```powershell
docker image ls
```

List all containers, including stopped containers:

```powershell
docker ps -a
```

Images and containers are different resources. Compose-created images may have names derived from the Compose project and service names. Older manually built images can remain on the computer even after Compose uses different image names.

Do not remove database volumes as a routine cleanup operation.

## AWS

An AWS account exists.

No final production AWS architecture has been selected yet.

Planned infrastructure includes:

- AWS networking
- VPC
- subnets
- route tables
- Internet Gateway
- NAT
- Security Groups
- IAM
- container deployment
- managed PostgreSQL
- frontend hosting
- gateway/load balancing
- CI/CD
- monitoring

AWS infrastructure will be introduced deliberately rather than created before the underlying concepts are understood.

## CI/CD

CI/CD is not implemented yet.

Planned technology:

- GitHub Actions

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

Current security-related practices:

- database passwords are supplied through environment variables
- passwords are not stored in `application.properties`
- `.env` is excluded from Git
- `.env.example` documents required variables without real secrets
- the backend uses the separate `trail_races_app` database account
- PostgreSQL and backend ports are not published to the host in Compose
- only the frontend port is published
- network access and permissions are designed deliberately
- validation is applied at the API boundary

The current local `.env` approach is for development. Before cloud deployment, secret storage and delivery must be designed for the target AWS architecture.

## Engineering Approach

The project is intentionally **not developed using a "byleby coś odpalić" / "make it work at any cost" approach**.

The target is to learn how a real engineering team would build and operate the application:

- use good practices appropriate to the problem
- keep the code simple, but not careless
- introduce abstractions only when they solve a real problem
- consider security from the beginning
- avoid hardcoded secrets
- understand infrastructure trade-offs
- prefer maintainable and testable solutions
- document important architectural decisions
- keep AWS costs under control

The learning project may use production-like patterns even when a simpler solution would be enough for the application itself, but every additional component must have a clear learning or engineering reason.

## Testing

The current frontend and backend have working test configurations.

The user has intentionally postponed the broader test-quality pass until the application foundation is complete. Tests should be reviewed and expanded at the end of the application-foundation stage rather than interrupting each infrastructure learning step.

Docker image builds and the full Docker Compose startup have been verified. A comprehensive automated test strategy for the complete containerized environment has not yet been established.

## Current Next Step

The next step is to finish reviewing the Docker stage before moving to AWS:

- review the current Docker Compose and Nginx configuration
- verify reproducible startup and the persistence of PostgreSQL data
- identify any remaining security or operational improvements
- update the documentation as the implementation is verified

After this review, continue with AWS networking and infrastructure. Do not jump directly into AWS deployment or CI/CD before the required networking and infrastructure concepts have been covered.
