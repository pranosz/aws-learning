# Trail Races — Learning Project

Educational project for learning software architecture, Java, Spring Boot, Angular, AWS, infrastructure, networking, security and CI/CD.

## Project

Trail Races is a web application for discovering and browsing mountain running races.

The application is developed incrementally, starting from a simple local application and evolving toward a professional cloud architecture.

The application is primarily a **learning vehicle for AWS, networking, infrastructure, Docker and CI/CD**. The frontend and backend provide a realistic application to deploy, secure, monitor and automate.

## Technology

### Frontend

* Angular
* Angular Material
* SCSS (Sassy Cascading Style Sheets)
* BEM (Block Element Modifier)
* Signal Store — planned, not currently required

Current frontend baseline:

* Angular 22.2.0
* Angular Material 22.2.0
* TypeScript 6.0.3
* RxJS 7.8.2
* Vitest 5.0.2
* Node.js 22.22.3

The frontend follows the current Angular Style Guide as the baseline for code organization and implementation decisions.

The frontend currently provides:

* race list
* text search
* distance range filtering
* server-side pagination
* server-side sorting
* loading and error states

The frontend work is considered sufficient for the current learning goal. Further UI features are not a priority unless they become useful for infrastructure or deployment work.

### Backend

* Java 21
* Spring Boot 4.1.1
* Maven
* Spring Data JPA
* Hibernate

### Database

* PostgreSQL
* Flyway

### Infrastructure

* AWS (Amazon Web Services)
* AWS CDK (Cloud Development Kit)
* Docker

### CI/CD

* GitHub Actions
* CI (Continuous Integration)
* CD (Continuous Delivery / Continuous Deployment)

## Main Learning Goals

The main goal is to understand how a real application is built, deployed and operated, with particular emphasis on:

* AWS
* networking
* infrastructure
* Infrastructure as Code
* Docker and containerization
* CI/CD
* security
* deployment
* scalability
* availability
* observability
* architectural decision-making
* database persistence and migrations

The application itself is not the end goal. It is the practical environment in which these concepts are learned.

The target progression is:

```text
Working local application
        ↓
Docker
        ↓
Networking fundamentals
        ↓
AWS infrastructure
        ↓
Infrastructure as Code
        ↓
Cloud deployment
        ↓
CI/CD
        ↓
Security and observability
```

## Engineering and Security Principles

This project is **not a "make it work at any cost" project**.

The goal is to learn how a real application is designed, implemented and operated in a professional engineering environment.

The implementation should therefore:

* follow good engineering practices
* consider security from the beginning
* never hardcode secrets
* use appropriate access control and least privilege
* avoid unnecessary network exposure
* validate and handle external input deliberately
* prefer maintainable and testable solutions
* document important architecture and security decisions
* evaluate alternatives and trade-offs
* avoid unnecessary enterprise complexity
* keep AWS costs under control

Security is not something that will be added only at the end of the course. Security considerations should influence decisions throughout the project.

The project should also avoid introducing an expensive or complex AWS service merely because it is common in large organizations. Each component should have a clear engineering or learning reason.

## Learning Approach

The project is the practical learning environment.

Development should proceed in logical, testable steps rather than as isolated theoretical exercises.

A typical learning cycle is:

```text
Understand the reason
        ↓
Make one logical change
        ↓
Run the application
        ↓
Verify the result
        ↓
Understand what happened
        ↓
Document durable knowledge
        ↓
Continue
```

Programming should follow:

