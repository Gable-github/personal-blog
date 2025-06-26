---
title: "Open Source Project: Springboot Auth Microservice"
date: 2025-06-04
authors:
  - Gabryel Soh
description: "A production-ready, secure, and well-documented authentication microservice built with Spring Boot, designed for easy integration into microservice architectures."
github: 'https://github.com/Gable-github/auth-microservice'
tech:
  - Spring Boot
  - Java 17
  - PostgreSQL
  - Spring Security
  - JWT
  - AWS Secrets Manager
  - AWS EC2
  - Docker & Docker Compose
  - Github Actions CI/CD
  - Maven
  - Lombok
  - Spring Data JPA
  - Spring Validation
  - Spring Web
  - Spring Test
  - Spring Boot Actuator
  - Spring Boot DevTools
company: 'Open Source Project'
showInProjects: true
tags:
  - Personal Projects
---

# Open Source: Springboot Auth Microservice

Recognizing the recurring challenges of implementing secure authentication across distributed systems, I developed this lightweight authentication microservice to address common user management and JWT-based authentication requirements.

Built with Spring Boot, it's designed to be easily integrated into any microservice architecture, almost like plug-and-play where you've got a fully functional auth system for your other services.

This project was created to provide a production-ready, secure, and well-documented authentication solution that streamlines development while maintaining enterprise-grade security standards for all.

The microservice is designed for flexible deployment across various environments - from local development to production-grade infrastructure, supporting both containerized deployments via Docker and orchestration through Kubernetes.

API documentation is provided through SpringDoc OpenAPI (Swagger UI).

**Key Features:**

- User registration and login endpoints
- JWT-based authentication and authorization
- Secure password hashing and input validation
- PostgreSQL database integration
- Environment-based configuration (local, production, Docker)
- AWS Secrets Manager for secure secret management in production
- RESTful API design with clear separation of concerns
- Comprehensive unit and integration tests
- Docker support for easy deployment and development

**Security Highlights:**

- Passwords are securely hashed before storage
- JWT tokens are signed and validated using environment secrets
- Production secrets managed via AWS Secrets Manager
- HTTPS recommended for all deployments

**Development & Deployment:**

- Profile-based configuration for local and production
- Docker Compose for local and production-like environments
- CI/CD with Github Actions

For more details, see the [GitHub repository](https://github.com/Gable-github/auth-microservice). 