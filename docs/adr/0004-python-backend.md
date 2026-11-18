# Python for the IVR; TypeScript only for repo agent tooling

The IVR is written in Python (asyncio, FastAPI, pytest, managed with uv). Python was chosen over TypeScript because the speech stack is Python-native: faster-whisper, Vosk and Piper run in-process instead of as separate services, and Asterisk audio arrives over AudioSocket/ARI into an asyncio server. The existing TypeScript in the repo (`.sandcastle/`, `package.json`, `tsconfig.json`) is agent-workflow tooling, not product code, and stays as is. The Admin Console is React in plain JavaScript (Vite), served by the same FastAPI app.
