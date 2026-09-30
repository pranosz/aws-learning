# Questions

## Current Questions

### Docker and local infrastructure

* What problem does Docker solve compared with running Java and PostgreSQL directly on the host?
* What is the difference between an image and a container?
* How does container networking work?
* How should environment variables and secrets be passed to containers?
* When should Docker Compose be used?
* What should be included in the backend Docker image?
* Should PostgreSQL run in Docker for local development?

### Networking

* How does an HTTP request travel from a browser to a backend?
* What are IP addresses, ports and TCP connections?
* What is the difference between a public and private subnet?
* How do route tables determine where traffic goes?
* What does an Internet Gateway do?
* What does a NAT Gateway do?
* How do Security Groups control traffic?
* What is a reverse proxy?
* What is the difference between a load balancer and a gateway?

### AWS architecture

* Which AWS services are actually needed for this application?
* What is the cheapest architecture that still teaches the intended AWS concepts without compromising security?
* When should ECS/Fargate be used instead of EC2?
* What is the appropriate role of an Application Load Balancer?
* When would API Gateway add real value?
* Which resources should be public and which should remain private?
* How should IAM follow least-privilege principles?

### Infrastructure as Code

* What problem does Infrastructure as Code solve?
* Why use AWS CDK instead of creating resources manually?
* How should infrastructure be separated from application code?
* How should development and production environments differ?
* How should infrastructure changes be reviewed and deployed?

### Database

* How should PostgreSQL move from local development to RDS securely?
* What should remain private in the AWS network?
* How should database credentials be managed?
* Which indexes will be useful for race search and filtering?
* How should backups and recovery be handled?

### CI/CD

* What should CI validate on every push?
* When should Docker images be built?
* How should images be tagged?
* How should GitHub Actions authenticate to AWS securely?
* How should deployments be rolled back?
* How should different environments be handled?

### Security

* How should authentication and authorization be introduced if the application later requires them?
* How should secrets be managed securely in AWS?
* How should database and backend network access be restricted?
* Which security checks should be part of CI/CD?
* How should public exposure be minimized?

### Architecture

* How should the local architecture evolve toward the AWS architecture?
* What is the difference between API Gateway and ALB (Application Load Balancer)?
* Which components are genuinely needed and which would be unnecessary complexity?
* How should the architecture balance security, cost, availability and learning value?
