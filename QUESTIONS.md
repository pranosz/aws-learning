# Questions

## Current Questions

### Backend architecture

* When does introducing additional layers become unnecessary abstraction?
* Which responsibilities should remain in the Service as the application grows?
* When should a Repository contain derived query methods and when should a custom query be introduced?

### API

* How should the `search` parameter be implemented in PostgreSQL?
* How should `distanceFrom` and `distanceTo` be translated into a database query?
* How should optional filters be handled?
* How should pagination be implemented?
* Should `page` start at 0 or 1 in the API?
* What should the final pagination response contract look like?
* How should validation errors be returned by the API?
* How should API errors be represented consistently?

### Database

* How should transactions be used in the application?
* What indexes will be useful for race search and filtering?
* How should JPA mappings be evolved as the domain grows?
* When should a database constraint be represented in the schema versus enforced in application code?

### Security

* How should authentication and authorization be introduced?
* What is the appropriate least-privilege model for the application and database?
* How should secrets be managed securely in AWS?
* How should database and backend network access be restricted?
* Which security checks should be part of CI/CD?

### Architecture

* How should the local architecture evolve toward the AWS architecture?
* Where should a gateway be placed?
* What is the difference between API Gateway and ALB (Application Load Balancer)?
* Which components are genuinely needed for this application and which would be unnecessary complexity?

### AWS

* How should the application be deployed to AWS?
* Which AWS services are actually needed?
* Which services introduce costs?
* What is the cheapest architecture that still teaches the intended AWS concepts without compromising security?
* How should PostgreSQL move from local development to RDS securely?
