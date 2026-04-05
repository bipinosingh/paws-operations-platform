# Event contracts

All events are published to **Kafka** as JSON messages with a common envelope.

## Common envelope

```json
{
  "eventId": "uuid",
  "eventType": "string",
  "occurredAt": "2025-04-03T12:00:00Z",
  "traceId": "uuid",
  "data": { }
}


- eventId – unique ID for the event
- eventType – logical type name (e.g., BookingCreated)
- occurredAt – timestamp in UTC
- traceId – correlation ID for tracing across services
- data – event-specific payload
