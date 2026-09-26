# UI/UX & COMPONENT ARCHITECTURE - Version 1.0
**Project Name:** SyntaxFlow

## 1. UI/UX Design System & Layout
The frontend is built using **Blazor WebAssembly** to guarantee zero-latency keystroke tracking. The visual design prioritizes a distraction-free, "IDE-like" developer aesthetic.

### 1.1 Global Design Guidelines
- **CSS Framework:** Tailwind CSS (for highly customizable, utility-first styling without the bloat of heavy component libraries).
- **Theme:** Default Dark Mode (background: `#1E1E1E`, text: `#D4D4D4`, primary accent: `#569CD6` for Azure/C# aesthetics).
- **Typography:** 
  - Monospace (Fira Code or JetBrains Mono) for the Typing Engine and code blocks.
  - Sans-Serif (Inter) for standard application UI and the E-Reader module.

### 1.2 Core Screen Flows
1. **Dashboard:** Summarizes daily FSRS due count, WPM progression chart, and recent telemetry.
2. **Typing Arena:** A minimalist, center-aligned text display. No animations or moving elements to prevent cognitive overload.
3. **E-Reader Module:** A dual-pane view. Left pane: PDF/ePub text. Right pane: Contextual FSRS flashcard generator.
4. **Review Deck:** A Tinder-style swipe or hotkey-driven interface (Keys 1-4) for FSRS flashcard grading.

## 2. Blazor Component Tree & State Management
To prevent unnecessary DOM re-renders (which kill typing performance), the application follows a strict Parent-to-Child component hierarchy utilizing scoped state containers.

### 2.1 State Management
- **Pattern:** Flux-style state management (using `Fluxor` or custom Scoped `StateContainer` classes injected via Dependency Injection).
- **Global States:** `UserState`, `FSRSQueueState`.
- **Transient States:** `TypingSessionState` (destroyed immediately after session submission).

### 2.2 Component Hierarchy
```text
App.razor
├── MainLayout.razor
│   ├── SidebarNav.razor
│   └── Body (Router)
│       ├── Dashboard.razor
│       ├── TypingArena.razor
│       │   ├── SessionHeader.razor (WPM/Accuracy live counter)
│       │   ├── TypeTarget.razor (The text being typed)
│       │   └── HeatmapVisualizer.razor (Post-session rendering)
│       ├── DocumentReader.razor
│       │   ├── DocumentViewer.razor (Renders PDF/Text)
│       │   ├── SpeechTracker.razor (Handles microphone UI)
│       │   └── ContextExtractor.razor (Triggers GenAI contextual meaning)
│       └── FlashcardReview.razor

3. High-Performance Component Details
3.1 TypeTarget.razor (The Typing Engine)
Challenge: Standard Blazor two-way binding triggers a full component re-render on every keystroke, which causes latency.
Solution:

Implement @onkeydown and @oninput event handlers that update a character-level array in memory.

Manually override the ShouldRender() lifecycle method. The component will ONLY re-render the specific <span> of the current word being typed, bypassing the Blazor diffing engine for the rest of the paragraph.

Use CSS classes (.correct, .error, .active) toggled via C# to change text color instantaneously.

3.2 SpeechTracker.razor (E-Reader Pronunciation)
Challenge: Blazor WASM cannot natively access the device microphone or browser speech engines.
Solution:

JS Interop: Utilize the browser's native Web Speech API (window.SpeechRecognition).

Create a speech-to-text.js wrapper in wwwroot that triggers the microphone.

Bind JavaScript callbacks to a [JSInvokable] C# method in the Blazor component.

When the JS engine flags a mispronounced word (by comparing the spoken transcript to the document text), it passes the event back to Blazor to pause the UI and highlight the word.

3.3 ContextExtractor.razor
Interaction: User highlights a sentence in DocumentViewer.razor and double-clicks a specific word.

Action: A floating context menu appears. Clicking "Generate Flashcard" triggers an asynchronous HTTP POST request to the Azure backend, placing the request on the Azure Service Bus, and immediately showing a non-blocking "Generating..." toast notification.
