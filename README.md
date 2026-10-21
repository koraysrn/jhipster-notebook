# JHipster Notebook

A hands-on learning resource for [JHipster](https://www.jhipster.tech): a from-scratch, step-by-step notebook that shows how a full-stack application is generated, what every wizard question means, how entities are added with JDL, and how the result runs.

> This repository is an **educational companion** to the tutorial in [`docs/tutorial.md`](docs/tutorial.md). The code is here to be read, run, and studied — not as a production product.

## What You Will Learn

- How to install and run the JHipster generator
- What every `jhipster` wizard question does and which options exist
- How the same application can be reproduced with a JDL file
- How to add entities, enums, and relationships with JDL
- How a generated JHipster project is structured (Spring Boot backend + React frontend)

## Tech Stack

| Layer          | Technology                                                                   |
| -------------- | ---------------------------------------------------------------------------- |
| Backend        | Spring Boot 4.1.1 (Java 21), JPA/Hibernate, Spring Security (JWT), Liquibase |
| Frontend       | React 19, Redux Toolkit, React Router, Bootstrap, Vite, TypeScript           |
| Database       | PostgreSQL 18 (dev & prod), HikariCP                                         |
| Build          | Maven (wrappers: `mvnw`, `npmw`)                                             |
| Infrastructure | Docker Compose (PostgreSQL, monitoring), Jib                                 |

## Repository Layout

| Path                                   | Purpose                                                                     |
| -------------------------------------- | --------------------------------------------------------------------------- |
| [`docs/tutorial.md`](docs/tutorial.md) | The full step-by-step tutorial (wizard Q&A, JDL entities, run instructions) |
| [`app.jdl`](app.jdl)                   | The application configuration (the same answers as the wizard)              |
| [`entities.jdl`](entities.jdl)         | The JDL that defines all 8 entities and their relationships                 |
| `src/main/java/`                       | Generated Spring Boot backend                                               |
| `src/main/webapp/`                     | Generated React frontend                                                    |
| `src/main/resources/config/liquibase/` | Database migrations                                                         |
| `src/main/docker/`                     | Docker Compose files                                                        |
| `.jhipster/`                           | Per-entity JSON configuration                                               |

## Entities

`Region`, `Country`, `Location`, `Department`, `Task`, `Employee`, `Job`, `JobHistory` plus the `Language` enum — with OneToOne, OneToMany, ManyToOne, and ManyToMany relationships. See [`entities.jdl`](entities.jdl).

## Quick Start

### Prerequisites

- Node.js 24+
- Java JDK 21
- Git
- Docker (for PostgreSQL)

### Run

```bash
npm install -g generator-jhipster   # install the generator (once)

./npmw install                      # install dependencies
./npmw run watch                    # backend (8080) + frontend (9000) together
```

- Application: http://localhost:8080
- Frontend dev server: http://localhost:9000
- Default users: `admin` / `admin`, `user` / `user`

> The app starts PostgreSQL automatically via Docker Compose (see the `spring.docker.compose` setting in [`application.yml`](src/main/resources/config/application.yml)). If Docker is not accessible, join the `docker` group or start the database manually.

### Regenerate or add entities

```bash
jhipster jdl app.jdl                # regenerate the app from the wizard answers
jhipster jdl entities.jdl --force   # (re)generate all entities
```

## Development & Build

| Task                          | Command                                |
| ----------------------------- | -------------------------------------- |
| Run backend only              | `./npmw run backend:start`             |
| Run frontend only             | `./npmw run start`                     |
| Run both (backend + frontend) | `./npmw run watch`                     |
| Backend unit tests            | `./npmw run backend:unit:test`         |
| Frontend tests                | `./npmw run test`                      |
| Production build              | `./mvnw -Pprod clean verify`           |
| Run production jar            | `java -jar target/*.jar`               |
| Docker image                  | `./mvnw -Pprod verify jib:dockerBuild` |

## Learn More

The full walkthrough — every wizard question and what each option does, the JDL entity definitions explained, and the step-by-step run instructions — is in [`docs/tutorial.md`](docs/tutorial.md).

## License

This is a learning resource generated with JHipster 9.4.0. Feel free to read, run, and adapt it for your own learning.
