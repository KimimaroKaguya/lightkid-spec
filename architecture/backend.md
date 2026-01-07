# LightKid Backend Specification

## Overview

The LightKid backend is designed to support a child growth and activity tracking application. It is built using Go (Golang) and utilizes the Fiber v2 web framework. The project follows a Clean Architecture pattern to ensure scalability, testability, and maintainability.

## Technology Stack

- **Language:** Go 1.25.1
- **Web Framework:** Fiber v2
- **Database:** PostgreSQL
- **Observability:** OpenTelemetry (OTEL)
- **Documentation:** Swagger (Swag)
- **Testing:** Testify, SQLMock
- **Code Quality Results**: 100% Test Coverage Required for Business Logic
- **CI/CD:** GitHub Actions

## Project Structure

The project is organized as follows:

```
backend/
├── .air.toml               # Configuration for Air (live reload)
├── docker-compose.yml      # Docker Compose file
├── go.mod                  # Go module definition and dependencies
├── migrations/             # Database migration files
├── src/                    # Source code
│   ├── cmd/                # Application entry points
│   │   └── api/            # Main API server entry point
│   ├── config/             # Configuration loading and management
│   ├── dto/                # Data Transfer Objects (request/response models)
│   ├── entity/             # Domain entities and business objects
│   ├── infra/              # Infrastructure layer (database, external APIs)
│   ├── internal/           # Internal application logic (private code)
│   ├── pkg/                # Public shared packages / libraries
│   ├── routers/            # HTTP route definitions (endpoints only)
│   └── usecase/            # Business logic interfaces
└── ...
```

## Source Code Details (src/)

### 1. cmd/ (Entry Points)

Contains the main application entry points.

- **api/**: The main REST API server for the LightKid application.

### 2. routers/ (HTTP Interface)

Defines the HTTP endpoints and maps them to handlers.

- **routes.go**: Main router setup.
- **authrouters.go**: Auth routes.
- **childrouters.go**: Child routes.
- ... (and so on for other features)

### 3. internal/ (Implementation)

Contains the implementation of handlers and services.

- **handlers/**: HTTP handlers that parse requests and call services.
- **services/**: Business logic implementation implementing usecase interfaces.

### 4. usecase/ (Interfaces)

Defines the interfaces for business logic.

- **auth_usecase.go**: Interface for auth logic.
- **child_usecase.go**: Interface for child logic.
- ...

### 5. entity/ (Domain)

Defines the core business objects.

- **User**: Parent or guardian account.
- **Child**: Child profile (Name, DOB, Gender).
- **GrowthRecord**: Height, Weight, Head Circumference measurements.
- **SleepRecord**: Sleep duration, start time, end time.
- **Activity**: Activities like feeding, playing, etc.

### 5. infra/ (Infrastructure)

Implements interfaces defined in the domain layer.

- **postgres**: PostgreSQL database implementation.
- **auth**: JWT token generation and validation.

### 6. dto/ (Data Transfer Objects)

Defines the structures used for API requests and responses.

- `LoginRequest`, `LoginResponse`
- `CreateChildRequest`, `ChildResponse`
- `AddGrowthRecordRequest`

### 7. config/

Handles application configuration (DB connection strings, API keys, etc.).

### 8. pkg/

Shared utility code (e.g., logger, validator, error handling).

## Infrastructure & Deployment

- **Docker**: Containerized using Docker.
- **Air**: Live reloading for local development.
- **CI/CD**: GitHub Actions for automated testing and deployment.
- **Linting**: golangci-lint for code quality.

## Key Dependencies

- `github.com/gofiber/fiber/v2`: Web framework.
- `gorm.io/gorm`: ORM (optional, or use raw SQL/sqlx as per preference).
- `gorm.io/driver/postgres`: PostgreSQL driver.
- `github.com/swaggo/swag`: API documentation.
