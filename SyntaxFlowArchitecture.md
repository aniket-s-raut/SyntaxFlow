# SYSTEM ARCHITECTURE & TECH STACK SPECIFICATION - Version 1.0
**Project Name:** SyntaxFlow

## 1. Architectural Pattern
- **Clean Architecture (Domain-Driven Design):** The solution is divided into Core, Application, Infrastructure, and Presentation layers to ensure separation of concerns.
- **CQRS Pattern:** Command Query Responsibility Segregation implemented using **MediatR** to strictly separate read operations (Queries) from write operations (Commands).

## 2. API Design & Communication Protocols
- **RESTful Web API:** The core backend will be built as a strict REST Web API using .NET 8/9.
- **HTTP Verbs & Status Codes:** Strict adherence to REST standards using `GET`, `POST`, `PUT`, `PATCH`, and `DELETE` with appropriate HTTP status code responses (200 OK, 201 Created, 400 Bad Request, 401 Unauthorized, etc.).
- **Content Negotiation:** While the primary request/response format will be **JSON** for frontend consumption, the API will be configured with Content Negotiation to support **XML** serialization via `Accept` headers to demonstrate enterprise system integration capabilities.

## 3. Security & Authentication
- **Single Sign-On (SSO):** Integrated via Microsoft Entra ID (formerly Azure AD) using OpenID Connect (OIDC) and OAuth2 flows.
- **JSON Web Tokens (JWT):** The API will be secured using JWT-based stateless authentication issued by Entra ID. Clients must pass the JWT in the `Authorization: Bearer <token>` header.
- **Role-Based Access Control (RBAC):** Token payloads will include specific claims and roles managed in Azure to authorize specific API endpoints.

## 4. Data & Caching Strategy
- **Relational Database:** Azure SQL Database (Free Tier) managed via Entity Framework Core (Code-First Approach).
- **Distributed Caching:** Redis Cache (Azure Cache for Redis) to store frequently accessed FSRS scheduling algorithms and dictionary lookups, reducing database load.

## 5. Asynchronous Processing & Microservices
- **Parallel Processing:** Extensive use of `async/await` and the Task Parallel Library (TPL) for CPU-bound tasks like AI prompt compilation and speech telemetry parsing.
- **Message Broker:** Azure Service Bus will decouple the AI Generation Module (Azure Functions) from the main API.
