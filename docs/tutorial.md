# JHipster Notebook: Creating an Application from Scratch, Adding Entities, and Running It

This tutorial explains step by step how to create an application with JHipster from scratch, what every question in the wizard means, which answer was chosen, and what each option does. Then it shows how to add entities with JDL and how to run the application.

Versions used: **JHipster 9.4.0**, **Spring Boot 4.1.1**, **Java 21**, **Node 24**, **React 19**, **PostgreSQL 18**.

---

## 1. Prerequisites

| Tool | Required Version | Check |
|------|------------------|-------|
| Node.js | 24+ | `node -v` |
| Java JDK | 21 | `java -version` |
| Git | Latest | `git --version` |
| Docker (optional but recommended) | Latest | `docker version` |

> There is no need to install Maven/Gradle; JHipster adds its own wrappers (`mvnw`, `npmw`) to the project.

## 2. Install the JHipster Generator

```bash
npm install -g generator-jhipster
jhipster --version   # 9.4.0
```

---

## 3. The Wizard: All Questions, the Answers, and What They Do

When you run the `jhipster` command, an interactive wizard starts. Below you can find every question, the answer chosen, and what the other options mean.

| # | Question | Choice | What the Other Options Do |
|---|----------|--------|---------------------------|
| 1 | **Application type** | `Monolith` | `Microservice`: small, independent services. `Gateway`: a single entry point in front of microservices (routing + auth). |
| 2 | **Base name** | `jhipsterNotebook` | The application name; it determines the package name, artifactId, and the title in the UI. |
| 3 | **Reactive (Spring WebFlux)** | `No` | Choosing `Yes` generates an asynchronous/reactive backend (Netty + R2DBC). `No` keeps the classic Servlet-based stack. |
| 4 | **Default package name** | `com.notebook.jhipster` | The root package where the Java classes are placed. |
| 5 | **Authentication type** | `JWT` | `OAuth 2.0/OIDC`: external identity provider (Keycloak etc.). `Session`: cookie-based session. JWT is stateless and API-friendly. |
| 6 | **Database type** | `SQL` | `MongoDB`/`Cassandra`/`Couchbase`/`Neo4j`: NoSQL alternatives. `SQL` for a relational data model. |
| 7 | **Development database** | `PostgreSQL` | `H2 disk`: file-based, no Docker needed. `H2 memory`: volatile. `MySQL`/`MariaDB`/`Oracle`/`MSSQL`: other SQL options. `PostgreSQL` keeps development closest to production. |
| 8 | **Production database** | `PostgreSQL` | The database used in production; keeping it the same as development reduces surprises. |
| 9 | **Cache provider** | `No` (none) | `Ehcache`/`Caffeine`: local (in-memory) cache. `Hazelcast`/`Infinispan`: distributed cache. `Redis`/`Memcached`: external cache server. Cache disabled for a simple monolith. |
| 10 | **Hibernate 2nd level cache** | (skipped) | This question is not asked when the cache provider is `no`. |
| 11 | **Build tool** | `Maven` | `Gradle`: faster and more flexible build. Maven is more common. |
| 12 | **Other technologies** (multi-select) | (none) | `GraphQL`, `WebSocket`, `Search engine` (Elasticsearch), `Message broker` (Kafka), `API-first development`, and similar extra modules. |
| 13 | **Client framework** | `React` | `Angular`: more comprehensive and opinionated. `Vue`: lightweight and flexible. `No`: backend only. |
| 14 | **Enable internationalization** | `Yes` | Generates multi-language UI (i18n) infrastructure. `No` keeps a single language. |
| 15 | **Native language** | `English` | The primary language of the application. |
| 16 | **Additional languages** | (English only) | Additional languages (e.g. Turkish) can be added if desired. |
| 17 | **Testing frameworks** (multi-select) | (default) | Extra test tools such as `Gatling` (load testing) and `Cucumber` (BDD). |

These answers produce the **monolith + JWT + PostgreSQL + Maven + React** combination. When the wizard finishes, the application files are generated.

### Reproducing the Same Result with a Single Command

Instead of the wizard, the answers can be written into a JDL file and the same application generated deterministically. This repository contains [`app.jdl`](../app.jdl):

```
application {
  config {
    applicationType monolith
    baseName jhipsterNotebook
    packageName com.notebook.jhipster
    authenticationType jwt
    buildTool maven
    databaseType sql
    devDatabaseType postgresql
    prodDatabaseType postgresql
    cacheProvider no
    clientFramework react
    enableTranslation true
    nativeLanguage en
    languages [en]
    withAdminUi true
  }
}
```

```bash
jhipster jdl app.jdl
```

---

## 4. Install Dependencies and Run

```bash
./npmw install      # installs local Node + npm and the npm dependencies
```

Start the backend and frontend together with a single command:

```bash
./npmw run watch    # runs Spring Boot (8080) + Vite/React (9000) via concurrently
```

