# Questions

## Current Questions

### Backend architecture

* What exactly should belong in the Controller?
* What exactly should belong in the Service?
* What exactly should belong in the Repository?
* Why is the Service layer useful if the Controller could call the Repository directly?
* When does introducing additional layers become unnecessary abstraction?

### API

* How should the `search` parameter be implemented in PostgreSQL?
* How should `distanceFrom` and `distanceTo` be translated into a database query?
* How should optional filters be handled?
* How should pagination be implemented?
* Should `page` start at 0 or 1 in the API?
* What should the final pagination response contract look like?
* How should validation errors be returned by the API?

### Database

* How will the `Race` Java class become a PostgreSQL entity?
* What is JPA (Jakarta Persistence API)?
* What is Spring Data JPA?
* How does a Repository communicate with PostgreSQL?
* What indexes will be useful for race search and filtering?

### Architecture

* What is the difference between application/business logic and HTTP/API logic?
* How should the local architecture evolve toward the AWS architecture?
* Where should authentication and authorization be introduced later?
* Where should a gateway be placed?
* What is the difference between API Gateway and ALB (Application Load Balancer)?

### AWS

* How should the application be deployed to AWS?
* Which AWS services are actually needed?
* Which services introduce costs?
* What is the cheapest architecture that still teaches the intended AWS concepts?