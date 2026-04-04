# Architecture overview

## Context

The Paws Operations Platform models a multi-location care center environment where:

- Clients (owners) bring their pets for services.
- Bookings are created and managed across centers.
- Staff are assigned to fulfill bookings.
- Billing is generated based on completed services.

The platform is designed as an **event-driven, microservices-based system** that can integrate with external ERP/CRM/logistics systems.

---

## Core architectural principles

- **API-first design**  
  Each service exposes clear, versioned HTTP APIs.

- **Event-driven communication**  
  Domain events are published to **Kafka** for durability, replay, and integration.

- **Separation of concerns**  
  Booking, Workforce, Billing, and API Gateway are independent services with their own data stores.

- **Polyglot persistence**  
  Use the right storage for the right workload:
  - PostgreSQL for transactional data (bookings, invoices)
  - MongoDB for flexible workforce/assignment documents
  - Redis for caching and coordination

- **Asynchronous workflows**  
  Use **Kafka** for domain events and **RabbitMQ** for commands/tasks.

---

## Services and responsibilities

### Booking Service

- **Responsibilities**
  - Manage bookings, pets, and owners
  - Persist bookings in PostgreSQL
  - Publish `BookingCreated` and `BookingStatusChanged` events to Kafka
  - Send workflow commands (e.g., `SendBookingConfirmationEmail`) to RabbitMQ

- **Key integrations**
  - Kafka (producer)
  - RabbitMQ (producer)
  - PostgreSQL

---

### Workforce Service

- **Responsibilities**
  - Manage staff and assignments
  - Persist staff and assignments in MongoDB
  - Consume `BookingCreated` events from Kafka
  - Use Redis for:
    - Caching staff availability
    - Distributed locks to avoid double assignment
  - Publish `StaffAssigned` events to Kafka

- **Key integrations**
  - Kafka (consumer + producer)
  - MongoDB
  - Redis

---

### Billing Service

- **Responsibilities**
  - Manage invoices
  - Persist invoices in PostgreSQL
  - Consume `BookingCreated` and `StaffAssigned` events from Kafka
  - Publish `InvoiceGenerated` events to Kafka

- **Key integrations**
  - Kafka (consumer + producer)
  - PostgreSQL

---

### API Gateway

- **Responsibilities**
  - Provide a single entry point for clients
  - Route requests to underlying services
  - Aggregate data for:
    - `GET /bookings/{id}/details`
      - Booking (Booking Service)
      - Assignment (Workforce Service)
      - Invoice (Billing Service)
  - Cache aggregated responses in Redis

- **Key integrations**
  - Booking, Workforce, Billing services (HTTP)
  - Redis (cache)

---

## Messaging and data flow

### Event streaming with Kafka

Kafka is used as the **event backbone**:

- `BookingCreated` → produced by Booking Service
- `StaffAssigned` → produced by Workforce Service
- `InvoiceGenerated` → produced by Billing Service

These events can be consumed by:

- Internal services (e.g., Billing, Workforce)
- External systems (e.g., ERP, analytics, reporting)

---

### Command and task messaging with RabbitMQ

RabbitMQ is used for **short-lived commands and background tasks**, such as:

- `SendBookingConfirmationEmail`
- `GenerateDailySummaryReport`
- `NotifyStaffOfNewAssignment`

This separation demonstrates understanding of:

- **Event streaming** (Kafka) vs
- **Message queuing / task dispatch** (RabbitMQ)

---

## High-level diagrams

> Diagrams can be created using draw.io or Mermaid and stored under `docs/diagrams/`.

Suggested diagrams:

1. **Context diagram**  
   - Paws Operations Platform  
   - External systems (ERP, CRM, Notifications, Analytics)

2. **Container diagram**  
   - Booking, Workforce, Billing, API Gateway  
   - Kafka, RabbitMQ, Redis, PostgreSQL, MongoDB

3. **Sequence diagram: Create booking flow**  
   - Client → API Gateway → Booking Service → Kafka → Workforce Service → Kafka → Billing Service  
   - API Gateway → Aggregated read

---
