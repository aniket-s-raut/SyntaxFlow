# BUSINESS REQUIREMENTS DOCUMENT (BRD) - Version 1.0
**Project Name:** SyntaxFlow
**Domain:** EdTech / Developer Productivity Tools

## 1. Executive Summary
The product is a cloud-native, distributed web platform integrating a client-side touch-typing engine with an automated Spaced Repetition System (FSRS algorithm). Designed for software developers and bilingual professionals, it targets developer-symbol typing speed, active professional English syntax, and real-time speech pronunciation. 

## 2. Business Drivers & Objectives
- **Personal Productivity:** Eliminate the cognitive bottleneck of typing under 40 WPM and hesitation during workplace speech.
- **Enterprise Portfolio Asset:** Demonstrate senior-level .NET/Azure SDLC ownership, cloud cost-optimization ($0 stack), Clean Architecture, distributed microservices, and GenAI integration for Senior Fintech/Enterprise engineering roles.
- **Commercial Scalability:** Establish a foundational MVP capable of future SaaS monetization.

## 3. Target User Profile
- **Primary:** Software developers stuck at 30-40 WPM due to poor symbol/number row reach.
- **Secondary:** ESL software engineers looking to bridge the gap between reading comprehension, active sentence generation, and pronunciation.

## 4. Core Scope (MVP Features)
- **AI Text Generation (GenAI):** Prompt-based generation of realistic .NET/Azure workplace scenarios (e.g., explaining CI/CD pipelines, DevOps blockers) and day-to-day conversational English.
- **Typing Engine:** Zero-latency client-side engine (Blazor WebAssembly) tracking WPM, accuracy, and hesitation heatmaps.
- **Strict Error Handling:** Enforcement mode requiring full word re-entry on error to overwrite bad muscle memory.
- **E-Reader & Speech Recognition:** PDF/ePub upload capability. Uses Web Speech API to listen to the user read aloud, pausing and offering correct audio pronunciations upon detecting errors.
- **Contextual FSRS Flashcards:** Users highlight unknown words in a sentence; the AI generates the contextual meaning, injecting it directly into a Free Spaced Repetition Scheduler (FSRS) database for daily review.

## 5. Architectural & Technical Requirements
- **Architecture:** Clean Architecture implementing the CQRS pattern (via MediatR). 
- **Distributed Systems:** Event-driven communication using Azure Service Bus to decouple the AI generation engine (Azure Functions) from the main API.
- **Parallel Processing:** Heavy utilization of C# `async/await` and Task Parallel Library (TPL) for concurrent AI requests and telemetry processing.
- **Security:** Single Sign-On (SSO) integrated via Microsoft Entra ID (OIDC/OAuth2).
- **Cloud & DevOps:** Hosted entirely on Azure Free Tier (Static Web Apps, App Service, Azure SQL). Automated CI/CD pipelines utilizing Azure DevOps or GitHub Actions.
- **Cost Constraint:** Zero operational cost. Must utilize perpetual free tiers.

## 6. Measurable Success Metrics (KPIs)
- **System Performance:** AI text generation latency under 3 seconds; Typing engine latency at 0ms (client-side execution).
- **User Outcome:** Achieve 60 WPM with 98% accuracy on C# symbol-heavy text within 60 days.

## 7. Out of Scope (MVP)
- Mobile application and touch-screen typing.
- Real-time multiplayer racing.
- Payment gateway integration.
