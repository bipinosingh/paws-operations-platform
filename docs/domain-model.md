# Domain model

## Core entities

### Owner

Represents the person responsible for one or more pets.

- `OwnerId` (UUID)
- `Name`
- `Email`
- `Phone`
- `PreferredContactMethod`

### Pet

Represents an animal receiving services.

- `PetId` (UUID)
- `OwnerId` (UUID, FK to Owner)
- `Name`
- `Species` (e.g., Dog, Cat)
- `Breed`
- `DateOfBirth`
- `Notes` (e.g., medical conditions, behavior)

### Booking

Represents a scheduled service for a pet at a specific center.

- `BookingId` (UUID)
- `PetId` (UUID, FK to Pet)
- `CenterId`
- `StartTime`
- `EndTime`
- `Status` (e.g., Created, Confirmed, InProgress, Completed, Cancelled)
- `ServiceType` (e.g., Boarding, Grooming)
- `CreatedAt`
- `UpdatedAt`

### StaffMember

Represents a staff member who can be assigned to bookings.

- `StaffId` (UUID)
- `Name`
- `CenterId`
- `Skills` (list)
- `IsActive`
- `PreferredSpecies` (optional)

Stored in **MongoDB** to allow flexible schema for skills and preferences.

### Assignment

Represents the assignment of a staff member to a booking.

- `AssignmentId` (UUID)
- `BookingId` (UUID)
- `StaffId` (UUID)
- `Status` (e.g., Assigned, InProgress, Completed)
- `AssignedAt`
- `CompletedAt` (optional)

Stored in **MongoDB** alongside staff data.

### Invoice

Represents billing for a booking.

- `InvoiceId` (UUID)
- `BookingId` (UUID)
- `Amount`
- `Currency`
- `Status` (e.g., Draft, Issued, Paid, Cancelled)
- `IssuedAt`
- `PaidAt` (optional)

Stored in **PostgreSQL** for transactional consistency.

---

## Service ownership

- **Booking Service**
  - Owner, Pet, Booking

- **Workforce Service**
  - StaffMember, Assignment

- **Billing Service**
  - Invoice

Each service owns its data and exposes APIs and events for integration.

---
