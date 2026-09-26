# DATABASE SCHEMA & API DOCUMENTATION - Version 1.0
**Project Name:** SyntaxFlow

## 1. Database Schema (Azure SQL)
The database is designed for Entity Framework Core using a Code-First approach. It tracks user telemetry, FSRS spaced repetition data, and uploaded reading materials.

### 1.1 Tables & Entities

**Users** (Authentication managed via Microsoft Entra ID; this table links telemetry to the user)
| Column | Type | Constraints | Description |
| :--- | :--- | :--- | :--- |
| `Id` | UNIQUEIDENTIFIER | PK | Primary Key |
| `EntraObjectId` | NVARCHAR(256) | UNIQUE, INDEX | Mapped from Entra ID JWT `oid` claim |
| `Email` | NVARCHAR(256) | NOT NULL | User's email |
| `CreatedAt` | DATETIME2 | NOT NULL | Account creation timestamp |

**TypingSessions** (Telemetry Data)
| Column | Type | Constraints | Description |
| :--- | :--- | :--- | :--- |
| `Id` | UNIQUEIDENTIFIER | PK | Primary Key |
| `UserId` | UNIQUEIDENTIFIER | FK | Foreign Key to Users table |
| `Wpm` | INT | NOT NULL | Words Per Minute |
| `Accuracy` | DECIMAL(5,2) | NOT NULL | Typing accuracy percentage |
| `HesitationMap` | NVARCHAR(MAX) | NULL | JSON string of keystroke hesitation metrics |
| `SessionDate` | DATETIME2 | NOT NULL | Date of practice |

**Flashcards** (Strict FSRS Algorithm Data)
| Column | Type | Constraints | Description |
| :--- | :--- | :--- | :--- |
| `Id` | UNIQUEIDENTIFIER | PK | Primary Key |
| `UserId` | UNIQUEIDENTIFIER | FK | Foreign Key to Users table |
| `TargetWord` | NVARCHAR(100) | NOT NULL | The vocabulary word |
| `ContextSentence`| NVARCHAR(MAX) | NOT NULL | The sentence where the word was found |
| `Difficulty` | DECIMAL(10,4) | NOT NULL | FSRS Difficulty (D) |
| `Stability` | DECIMAL(10,4) | NOT NULL | FSRS Stability (S) |
| `Repetitions` | INT | NOT NULL | FSRS Repetition count |
| `Lapses` | INT | NOT NULL | FSRS Lapse count |
| `LastReview` | DATETIME2 | NULL | Last reviewed date |
| `Due` | DATETIME2 | NOT NULL, INDEX| Next scheduled review date |

**ReadingDocuments**
| Column | Type | Constraints | Description |
| :--- | :--- | :--- | :--- |
| `Id` | UNIQUEIDENTIFIER | PK | Primary Key |
| `UserId` | UNIQUEIDENTIFIER | FK | Foreign Key to Users table |
| `Title` | NVARCHAR(255) | NOT NULL | Document title |
| `BlobUri` | NVARCHAR(500) | NOT NULL | URL to Azure Blob Storage file |
| `UploadedAt` | DATETIME2 | NOT NULL | Upload timestamp |

---

## 2. API Endpoints (RESTful Web API)
All endpoints require a valid JWT issued by Microsoft Entra ID. The API supports Content Negotiation (`application/json` default, `application/xml` via `Accept` header).

### 2.1 Typing Engine
| Verb | Endpoint | CQRS Pattern | Description |
| :--- | :--- | :--- | :--- |
| `POST` | `/api/v1/typing/sessions` | Command | Submits a completed typing session telemetry payload. |
| `GET` | `/api/v1/typing/stats` | Query | Retrieves user's historical WPM and accuracy metrics. |

### 2.2 FSRS Flashcards
| Verb | Endpoint | CQRS Pattern | Description |
| :--- | :--- | :--- | :--- |
| `GET` | `/api/v1/flashcards/due` | Query | Retrieves a list of flashcards due for review today. |
| `POST` | `/api/v1/flashcards` | Command | Creates a new contextual flashcard from the E-Reader. |
| `PUT` | `/api/v1/flashcards/{id}/review`| Command | Updates FSRS metrics based on user's recall grade (1-4). |

### 2.3 E-Reader
| Verb | Endpoint | CQRS Pattern | Description |
| :--- | :--- | :--- | :--- |
| `POST` | `/api/v1/documents/upload` | Command | Uploads a PDF/ePub to Blob Storage and creates a DB record. |
| `GET` | `/api/v1/documents` | Query | Lists all user documents. |

### 2.4 AI Content Generation (Asynchronous)
| Verb | Endpoint | Pattern | Description |
| :--- | :--- | :--- | :--- |
| `POST` | `/api/v1/ai/generate` | Event-Driven | Pushes generation request to Azure Service Bus. Returns `202 Accepted`. |

---

## 3. Azure Service Bus Message Contracts
Since AI Generation is decoupled via a microservice (Azure Functions), the main API publishes the following JSON payload to the Service Bus Topic `ai-generation-requests`:

```json
{
  "RequestId": "b73d2...",
  "UserId": "a14f5...",
  "ScenarioType": "Azure_DevOps_Pipeline",
  "EnglishLevel": "Intermediate",
  "TargetSymbolDensity": 0.15,
  "RequestedAt": "2026-09-27T01:36:25Z"
}
