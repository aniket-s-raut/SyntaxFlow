# AGILE SPRINT BACKLOG & TASK BREAKDOWN - Version 1.0
**Project Name:** SyntaxFlow
**Methodology:** Agile Scrum (2-Week Sprints)

## Sprint 1: Foundation & Infrastructure (The Skeleton)
**Goal:** Establish the Clean Architecture boundaries, database schema, and deployment pipelines.

- [ ] **Task 1.1:** Setup `.NET 8/9` solution using Clean Architecture (Domain, Application, Infrastructure, WebApi, Blazor Wasm).
- [ ] **Task 1.2:** Configure **Microsoft Entra ID** (Free Tier) for Single Sign-On (SSO) and implement JWT validation in the Web API.
- [ ] **Task 1.3:** Setup **Entity Framework Core** (Code-First). Create the `Users`, `TypingSessions`, `Flashcards`, and `ReadingDocuments` entities.
- [ ] **Task 1.4:** Generate initial EF Core Migrations and deploy the schema to **Azure SQL Database** (Free Tier).
- [ ] **Task 1.5:** Configure CI/CD: Create a GitHub Actions workflow to auto-build and deploy the API to **Azure App Service** and the Blazor frontend to **Azure Static Web Apps**.

## Sprint 2: Zero-Latency Typing Engine (The Core Loop)
**Goal:** Build the Blazor WASM typing interface with custom rendering to guarantee 0ms latency.

- [ ] **Task 2.1:** Scaffold the Blazor WASM frontend with **Tailwind CSS** (Dark Mode developer theme).
- [ ] **Task 2.2:** Build `TypeTarget.razor` component. Implement `@onkeydown` interceptors and override `ShouldRender()` to prevent full-DOM diffing lag on every keystroke.
- [ ] **Task 2.3:** Implement the **Strict Error Mode** logic (forcing word re-entry on mistype).
- [ ] **Task 2.4:** Build telemetry logic: Calculate WPM, Accuracy, and Keystroke Hesitation mapping in real-time.
- [ ] **Task 2.5:** Create the MediatR `Command` to POST completed typing session data to the backend API.

## Sprint 3: AI Microservices & Event-Driven Architecture (The Cloud)
**Goal:** Decouple the GenAI content generation from the main API using Azure Service Bus and Azure Functions.

- [ ] **Task 3.1:** Provision an **Azure Service Bus** Namespace and Topic (`ai-generation-requests`) on the Free Tier.
- [ ] **Task 3.2:** Create an **Azure Function** (Microservice) triggered by Service Bus messages.
- [ ] **Task 3.3:** Integrate the free LLM API (Gemini/Groq) into the Azure Function using Microsoft's **Semantic Kernel** SDK.
- [ ] **Task 3.4:** Write the LLM Prompt logic to generate C#/.NET contextual scenarios based on the user's English level.
- [ ] **Task 3.5:** Update Blazor frontend to fetch AI-generated text for the Typing Arena.

## Sprint 4: E-Reader, Speech Recognition & FSRS (The Differentiation)
**Goal:** Complete the ESL/learning loop with Web Speech API and FSRS mathematics.

- [ ] **Task 4.1:** Build `DocumentViewer.razor`. Implement file upload to **Azure Blob Storage** and parse PDF/ePub text to the browser.
- [ ] **Task 4.2:** Write `speech-to-text.js` wrapper utilizing the browser's native **Web Speech API**.
- [ ] **Task 4.3:** Connect JS Interop to `SpeechTracker.razor` to pause the reader when pronunciation deviates from the document text.
- [ ] **Task 4.4:** Integrate the C# FSRS algorithm (e.g., using `FSRS.Core` or a custom implementation of FSRS v5) into the Domain layer.
- [ ] **Task 4.5:** Build `ContextExtractor.razor`. Allow users to highlight text, send it to the AI for contextual meaning, and save it as an FSRS flashcard.
- [ ] **Task 4.6:** Build the Dashboard UI to review due FSRS flashcards and visualize typing heatmaps.
