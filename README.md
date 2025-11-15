# What Is an Event-Driven System?

> An **event-driven system** is an architecture where **components communicate through events**, not direct calls.

> Instead of services calling each other directly (as in request-response), they **emit events** that other components **consume asynchronously**.

### **Example:**

When a user places an order:

1.  The Order Service publishes an **OrderPlaced** event.
    
2.  The Payment Service listens for **OrderPlaced** and initiates a payment.
    
3.  The Inventory Service listens for **OrderPlaced** and reserves stock.

## Core Concepts

| Concept                     | Description                                                                  |
| --------------------------- | ---------------------------------------------------------------------------- |
| **Event**                   | A record of something that happened (e.g., *UserRegistered*, *OrderPlaced*). |
| **Producer / Publisher**    | Component that emits events.                                                 |
| **Consumer / Subscriber**   | Component that reacts to events.                                             |
| **Event Broker**            | Middleware that delivers events (Kafka, RabbitMQ, NATS, etc.).               |
| **Event Stream**            | A continuous flow of events ordered by time.                                 |
| **Event Store**             | Persistent log of all past events.                                           |
| **Event Schema / Contract** | Defines the structure of events (e.g., Avro, JSON Schema, Protobuf).         |

## System Architecture

Typical Production-Grade EDA (Event-Driven Architecture)

```
 ┌───────────┐     ┌─────────────┐     ┌──────────────┐
 │  Producer │──▶  │ Event Broker│──▶  │  Consumers   │
 └───────────┘     └─────────────┘     └──────────────┘
        │                   │                    │
        │        Kafka / RabbitMQ / NATS / SNS/SQS
        │                   │                    │
        ▼                   ▼                    ▼
  Microservices         Stream Processors     Databases / APIs

```

## Key Components in Production Systems

| Layer                       | Common Tools / Frameworks                                  |
| --------------------------- | ---------------------------------------------------------- |
| **Event Broker**            | Apache Kafka, RabbitMQ, NATS, AWS SNS/SQS, Azure Event Hub |
| **Stream Processing**       | Kafka Streams, Apache Flink, Apache Beam, ksqlDB           |
| **Schema Management**       | Confluent Schema Registry, Avro, Protobuf                  |
| **Orchestration / Infra**   | Kubernetes, Docker, Helm                                   |
| **Monitoring**              | Prometheus, Grafana, OpenTelemetry                         |
| **Error Handling & Replay** | Dead Letter Queues (DLQ), Event replay via Kafka topics    |
| **Security**                | TLS, OAuth2/JWT, SASL, RBAC                                |


## Design Patterns

| Pattern                                             | Description                                                                       | Example                     |
| --------------------------------------------------- | --------------------------------------------------------------------------------- | --------------------------- |
| **Event Sourcing**                                  | Store every change as an event. The current state is rebuilt by replaying events. | Financial transactions      |
| **CQRS** (Command Query Responsibility Segregation) | Separate write (commands) and read (queries) models.                              | E-commerce order tracking   |
| **Event-Carried State Transfer**                    | Events carry enough data for consumers to maintain their own local state.         | Cache synchronization       |
| **Choreography**                                    | Distributed workflow managed by event subscriptions (no central controller).      | Microservice orchestration  |
| **Orchestration**                                   | Central workflow engine (e.g., Temporal, Camunda) controls event flow.            | Business process automation |

## Example: Simplified Order Flow

**1. Order Service:**

```python
# order_service.py
event = {
    "event_name": "OrderPlaced",
    "order_id": "ORD123",
    "user_id": "U001",
    "amount": 250.00,
    "timestamp": "2025-11-15T13:00:00Z"
}
producer.send("orders", event)

```

**2. Payment Service:**

``` python
# payment_service.py
def handle_event(event):
    if event["event_name"] == "OrderPlaced":
        process_payment(event["order_id"], event["amount"])
consumer.subscribe("orders", handle_event)

```

----------

## Production Concerns

| Concern                   | Description / Solution                                       |
| ------------------------- | ------------------------------------------------------------ |
| **Reliability**           | Use message acknowledgments, retries, and DLQs.              |
| **Ordering Guarantees**   | Kafka partitions maintain order within a key.                |
| **Idempotency**           | Ensure consumers handle duplicate events safely.             |
| **Monitoring & Tracing**  | Use OpenTelemetry for distributed tracing.                   |
| **Schema Evolution**      | Manage backward/forward compatibility using Schema Registry. |
| **Backpressure Handling** | Apply consumer throttling, batching, or windowing.           |
| **Deployment & Scaling**  | Horizontal scaling via Kubernetes / autoscaling groups.      |

## Example Tech Stack (Enterprise-Ready)

| Layer              | Technology                     |
| ------------------ | ------------------------------ |
| **Message Broker** | Kafka (Confluent Cloud or MSK) |
| **Processing**     | Kafka Streams or Flink         |
| **Storage**        | PostgreSQL + Redis             |
| **Deployment**     | Kubernetes (Helm + ArgoCD)     |
| **Monitoring**     | Prometheus + Grafana           |
| **Tracing**        | Jaeger or OpenTelemetry        |
| **Contracts**      | Avro + Schema Registry         |
| **Infra as Code**  | Terraform                      |


## Testing & CI/CD

-   **Unit tests** for producers and consumers.
    
-   **Contract testing** (e.g., Pact).
    
-   **Integration tests** with embedded Kafka or Testcontainers.
    
-   **Replay testing** from event logs.
    
-   **Continuous delivery** pipelines with blue-green deployment.

## Real-World Use Cases

| Industry       | Example                                                              |
| -------------- | -------------------------------------------------------------------- |
| **E-commerce** | Order events trigger inventory, payment, and shipping microservices. |
| **FinTech**    | Transaction events feed fraud detection and analytics pipelines.     |
| **IoT**        | Device telemetry events processed by Kafka → Flink → Time-series DB. |
| **Gaming**     | In-game events drive leaderboards and reward systems.                |
| **Logistics**  | Shipment updates and tracking events across multiple carriers.       |

-----

# Reference architecture diagram

## production-grade event-driven system using Kafka, Flink, microservices, observability

<img width="1536" height="1024" alt="production-grade event-driven system" src="https://github.com/user-attachments/assets/b92b14c7-d774-4a4c-bebf-ec8f4c0fde68" />

