#  MechanicShop Workshop

![.NET 10](https://img.shields.io/badge/.NET-10-512BD4?style=for-the-badge&logo=.net)
![C#](https://img.shields.io/badge/C%23-Language-239120?style=for-the-badge&logo=csharp)
![Blazor](https://img.shields.io/badge/Blazor-WebAssembly-512BD4?style=for-the-badge&logo=blazor)
![SQL Server](https://img.shields.io/badge/Database-SQL%20Server-CC2927?style=for-the-badge&logo=microsoft-sql-server)
![Clean Architecture](https://img.shields.io/badge/Architecture-Clean-blue?style=for-the-badge)

A professional, full-stack **Mechanic Shop Management System** built with **.NET 10** and **Blazor**, following **Clean Architecture**, **CQRS**, and **Domain-Driven Design** principles. The system manages work orders, customers, repair tasks, scheduling, billing, and identity. designed to reflect real-world, production-grade software engineering practices.

---

##  Table of Contents

- [Project Overview](#-project-overview)
- [Architecture & Patterns](#-architecture--patterns)
- [Tech Stack](#-tech-stack)
- [Key Features](#-key-features)
- [API Endpoints](#-api-endpoints)
- [Getting Started](#-getting-started)
- [Testing Strategy](#-testing-strategy)
- [Best Practices](#-best-practices)

---

##  Project Overview

MechanicShop is a backend + frontend workshop management platform designed to streamline day-to-day operations of an automotive repair shop. It provides:

- A **RESTful API** and **Minimal API Endpoints** for core business operations
- A **Blazor WebAssembly** client for managing schedules, customers, work orders, and invoices
- **Real-time notifications** via SignalR for live work order updates
- **JWT-based authentication** with refresh token support
- **PDF invoice generation** and email notification capabilities

---

##  Architecture & Patterns

The solution is structured around **Clean Architecture**, ensuring a clear separation of concerns and independence between layers.

```
┌─────────────────────────────────────────────┐
│               MechanicShop.Api              │  ← Presentation Layer
│         (Controllers + Endpoints)           │
├─────────────────────────────────────────────┤
│          MechanicShop.Application           │  ← Application Layer
│     (CQRS Commands/Queries via MediatR)     │
├─────────────────────────────────────────────┤
│          MechanicShop.Infrastructure        │  ← Infrastructure Layer
│    (EF Core, Identity, SignalR, Services)   │
├─────────────────────────────────────────────┤
│            MechanicShop.Domain              │  ← Domain Layer
│       (Entities, Value Objects, Events)     │
└─────────────────────────────────────────────┘
```

### Layers Breakdown

| Layer | Project | Responsibility |
|---|---|---|
| **Domain** | `MechanicShop.Domain` | Core business entities, domain events, value objects, and business rules. Zero external dependencies. |
| **Application** | `MechanicShop.Application` | Use cases implemented as CQRS Commands/Queries using MediatR. Contains validators, mappers, DTOs, and interfaces. |
| **Infrastructure** | `MechanicShop.Infrastructure` | EF Core DbContext, Identity, Token Provider, SignalR Hub, PDF generation, email notifications, and background jobs. |
| **API** | `MechanicShop.Api` | ASP.NET Core Web API exposing both traditional Controllers and Minimal API Endpoints. Handles OpenAPI/Swagger, middleware, and DI wiring. |
| **Client** | `MechanicShop.Client` | Blazor WebAssembly SPA consuming the API with JWT bearer auth and real-time SignalR integration. |
| **Contracts** | `MechanicShop.Contracts` | Shared request/response models between the API and client. |

### Design Patterns

- **CQRS** — Commands and Queries are strictly separated using **MediatR**
- **Pipeline Behaviours** — Cross-cutting concerns (validation, logging, caching, performance monitoring, exception handling) are implemented as MediatR pipeline behaviors
- **Repository Pattern** — Data access is abstracted via `IAppDbContext`
- **Result Pattern** — Domain operations return typed `Result<T>` objects instead of throwing exceptions
- **Domain Events** — Domain state changes raise events (e.g., `WorkOrderCompleted`, `WorkOrderCollectionModified`) handled asynchronously

---

##  Tech Stack

### Backend
| Technology | Purpose |
|---|---|
| **.NET 10 / C#** | Core runtime and language |
| **ASP.NET Core** | Web API framework (Controllers + Minimal APIs) |
| **Entity Framework Core** | ORM for database access |
| **MediatR** | CQRS mediator pattern implementation |
| **FluentValidation** | Input validation for commands and queries |
| **ASP.NET Core Identity** | User management and authentication |
| **JWT Bearer Tokens** | Stateless authentication with refresh tokens |
| **SignalR** | Real-time push notifications for work order updates |
| **Serilog** | Structured logging |
| **QuestPDF / PDF Generator** | Invoice PDF generation |
| **Docker** | Containerization |

### Frontend
| Technology | Purpose |
|---|---|
| **Blazor WebAssembly** | SPA client framework |
| **MudBlazor / Custom Components** | UI component library |
| **SignalR Client** | Real-time updates in the browser |

### Testing
| Technology | Purpose |
|---|---|
| **xUnit** | Test framework |
| **FluentAssertions** | Readable test assertions |
| **WebApplicationFactory** | Integration and subcutaneous test host |

---

##  Key Features

### Work Order Management
- Create, update, relocate, and delete work orders
- Assign repair tasks and labor to work orders
- Track and transition work order states (e.g., Pending → InProgress → Completed)
- Real-time updates pushed to connected clients via SignalR

### Customer Management
- Full CRUD for customers with paginated list support
- Associate multiple vehicles per customer
- Vehicle create and update operations

### Repair Task Management
- Define reusable repair tasks with associated parts
- Create, update, and remove repair tasks and their parts
- Query by ID or paginated list

### Scheduling
- Daily schedule view with availability slots and time-slot markers
- Spot-based scheduling (e.g., multiple bays)
- Work order relocation across schedule slots

### Billing & Invoicing
- Issue invoices from completed work orders
- Settle (pay) outstanding invoices
- Generate downloadable PDF invoices
- Invoice line item tracking

### Identity & Security
- JWT access token generation and refresh token rotation
- Role-based authorization (e.g., labor assignment policy)
- Secure token storage and revocation

### Dashboard
- Aggregate today's work order statistics for at-a-glance operational insight

### Background Services
- Automated cleanup of overdue bookings via a hosted background job

---

##  API Endpoints

### Customers
| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/api/customers` | Get paginated list of customers |
| `GET` | `/api/customers/{id}` | Get customer by ID |
| `POST` | `/api/customers` | Create a new customer |
| `PUT` | `/api/customers/{id}` | Update a customer |
| `DELETE` | `/api/customers/{id}` | Remove a customer |

### Work Orders
| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/api/workorders` | Get filtered/paginated work orders |
| `GET` | `/api/workorders/{id}` | Get work order by ID |
| `POST` | `/api/workorders` | Create a new work order |
| `PUT` | `/api/workorders/{id}/state` | Update work order state |
| `PUT` | `/api/workorders/{id}/relocate` | Relocate a work order slot |
| `PUT` | `/api/workorders/{id}/repair-tasks` | Update repair tasks on a work order |
| `PUT` | `/api/workorders/{id}/labor` | Assign labor to a work order |
| `DELETE` | `/api/workorders/{id}` | Delete a work order |

### Repair Tasks
| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/api/repairtasks` | Get all repair tasks |
| `GET` | `/api/repairtasks/{id}` | Get repair task by ID |
| `POST` | `/api/repairtasks` | Create a repair task |
| `PUT` | `/api/repairtasks/{id}` | Update a repair task |
| `DELETE` | `/api/repairtasks/{id}` | Remove a repair task |

### Billing
| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/api/invoices/{id}` | Get invoice by ID |
| `GET` | `/api/invoices/{id}/pdf` | Download invoice as PDF |
| `POST` | `/api/invoices` | Issue an invoice |
| `PUT` | `/api/invoices/{id}/settle` | Settle (pay) an invoice |

### Identity
| Method | Endpoint | Description |
|---|---|---|
| `POST` | `/api/identity/token` | Generate JWT access + refresh tokens |
| `POST` | `/api/identity/refresh` | Refresh access token |
| `GET` | `/api/identity/me` | Get current user info |

### Dashboard & Settings
| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/api/dashboard/stats` | Get today's work order statistics |
| `GET` | `/api/settings` | Get application settings |

---

##  Getting Started

### Prerequisites

- [.NET 10 SDK](https://dotnet.microsoft.com/download)
- [Docker](https://www.docker.com/) (optional, for containerized setup)
- A running SQL Server instance (or configure for SQLite/PostgreSQL)

### Clone the Repository

```bash
git clone https://github.com/your-username/MechanicShopWorkshop.git
cd MechanicShopWorkshop
```

### Restore Dependencies

```bash
dotnet restore
```

### Apply Database Migrations

```bash
dotnet ef database update --project src/MechanicShop.Infrastructure --startup-project src/MechanicShop.Api
```

### Run the API

```bash
dotnet run --project src/MechanicShop.Api
```

The API will be available at `https://localhost:5001`. Swagger UI is accessible at `https://localhost:5001/swagger`.

### Run the Blazor Client

```bash
dotnet run --project src/MechanicShop.Client
```

### Run with Docker

```bash
docker-compose up --build
```

### Run Tests

```bash
# Run all tests
dotnet test

# Run specific test project
dotnet test tests/MechanicShop.Domain.UnitTests
dotnet test tests/MechanicShop.Application.SubcutaneousTests
dotnet test tests/MechanicShop.Api.IntegrationTests
```

---

##  Testing Strategy

The project employs a comprehensive, multi-layered testing approach:

| Test Type | Project | Scope |
|---|---|---|
| **Unit Tests (Domain)** | `MechanicShop.Domain.UnitTests` | Pure domain logic: entities, aggregates, value objects |
| **Unit Tests (Application)** | `MechanicShop.Application.UnitTests` | MediatR behaviors, mappers |
| **Subcutaneous Tests** | `MechanicShop.Application.SubcutaneousTests` | Full application pipeline via in-memory `WebApplicationFactory` — tests commands, queries, validators, and event handlers end-to-end without going through HTTP |
| **Integration Tests** | `MechanicShop.Api.IntegrationTests` | Full HTTP request/response cycle via `WebApplicationFactory` |

Shared test infrastructure lives in `MechanicShop.Tests.Common`, including **factory classes** for creating test entities (e.g., `CustomerFactory`, `WorkOrderFactory`, `InvoiceFactory`) and test user helpers.

---

##  Best Practices

This project is built to reflect professional, production-grade engineering standards:

- **SOLID Principles** — Each class has a single responsibility; dependencies are injected via interfaces
- **Clean Architecture** — Business logic is completely isolated from infrastructure and UI concerns
- **Domain-Driven Design (DDD)** — Rich domain models with encapsulated behavior, domain events, and aggregate roots
- **Result Pattern** — Explicit, typed error handling without exception-driven control flow
- **Pipeline Behaviours** — Cross-cutting concerns (validation, logging, caching, performance) are handled generically via MediatR
- **Structured Logging** — Request context and performance metrics are logged via Serilog
- **OpenAPI / Swagger** — API is fully documented with bearer security scheme and version info transformers
- **CI/CD** — GitHub Actions workflow (`build-and-test.yml`) automates build and test on every push
- **Docker Support** — Containerized deployment with `Dockerfile` and `docker-compose.yml`

---

Built with ❤️
