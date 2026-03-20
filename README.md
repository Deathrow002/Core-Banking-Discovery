# Core Banking — Discovery Service

A Spring Cloud Netflix Eureka server that acts as the service registry for the Core Banking microservices platform. All services register here on startup and use it to discover each other.

## Tech Stack

| Layer | Technology |
|---|---|
| Language | Java 21 |
| Framework | Spring Boot, Spring Cloud Netflix Eureka Server |
| Cache | Caffeine (10-min TTL) |
| Metrics | Micrometer + Prometheus |
| Build | Maven |
| Runtime | Docker / Kubernetes |

## Configuration

| Property | Value |
|---|---|
| Port | `8761` |
| Application name | `discovery-service` |
| Eureka URL | `http://localhost:8761/eureka/` |
| Self-preservation | Enabled |
| Lease renewal interval | 30 s |
| Lease expiration duration | 90 s |

## Running Locally

### Prerequisites
- Java 21
- Maven 3.9+
- Parent POM installed (`mvn install -N` from project root)

### Build & Run
```bash
# From project root
mvn clean install -N

# From Discovery directory
mvn spring-boot:run
```

The Eureka dashboard will be available at [http://localhost:8761](http://localhost:8761).

## Running with Docker

```bash
# From project root
docker build -f Discovery/Dockerfile -t discovery-service .
docker run -p 8761:8761 discovery-service
```

## Running on Kubernetes

```bash
kubectl apply -f k8s/deployments/namespace.yml
kubectl apply -f k8s/deployments/discovery-service.yml
# Check status
kubectl get pods -l app=discovery-service -n core-bank
```

Port-forward for local access:
```bash
kubectl port-forward svc/discovery-service 8761:8761 -n core-bank
```

## Health & Metrics

| Endpoint | Description |
|---|---|
| `GET /actuator/health` | Liveness / readiness probes |
| `GET /actuator/info` | Application info |
| `GET /actuator/prometheus` | Prometheus metrics scrape endpoint |

## Service Registration

Other microservices register with this server by setting the following in their `application.properties`:

```properties
eureka.client.serviceUrl.defaultZone=http://discovery-service:8761/eureka/
eureka.client.registerWithEureka=true
eureka.client.fetchRegistry=true
```

## Registered Services

| Service | Default Port |
|---|---|
| Account Service | 8081 |
| Customer Service | 8083 |
| Transaction Service | 8082 |
| Authentication Service | 8084 |
