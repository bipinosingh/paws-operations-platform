# Paws Operations Platform 🐾

Event-driven, cloud-native reference architecture for a multi-location care center operations platform.

This project is designed as a **Solution Architect / Systems Architect** portfolio piece, demonstrating:

- Microservices and modern web architecture
- Event-driven design with **Kafka**
- Task and workflow messaging with **RabbitMQ**
- Caching and distributed coordination with **Redis**
- Polyglot persistence with **PostgreSQL** and **MongoDB**
- API-first design with an aggregation **API Gateway**
- Cloud-native deployment patterns (Docker, Kubernetes-ready structure)

---

## Problem domain

Imagine a network of care centers (e.g., pet boarding, grooming, or similar services). The platform must:

- Manage **bookings** for pets/clients across locations
- Coordinate **workforce assignments** (who handles which booking)
- Generate **billing** information for completed services
- Integrate with external systems (ERP/CRM/logistics) in an event-driven way

This repository implements a simplified but realistic version of that platform.

---

## High-level architecture

The system is composed of several microservices:

- **Booking Service**
  - Manages bookings, pets, and owners
  - Stores data in **PostgreSQL**
  - Publishes domain events (e.g., `BookingCreated`) to **Kafka**
  - Sends workflow commands (e.g., email notifications) via **RabbitMQ**

- **Workforce Service**
  - Manages staff and assignments
  - Stores data in **MongoDB**
  - Uses **Redis** for caching and distributed locks during assignment
  - Consumes `BookingCreated` events from **Kafka**
  - Publishes `StaffAssigned` events to **Kafka**

- **Billing Service**
  - Manages invoices
  - Stores data in **PostgreSQL**
  - Consumes `BookingCreated` and `StaffAssigned` events from **Kafka**
  - Publishes `InvoiceGenerated` events

- **API Gateway**
  - Single entry point for clients
  - Aggregates data from Booking, Workforce, and Billing services
  - Uses **Redis** to cache aggregated booking details

Core infrastructure:

- **Kafka** – event streaming backbone
- **RabbitMQ** – command/workflow queue
- **Redis** – caching and distributed coordination
- **PostgreSQL** – relational storage
- **MongoDB** – document storage

See [`docs/architecture-overview.md`](docs/architecture-overview.md) for diagrams and more detail.

---

## Tech stack

- **Language/Runtime:** .NET 8 (C#), minimal APIs
- **Messaging:**
  - Kafka (event streaming)
  - RabbitMQ (commands/tasks)
- **Data:**
  - PostgreSQL (bookings, invoices)
  - MongoDB (workforce, assignments)
  - Redis (cache, locks)
- **Infra:**
  - Docker Compose for local development
  - Kubernetes manifests (structure prepared under `infra/k8s/`)
- **Observability:**
  - Structured logging
  - Correlation IDs across services (planned)

---

## Services

- `booking-service`
- `workforce-service`
- `billing-service`
- `api-gateway`

Each service is designed to be independently deployable and owns its own data.

---

## Getting started (planned)

> Implementation is being built incrementally. Initial focus is on:
> 1. Booking Service (PostgreSQL + Kafka + RabbitMQ)
> 2. Workforce Service (MongoDB + Redis + Kafka)
> 3. Billing Service (PostgreSQL + Kafka)
> 4. API Gateway (aggregation + Redis cache)

Once the first slice is complete, you will be able to:

1. Start the stack with Docker Compose:
   ```bash
   docker compose up
