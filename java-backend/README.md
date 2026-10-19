# Argos Java Backend (Spring Boot)

High-performance, enterprise-grade Spring Boot backend service for the **Argos AI Companion**. Designed and developed by **x_drxzx_x** (`mssdkkl@gmail.com`) as the core Java backend engineer.

---

## Architecture Overview

```
                      ┌─────────────────────────────────────────┐
                      │          Argos Android Client           │
                      │   (FloatingRobotService / WebViews)     │
                      └────────────────────┬────────────────────┘
                                           │ HTTP REST / JSON
                                           ▼
                      ┌─────────────────────────────────────────┐
                      │           Argos Java Backend            │
                      │         (Spring Boot 4 / Java 17)       │
                      ├─────────────────────────────────────────┤
                      │  REST Controllers:                      │
                      │   • /api/chat       • /api/thought      │
                      │   • /api/voice      • /api/transcribe   │
                      │   • /api/models     • /api/health       │
                      │   • /api/auth/device                    │
                      ├─────────────────────────────────────────┤
                      │  Multi-Model AI Router:                 │
                      │   ├─ Gemini 3.8 Flash (Ultra-low latency│
                      │   ├─ GPT-6 Astra (Complex reasoning)    │
                      │   ├─ Fable 5.1 (Emotional expression)   │
                      │   └─ Local Cascade / Simulation Mode    │
                      ├─────────────────────────────────────────┤
                      │  Command Planner & Intent Engine:       │
                      │   • Facial Expressions: [TOOL:EXPR:...] │
                      │   • Gestures: [TOOL:HAND:...]           │
                      │   • Expression Sequences: [TOOL:EXPRSEQ]│
                      │   • Tool Actions & System Automation    │
                      ├─────────────────────────────────────────┤
                      │  Dual-Mode Persistence Layer:           │
                      │   • In-Memory H2 (Zero-config / default)│
                      │   • Production MySQL Profile            │
                      │   • JPA Entities: ChatMemory, Cached-   │
                      │     Response, FailedRequest, Device     │
                      └─────────────────────────────────────────┘
```

---

## Key Backend Features & Contributions

### 1. Multi-Model AI Routing & Resilient Cascade (`com.argos.argos_backend.ai`)
- **Abstract Provider Model**: Clean polymorphic architecture (`AiModelProvider`, `AbstractAiProvider`) decoupling model vendor APIs from business logic.
- **Dynamic Model Selection**: Support for **Gemini 3.8 Flash**, **GPT-6 Astra**, and **Fable 5.1**, switchable per-request via body payload or `X-Argos-Model` header.
- **Fail-Safe Cascade & Offline Simulation**: Automatically cascades across providers if an external API fails, with an integrated zero-dependency offline simulation mode for local development and testing without API keys.
- **Telemetry & Metrics**: Real-time tracking of request counts, error counts, latency averages, and health status per model via `/api/models`.

### 2. Command Planner & Expression Parsing (`CommandPlannerService`)
- Parses robot behavioral tags embedded in AI responses:
  - `[TOOL:EXPR:HAPPY]`, `[TOOL:EXPRESSION:ANGRY]`
  - `[TOOL:HAND:WAVE]`, `[TOOL:HAND:POINT]`
  - Timed expression sequences: `[TOOL:EXPRSEQ:[{"expr":"CURIOUS","delay":300},{"expr":"SMUG","delay":800}]]`
- Desktop & system automation planning: translates conversational intent into actionable tool execution payloads for desktop automation, file tools, and scheduling.

### 3. Full Android API Parity & Dual Response Contract
- Fully conforms to the Android client contract expected by `FloatingRobotService`:
  - Returns dual fields (`reply` and `response`) for conversational output.
  - Returns dual error fields (`detail` and `message`) for seamless Android exception handling.
- Comprehensive REST endpoints:
  - `POST /api/chat` — Multi-turn conversation with screen context and persistent memory.
  - `POST /api/thought` — Proactive thought bubbles triggered by screen awareness.
  - `POST /api/voice` — End-to-end voice pipeline (multipart audio upload -> transcription -> conversational response).
  - `POST /api/transcribe` — Dedicated speech-to-text audio transcription.
  - `POST /api/auth/device` — Device fingerprinting, registration, and token authentication.
  - `GET /api/models` — Available AI models, capabilities, and telemetry.
  - `GET /api/health` — Subsystem health checks and service uptime.

### 4. Dual-Mode Database Architecture
- **H2 In-Memory (Default)**: Ready out of the box with zero external dependencies.
- **MySQL (Production)**: Enable with `--spring.profiles.active=mysql`.
- **Entities**:
  - `ChatMemory`: Session-based conversation logs and memory persistence.
  - `CachedResponse`: Fast deduplication and caching for frequent queries.
  - `FailedRequest`: Resilient dead-letter logging for offline retries.
  - `Device`: Secure device registration, authorization, and hardware tokens.

### 5. Automated Test Suite
- Comprehensive suite of 31 unit, controller, and integration tests passing cleanly:
  - `AiModelRouterTest`: Model resolution, simulated fallback, telemetry metrics.
  - `ChatControllerTest`: Dual contract payloads, model header overrides, validation.
  - `ThoughtControllerTest` & `VoiceControllerTest`: Screen context shapes, multipart voice ingestion.
  - `CommandPlannerServiceTest`: Tag parsing, expression sequences, intent mapping.
  - `ArgosApiIntegrationTest`: End-to-end HTTP integration tests.

---

## Quick Start

### Prerequisites
- JDK 17 or higher
- Maven 3.8+ (or use the included `./mvnw`)

### Build & Run

```bash
cd java-backend

# Run with Maven Wrapper
./mvnw clean spring-boot:run
```

On Windows PowerShell:
```powershell
cd java-backend
.\mvnw.cmd clean spring-boot:run
```

The service will start on port `8080`:
- Health Check: `http://localhost:8080/api/health`
- Model Discovery: `http://localhost:8080/api/models`

### Run Tests

```bash
./mvnw test
```

---

## Configuration Reference

Key settings in `src/main/resources/application.yaml`:

```yaml
server:
  port: 8080

argos:
  ai:
    default-model: gemini-3.8-flash
    cascade-enabled: true
    simulation-mode: true   # Set to false when using live API keys
    gemini:
      api-key: ${GEMINI_API_KEY:}
    gpt6:
      api-key: ${OPENAI_API_KEY:}
    fable:
      api-key: ${FABLE_API_KEY:}
```

---

## Author & Contributions
- **Backend Architecture & Java Development**: **x_drxzx_x** ([mssdkkl@gmail.com](mailto:mssdkkl@gmail.com))
