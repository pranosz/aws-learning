# Learning Roadmap

## Phase 1 — Application foundations

* [x] Define the domain
* [x] Create Spring Boot backend
* [x] Create initial REST API
* [x] Introduce Controller → Service → Repository structure
* [x] Introduce PostgreSQL
* [x] Introduce JPA / Spring Data JPA
* [x] Introduce Flyway database migrations
* [x] Add initial database data through a migration
* [x] Verify API reads data from PostgreSQL
* [x] Create Angular frontend
* [x] Connect Angular with backend
* [x] Implement text search
* [x] Implement distance range filtering
* [x] Implement server-side pagination
* [x] Implement server-side sorting
* [x] Add loading and error states
* [ ] Review and expand automated tests

Basic CRUD is intentionally not part of the current learning scope. The application is currently read-oriented because the main objective is AWS, networking, infrastructure and CI/CD.

### Frontend baseline

The frontend baseline is complete for the current learning goal:

* Angular 22.2.0
* Angular Material 22.2.0
* SCSS (Sassy Cascading Style Sheets)
* BEM (Block Element Modifier)
* standalone Angular components
* strict TypeScript configuration
* Vitest for unit tests
* feature-based code organization following the current Angular Style Guide
* server-side pagination and sorting handled by the backend

Further frontend features are intentionally postponed.

## Phase 2 — Architecture and networking fundamentals

* [ ] HTTP (Hypertext Transfer Protocol)
* [ ] HTTPS (Hypertext Transfer Protocol Secure)
* [ ] DNS (Domain Name System)
* [ ] IP (Internet Protocol)
* [ ] TCP (Transmission Control Protocol)
* [ ] ports
* [ ] client/server communication
* [ ] reverse proxy
* [ ] load balancing
* [ ] stateless applications
* [ ] scalability
* [ ] availability

## Phase 3 — Docker

* [ ] Docker
* [ ] images
* [ ] containers
* [ ] Dockerfile
* [ ] container networking
* [ ] environment variables
* [ ] Docker Compose
* [ ] reproducible local startup

## Phase 4 — AWS foundations

* [ ] AWS (Amazon Web Services)
* [ ] Region
* [ ] Availability Zone
* [ ] IAM (Identity and Access Management)
* [ ] VPC (Virtual Private Cloud)
* [ ] subnets
* [ ] route tables
* [ ] Internet Gateway
* [ ] NAT Gateway
* [ ] Security Groups
* [ ] S3 (Simple Storage Service)
* [ ] CloudWatch

## Phase 5 — Infrastructure as Code

* [ ] AWS CDK (Cloud Development Kit)
* [ ] CloudFormation
* [ ] infrastructure repository
* [ ] infrastructure deployment
* [ ] infrastructure changes through Git
* [ ] environment separation

## Phase 6 — Backend on AWS

* [ ] ECR (Elastic Container Registry)
* [ ] ECS (Elastic Container Service)
* [ ] Fargate
* [ ] task definitions
* [ ] ECS services
* [ ] health checks
* [ ] Load Balancer
* [ ] autoscaling

## Phase 7 — Gateway and networking

* [ ] Understand the gateway concept
* [ ] Compare API Gateway and ALB (Application Load Balancer)
* [ ] Evaluate API Gateway + ALB
* [ ] Select the appropriate architecture
* [ ] Document the decision
* [ ] private versus public resources
* [ ] inbound and outbound traffic flows

## Phase 8 — Database on AWS

* [ ] RDS (Relational Database Service)
* [ ] PostgreSQL on RDS
* [ ] private database
* [ ] subnet groups
* [ ] Security Groups
* [ ] secrets
* [ ] backups
* [ ] recovery

## Phase 9 — Frontend on AWS

* [ ] Angular production build
* [ ] S3
* [ ] CloudFront
* [ ] Route 53
* [ ] ACM (AWS Certificate Manager)
* [ ] HTTPS
* [ ] caching
* [ ] deployment

## Phase 10 — CI/CD

* [ ] GitHub Actions
* [ ] CI (Continuous Integration)
* [ ] automated tests
* [ ] build
* [ ] Docker image build
* [ ] ECR push
* [ ] ECS deployment
* [ ] CD (Continuous Delivery / Continuous Deployment)
* [ ] deployment environments
* [ ] rollback strategy

## Phase 11 — Observability and production

* [ ] logs
* [ ] metrics
* [ ] alarms
* [ ] health checks
* [ ] monitoring
* [ ] error tracking
* [ ] observability
* [ ] cost monitoring

## Phase 12 — Advanced architecture

* [ ] monolith vs microservices
* [ ] event-driven architecture
* [ ] queues
* [ ] SQS (Simple Queue Service)
* [ ] SNS (Simple Notification Service)
* [ ] caching
* [ ] Redis
* [ ] asynchronous processing
* [ ] distributed systems
* [ ] eventual consistency
* [ ] Kubernetes
* [ ] blue/green deployment
* [ ] canary deployment
* [ ] resilience
* [ ] fault tolerance

## Guiding Principles

### Programming

For Angular and Java code:

* KISS (Keep It Simple, Stupid)
* DRY (Don't Repeat Yourself)
* avoid unnecessary abstractions
* use design patterns only when they solve a real problem

### Architecture and Engineering Quality

The project is intentionally not developed using a "make it work at any cost" approach.

The goal is to learn how real applications are designed, deployed and operated in professional engineering environments.

Therefore:

* security is considered from the beginning
* secrets are not hardcoded
* access should follow least-privilege principles where applicable
* unnecessary network exposure should be avoided
* architectural decisions should have a clear reason
* alternatives and trade-offs should be considered
* maintainability and testability matter
* enterprise complexity should not be introduced without a real reason
* every infrastructure component should have a clear learning or engineering purpose

### Cost

For AWS and infrastructure:

* explicitly consider cost
* compare cheaper alternatives
* understand the trade-offs
* prefer the cheapest solution that still provides sound security and teaches the intended concept

The architecture may therefore be more sophisticated than strictly necessary for the application itself, but every additional component should have a clear reason.
