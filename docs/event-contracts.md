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
