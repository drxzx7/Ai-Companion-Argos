# Argos Project — Contributors & Roles

This document outlines the core team and engineering contributions across the Argos AI Companion project.

---

## Backend Engineering

### **x_drxzx_x** ([@drxzx7](https://github.com/drxzx7) • [mssdkkl@gmail.com](mailto:mssdkkl@gmail.com))
**Role:** Core Backend Engineer (Java / Spring Boot & AI Systems)

**Key Contributions:**
- **Java Spring Boot Enterprise Backend (`java-backend`)**:
  - Architected and implemented the entire Spring Boot service from ground up, establishing clean separation of concerns across controllers, service layer, AI provider abstractions, and JPA repositories.
- **Multi-Model AI Router & Cascade Engine**:
  - Designed the extensible `AiModelProvider` interface supporting **Gemini 3.8 Flash**, **GPT-6 Astra**, and **Fable 5.1**.
  - Built resilient fallback cascades with request-level model overriding via headers (`X-Argos-Model`) and request bodies.
  - Implemented an offline simulation mode allowing complete local end-to-end functionality without requiring external API keys.
  - Added telemetry tracking for error rates, latencies, and request volumes per AI model.
- **Robot Command Planner & Intent Engine (`CommandPlannerService`)**:
  - Engineered the tag parsing engine for facial expressions (`[TOOL:EXPR:...]`), articulated hand gestures (`[TOOL:HAND:...]`), and timed multi-frame sequence arrays (`[TOOL:EXPRSEQ:[...]]`).
  - Added deterministic intent planning for desktop tool commands, file operations, app launching, and system actions.
- **Android REST API Contract Parity**:
  - Developed full endpoint parity for the Android floating overlay service: `/api/chat`, `/api/thought`, `/api/voice`, `/api/transcribe`, `/api/auth/device`, `/api/models`, and `/api/health`.
  - Built dual-schema response mappers (`reply` + `response`, `detail` + `message`) ensuring seamless drop-in compatibility with Android client deserializers.
- **Dual-Mode Persistence Architecture**:
  - Implemented zero-configuration in-memory H2 database for rapid local development alongside production-ready MySQL profile.
  - Designed entities for `ChatMemory`, `CachedResponse`, `FailedRequest`, and `Device`.
- **Testing & Quality Assurance**:
  - Authored a comprehensive suite of 31 automated unit and integration tests (`ArgosApiIntegrationTest`, `AiModelRouterTest`, `ChatControllerTest`, `ThoughtControllerTest`, `VoiceControllerTest`, `CommandPlannerServiceTest`), achieving clean CI/CD verification.

---

## Client & Mobile Engineering

### **Zrald** ([geraldbustilla@gmail.com](mailto:geraldbustilla@gmail.com))
**Role:** Project Lead / Android & Native Desktop Developer
- Developed the Android application, Three.js floating 3D robot overlay, gesture tracking, and Android Accessibility Service.
- Built Argos Desktop native C++ integration and phone-to-desktop relay.
- Managed release workflows and CI build pipelines.

---

## Python Backend Services

### **Umaima Mughal** ([umaimamughal965@gmail.com](mailto:umaimamughal965@gmail.com))
**Role:** Python Backend Developer
- Built the initial Python FastAPI backend service (`backend/`) with Groq and Cerebras integrations for chat, voice, and proactive thought endpoints.
