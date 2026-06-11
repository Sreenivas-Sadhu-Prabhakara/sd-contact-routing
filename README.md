# Contact Routing

BIAN Service Domain microservice — part of the [bian-platform](../../bian-platform/) landscape.

| | |
|---|---|
| **Business Area** | Sales and Service |
| **Business Domain** | Channels |
| **Functional Pattern** | Process |
| **Asset Type** | Contact Routing Record |
| **Control Record** | Contact Routing Record Procedure |
| **K8s Namespace** | `bian-sales-service` |
| **Stack** | Java 21 · Spring Boot 3 · Resilience4j · Cilium mesh |

> ⚠️ **Phase 1 (shallow):** real REST API over an in-memory store. Phase 2 replaces the store with per-domain persistence and real domain logic. This repo was stamped from `bian-platform/generator` — regenerate rather than hand-editing boilerplate.

## BIAN Semantic API

| Method | Path | BIAN action term |
|---|---|---|
| GET | `/v1/service-domain` | — (SD metadata) |
| POST | `/v1/contact-routing-record-procedure/initiate` | Initiate |
| GET | `/v1/contact-routing-record-procedure` | Retrieve (list) |
| GET | `/v1/contact-routing-record-procedure/{crId}/retrieve` | Retrieve |
| PUT | `/v1/contact-routing-record-procedure/{crId}/update` | Update |
| PUT | `/v1/contact-routing-record-procedure/{crId}/control` | Control — body `{"action": "suspend"\|"resume"\|"terminate"}` |

OpenAPI UI: `/swagger-ui.html` · Health: `/actuator/health` · Metrics: `/actuator/prometheus`

**API contract:** [`api/openapi.yaml`](api/openapi.yaml) — owned by **this repo** (contract-per-repo; no central contracts repo). The runtime spec at `/v3/api-docs` must stay compatible with it; Phase 2 adds contract tests that enforce this.

## Run locally

```bash
mvn spring-boot:run
curl localhost:8080/v1/service-domain

# lifecycle smoke test
curl -X POST localhost:8080/v1/contact-routing-record-procedure/initiate -H 'content-type: application/json' -d '{"note":"hello"}'
```

## Build & containerize

```bash
mvn -B verify
docker build -t bian/sd-contact-routing:0.1.0 .
```

## Deploy (Helm → K8s with Cilium mesh)

```bash
helm upgrade --install sd-contact-routing ./helm -n bian-sales-service
```

Exposed through the platform Gateway at path prefix `/sd-contact-routing` (Cilium Gateway API). Mesh policy (`CiliumNetworkPolicy`) allows: gateway ingress, same-area peers, Prometheus — everything else denied.
