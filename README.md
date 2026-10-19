# Argos — AI Companion for Android

Argos is an AI-powered companion app that floats on top of your screen as an interactive 3D robot. It listens to you, talks back, reacts with facial expressions and hand gestures, and can act on your device through Android's accessibility APIs — always triggered by an explicit user command.

## Features

- **Floating 3D Robot Overlay** — A Three.js robot rendered in a WebView that floats over any app
- **Voice Conversations** — Double-tap the robot to record, and Argos transcribes, thinks, and speaks the reply aloud
- **Text Chat** — Single-tap to open the chat bubble
- **Expressive Animations** — Facial expressions, articulated neon hands, and word-timed gestures driven by the AI's reply
- **Nanotech Movement** — Argos dissolves into particles, travels, and rebuilds itself in a new spot
- **Screen Awareness** — Reads on-screen text via `AccessibilityService` to give the AI context
- **Tool Execution** — The AI can dial numbers, send SMS, set alarms/timers, open apps, search, and manage notes
- **Persistent Memory** — Conversation history kept across chat sessions
- **Hand Tracking** — Front-camera hand tracking; pinch to grab and drag the robot

## Requirements

- Android 7.0 (API 24) or higher
- Accessibility Service enabled
- Overlay / display permission
- Microphone permission
- A running Argos backend (included in [`backend/`](backend/)) — see [Backend Setup](#backend-setup)

## Getting Started

### Prerequisites

- Android Studio (latest stable)
- Android SDK 36
- Android NDK 27.0.12077973
- JDK 17
- Gradle 8.14.2+ (the wrapper is committed — use `./gradlew`)

### Build

1. Clone the repository:
   ```bash
   git clone https://github.com/Zrald1/ibmbobproject.git
   cd ibmbobproject
   ```

2. Open the `android/` folder in Android Studio.

3. Point the app at your backend:
   - Edit `android/app/src/main/res/values/strings.xml` and set `backend_url` to your server.
   - Add the same host to `android/app/src/main/res/xml/network_security_config.xml`.

4. Build:
   ```bash
   cd android
   ./gradlew assembleRelease
   ```

   Release signing credentials are **not** committed. Provide them via environment
   variables (`ANDROID_KEYSTORE_PATH`, `ANDROID_KEYSTORE_PASSWORD`,
   `ANDROID_KEY_ALIAS`, `ANDROID_KEY_ALIAS_PASSWORD`) or in the git-ignored
   `android/local.properties`. Without them the release build falls back to the
   debug signing key.

## Backend Setup

Argos provides two backend implementations:

### 1. Java Spring Boot Backend (`java-backend/`) — Enterprise Multi-Model Service
Architected and developed by **x_drxzx_x** (`mssdkkl@gmail.com`). Features a multi-model AI router (Gemini 3.8 Flash, GPT-6 Astra, Fable 5.1), automated cascade fallback, zero-dependency offline simulation mode, robot command and expression planner, dual-mode H2/MySQL persistence, and 31 passing unit & integration tests.

See [`java-backend/README.md`](java-backend/README.md) for full documentation.

Quick start:
```bash
cd java-backend
./mvnw clean spring-boot:run     # On Windows: .\mvnw.cmd clean spring-boot:run
```

### 2. Python FastAPI Backend (`backend/`)
The original lightweight Python backend using Groq, Cerebras, and OpenAI-compatible providers. See [`backend/README.md`](backend/README.md).

Quick start:
```bash
cd backend
python -m venv venv && source venv/bin/activate
pip install -r requirements.txt
cp .env.example .env      # then fill in your API keys
uvicorn app.main:app --host 0.0.0.0 --port 8000
```

Endpoints used by the Android app:

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/api/chat` | POST | Send a chat message to the AI with screen context |
| `/api/thought` | POST | Generate a proactive thought bubble |
| `/api/voice` | POST | Voice pipeline (multipart audio -> transcribe + chat) |
| `/api/transcribe` | POST | Audio speech-to-text transcription |
| `/api/models` | GET | List available AI models and telemetry |
| `/api/health` | GET | System and subsystem health status |
| `/api/auth/device` | POST | Device registration and token authentication |

## Configuration

The app reads the backend URL from string resources. To change it:

1. **strings.xml** — update the `backend_url` value
2. **network_security_config.xml** — update the domain entry
3. **AndroidManifest.xml** — update the `argos.backend_url` metadata value

## Project Structure

```
ibmbobproject/
├── android/                                         # Android companion application
│   ├── app/
│   │   ├── src/main/
│   │   │   ├── assets/argos_robot.html              # Three.js 3D robot scene
│   │   │   ├── java/com/example/argos/              # Floating overlay, accessibility
│   │   │   └── cpp/                                 # JNI native code
│   │   └── build.gradle
│   └── SETUP_GUIDE.md
├── java-backend/                                    # Spring Boot 4 / Java 17 backend
│   ├── src/main/java/com/argos/argos_backend/
│   │   ├── ai/                                      # Multi-model AI router & providers
│   │   ├── controller/                              # Chat, voice, thought, health REST APIs
│   │   ├── service/                                 # Command planner & business logic
│   │   └── entity/                                  # Dual-mode DB models (H2 / MySQL)
│   ├── src/test/java/                               # 31 unit & integration tests
│   ├── pom.xml
│   └── README.md
├── backend/                                         # FastAPI Python backend
│   ├── app/
│   │   ├── main.py
│   │   └── routes/                                  # Chat, voice, thought
│   └── README.md
├── argos-desktop/ & cpp/                            # Native desktop tool executor & relay
├── scripts/                                         # Signing and automation helpers
├── .github/workflows/                               # CI + tagged release APK
├── CONTRIBUTORS.md                                  # Team roles and contributions
├── LICENSE
└── README.md
```

## Team & Contributions

This project was built collaboratively by:

- **x_drxzx_x** ([@drxzx7](https://github.com/drxzx7) • [mssdkkl@gmail.com](mailto:mssdkkl@gmail.com)) — **Backend Developer (Java & AI Architecture)**
  - Engineered the entire Java Spring Boot enterprise service (`java-backend/`).
  - Created the Multi-Model AI Router supporting Gemini 3.8 Flash, GPT-6 Astra, and Fable 5.1 with resilient cascades and zero-config offline simulation.
  - Implemented the Command Planner & Intent Engine (`CommandPlannerService`) parsing robot facial expressions, hand gestures, and timed expression sequence arrays (`[TOOL:EXPRSEQ]`).
  - Achieved full REST endpoint parity with Android dual-response contracts (`reply`/`response`).
  - Designed dual-mode persistence (in-memory H2 + production MySQL profile) and built a comprehensive 31-test automated suite.
  - See detailed breakdown in [CONTRIBUTORS.md](CONTRIBUTORS.md) and [`java-backend/README.md`](java-backend/README.md).

- **Zrald** ([geraldbustilla@gmail.com](mailto:geraldbustilla@gmail.com)) — **Project Lead / Android & Native Desktop Developer**
  - Android application, Three.js 3D robot floating overlay, accessibility service, gestures, and native C++ desktop relay.

- **Umaima Mughal** ([umaimamughal965@gmail.com](mailto:umaimamughal965@gmail.com)) — **Python Backend Developer**
  - Initial Python FastAPI backend with Groq & Cerebras integration.

## Releases

Pushing a version tag builds and publishes a signed release APK:

```bash
git tag v1.0.0 && git push origin v1.0.0
```

The workflow decodes the keystore from repository secrets, builds the APK,
verifies it is release-signed, and attaches it to a GitHub Release.

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) and [CONTRIBUTORS.md](CONTRIBUTORS.md) for details.

## License

This project is licensed under the MIT License — see [LICENSE](LICENSE) for details.

## Acknowledgments

- Android Accessibility Service documentation
- [three.js](https://threejs.org/) for the robot renderer
- [FastAPI](https://fastapi.tiangolo.com/) for the backend