* KISS (Keep It Simple, Stupid)
* DRY (Don't Repeat Yourself)
* avoid unnecessary abstractions
* introduce design patterns only when they solve a real problem

For infrastructure, the same principle applies: understand why a component exists before introducing it.

## Current Local Architecture

The current application works locally as:

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

The backend follows:

```text
RaceController
      ↓
RaceService
      ↓
RaceRepository
      ↓
PostgreSQL
```

Database schema is managed by Flyway.

The main API endpoint is:

```http
GET /api/races
```

The API currently supports:

* text search
* distance range filtering
* pagination
* sorting

Pagination and sorting are performed on the backend using Spring Data `Pageable`, so the database performs the ordering and pagination rather than the Angular application.

## Current Frontend State

The Angular frontend runs locally at:

```text
http://localhost:4200
```

The backend runs locally at:

```text
http://localhost:8080
```

Angular uses a development proxy so that frontend requests to:

```text
/api/races
```

are forwarded to the Spring Boot backend.

The current frontend feature is intentionally kept simple. It is sufficient to act as a realistic client for the backend while the project moves toward AWS and infrastructure topics.

## Current Backend State

The local backend follows:

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

Database schema is managed by Flyway migrations.

The database password is supplied through the `DB_PASSWORD` environment variable and is not stored in `application.properties`.

## API

The current API is:

```http
GET /api/races
```

Examples:

```http
GET /api/races
```

```http
GET /api/races?search=tatry&distanceFrom=20&distanceTo=80
```

```http
GET /api/races?page=0&size=10&sort=distance,desc
```

The `search` parameter searches:

* `name`
* `location`
* `currency`
* `description`
* `websiteUrl`

Distance filtering uses:

* `distanceFrom`
* `distanceTo`

Pagination uses:

* `page`
* `size`

Sorting uses Spring Data's `sort` parameter, for example:

```text
sort=distance,desc
```

The detailed API and implementation knowledge is documented in `KNOWLEDGE.md` and `DECISIONS.md`.

## AWS Direction

No final production AWS architecture has been selected yet.

The project is expected to evolve toward an architecture containing concepts such as:

```text
User
  ↓
DNS / HTTPS
  ↓
CloudFront / frontend hosting
  ↓
Gateway / Load Balancer
  ↓
Backend containers
  ↓
Private network
  ↓
Managed PostgreSQL
```

The exact AWS services and network topology will be selected deliberately after learning the underlying concepts.

The project will explicitly evaluate:

* VPC and subnets
* route tables
* Internet Gateway
* NAT
* Security Groups
* IAM
* Load Balancer / API Gateway
* ECS / containers
* ECR
* RDS
* S3
* CloudFront
* Route 53
* ACM
* CloudWatch
* secrets management

## CI/CD Direction

CI/CD has not yet been implemented.

The planned flow is:

```text
Git push
   ↓
GitHub Actions
   ↓
validation / tests
   ↓
build
   ↓
Docker image
   ↓
container registry
   ↓
deployment to AWS
```

The exact deployment strategy will be selected after the AWS infrastructure is understood and implemented.

## Documentation Workflow

The Markdown files in this repository are the durable source of project and learning context.

Do not invent, rename, or reinterpret existing file names or their purposes. Before updating documentation, inspect the current repository and preserve the existing file names, structure and roles.

When a learning session ends or a repository summary is requested:

1. Inspect the current repository first.
2. Identify only the Markdown files that actually need changes.
3. For every changed file, provide its complete current content.
4. Provide each changed file separately in one standalone Markdown code block.
5. The complete file content must be ready to copy using the Copy button and replace the entire existing file.
6. Never provide partial snippets, abbreviated fragments, examples presented as complete files, or only the changed section.
7. Do not provide unchanged files.
8. Do not provide unrelated files.
9. Do not put explanations, comments about changes, or other prose inside the file contents.
10. Do not create new files unless explicitly requested or genuinely required by the project.

The expected workflow is:

```text
Inspect repository
      ↓
Determine which Markdown files changed
      ↓
Show only changed files
      ↓
Each file = complete replacement content
      ↓
Copy
      ↓
Replace entire file
      ↓
Save
```

## Running Locally

### Backend

Requirements:

* Java 21
* PostgreSQL
* `DB_PASSWORD` configured

Windows:

```bash
cd C:\DyskD\aws-learning-repos\trail-races-backend
.\mvnw.cmd spring-boot:run
```

The backend starts on:

```text
http://localhost:8080
```

### Frontend

Windows:

```bash
cd C:\DyskD\aws-learning-repos\trail-races-frontend
npm start
```

The frontend starts on:

```text
http://localhost:4200
```

Open:

```text
http://localhost:4200/races
```

## Current Next Step

The frontend baseline is complete for the current learning goal.

The next implementation stage is **Docker and containerization**.

The first target is to package the backend and PostgreSQL development environment in a reproducible way and understand:

* images
* containers
* Dockerfile
* container networking
* environment configuration
* Docker Compose

After Docker, the project will move into AWS networking and infrastructure.