- Application: `http://localhost:8080`
- Frontend dev server (hot reload): `http://localhost:9000`
- Default users: `admin` / `admin` and `user` / `user`

> The application starts PostgreSQL automatically via Docker Compose (the `spring.docker.compose` setting in [`application.yml`](../src/main/resources/config/application.yml)). If Docker is not accessible, join the `docker` group or start the database manually.

---

## 5. Adding Entities with JDL

The application is ready. Now the `Region`, `Country`, `Location`, `Department`, `Task`, `Employee`, `Job`, and `JobHistory` entities, plus the `Language` enum, are added with JDL (JHipster Domain Language).

The full [`entities.jdl`](../entities.jdl) content:

```
entity Region {
  regionName String
}

entity Country {
  countryName String
}

entity Location {
  streetAddress String
  postalCode String
  city String
  stateProvince String
}

entity Department {
  departmentName String required
}

entity Task {
  title String
  description String
}

entity Employee {
  firstName String
  lastName String
  email String
  phoneNumber String
  hireDate Instant
  salary Long
  commissionPct Long
}

entity Job {
  jobTitle String
  minSalary Long
  maxSalary Long
}

entity JobHistory {
  startDate Instant
  endDate Instant
  language Language
}

enum Language {
  FRENCH, ENGLISH, SPANISH
}

relationship OneToOne {
  Country{region} to Region
}

relationship OneToOne {
  Location{country} to Country
}

relationship OneToOne {
  Department{location} to Location
}

relationship ManyToMany {
  Job{task(title)} to Task{job}
}

relationship OneToMany {
  Employee to Job{employee}
  Department to Employee{department}
}

relationship ManyToOne {
  Employee{manager} to Employee
}

relationship OneToOne {
  JobHistory{job} to Job
  JobHistory{department} to Department
  JobHistory{employee} to Employee
}

paginate JobHistory, Employee with infinite-scroll
paginate Job with pagination

service all with serviceImpl except Employee, Job
```

### What Gets Generated?

- For each entity: a JPA domain class, repository, REST controller (resource), service, DTO, React components (list/detail/update), i18n files, and Liquibase migration changelogs.
- Thanks to the relationships, foreign keys and related-record selectors in the frontend are generated automatically.
- `paginate ... with infinite-scroll` → infinite scrolling; `paginate ... with pagination` → pagination.
- `service all with serviceImpl except Employee, Job` → generates a Service + ServiceImpl layer for every entity except Employee and Job.

### Apply the Entities

```bash
jhipster jdl entities.jdl --force
```

> `--force` overwrites files that already exist (such as `master.xml`) and skips the interactive "Overwrite?" prompt. The `paginate` keyword is deprecated in JHipster 9; use `pagination` instead (it appears as a warning and does not block execution).

After the entities are added, simply restart the application:

```bash
./npmw run watch
```

Liquibase applies the new tables and relationships to the database automatically.

---

## 6. Generated Files and Libraries (Summary)

| Location | Contents |
|----------|----------|
| [`pom.xml`](../pom.xml) | Maven dependencies + build plugins |
| [`package.json`](../package.json) | npm scripts + frontend dependencies |
| `src/main/java/com/notebook/jhipster/` | Spring Boot source code (config, domain, repository, service, web/rest, security) |
| `src/main/webapp/` | The React application |
| `src/main/resources/config/` | `application.yml` and profile settings |
| `src/main/resources/config/liquibase/` | Database migrations |
| `src/main/docker/` | Docker Compose files for PostgreSQL and monitoring |
| `.jhipster/` | JSON configuration of each entity |
| `.yo-rc.json` | The JHipster configuration of the application |

**Backend libraries:** `spring-boot-starter-web`, `spring-boot-starter-data-jpa` + Hibernate, `spring-boot-starter-security` + `oauth2-resource-server` (JWT), `spring-boot-starter-validation`, `spring-boot-starter-actuator` + Micrometer, `spring-boot-starter-mail` + Thymeleaf, Liquibase, HikariCP, PostgreSQL JDBC, MapStruct, springdoc-openapi, `jhipster-framework`, Testcontainers, ArchUnit.

**Frontend libraries:** React 19, Redux Toolkit + React Redux, React Router, Axios, Bootstrap + React Bootstrap, react-hook-form, react-toastify, dayjs, Font Awesome, react-jhipster; on the build side: Vite, TypeScript, Vitest, ESLint, Prettier, Husky.

---

## 7. Command Summary

```bash
# 1. Installation
npm install -g generator-jhipster

# 2. Create the application (wizard)
jhipster

# 3. Install dependencies
./npmw install

# 4. Add entities
jhipster jdl entities.jdl --force

# 5. Run (backend + frontend together)
./npmw run watch
```

JHipster makes security, database, frontend, migrations, and CI/CD infrastructure ready within minutes. This tutorial created the `jhipsterNotebook` application from scratch, explained every wizard question, added 8 entities with JDL, and ran the application.
