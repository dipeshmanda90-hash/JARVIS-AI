# JARVIS AI — Android + Cloud AI starter

A starter Android chat app with a small FastAPI gateway. It lets the user choose Gemini or OpenAI ChatGPT models. API keys are stored on the backend, not in the Android APK.

## Included
- Android app built with Kotlin + Jetpack Compose
- Gemini / OpenAI model selector
- Chat screen and configurable backend URL
- FastAPI cloud gateway with `/health` and `/api/chat`
- Environment-variable API key configuration

## Requirements
- Android Studio (current stable) with Android SDK 35
- JDK 17
- Python 3.10+
- An API key for each provider you want to use

## 1. Start the backend locally

```bash
cd backend
python -m venv .venv
# Windows: .venv\Scripts\activate
# macOS/Linux: source .venv/bin/activate
pip install -r requirements.txt
cp .env.example .env
```

Edit `backend/.env` and add your own provider API keys. Then run:

```bash
uvicorn main:app --host 0.0.0.0 --port 8000 --reload
```

Check `http://127.0.0.1:8000/health`.

## 2. Run Android app

1. Open this project folder in Android Studio.
2. Let Gradle sync finish.
3. Start an Android emulator and press Run.

The default backend URL is `http://10.0.2.2:8000` (Android emulator -> host computer).

For a physical Android phone:
- Connect phone and computer to the same Wi-Fi.
- Find the computer's LAN IP (for example `192.168.1.10`).
- Enter `http://192.168.1.10:8000` in the app's Backend URL field.
- Allow port 8000 through your computer firewall.

## 3. Deploy for cloud use

Deploy the FastAPI backend to a trusted HTTPS hosting provider. Set `OPENAI_API_KEY` and/or `GEMINI_API_KEY` as server-side environment secrets. Change the app's Backend URL to the HTTPS backend URL.

Do not use a public unauthenticated deployment as-is. Before production, add:
- User authentication and per-user authorization
- Rate limiting and usage quotas
- API request logging with sensitive data redaction
- HTTPS only; disable cleartext traffic
- Provider cost limits and abuse protection
- Secure secret storage and key rotation

## Notes
- Provider API access and usage may incur charges; check each provider's current pricing and account requirements.
- The starter does not yet include voice wake-word detection, background agents, app generation, file tools, or device automation. Those are planned modules, not implemented features.
- Android's `usesCleartextTraffic=true` is for local development only. Remove it and use HTTPS for production.

## Project structure

```text
JARVIS_AI/
├── app/
│   ├── build.gradle.kts
│   └── src/main/
│       ├── AndroidManifest.xml
│       └── java/com/jarvisai/app/MainActivity.kt
├── backend/
│   ├── main.py
│   ├── requirements.txt
│   └── .env.example
├── build.gradle.kts
├── settings.gradle.kts
└── README.md
```


## Build APK using only an Android phone (GitHub cloud build)

You do not need Android Studio on your phone for this build route.

1. Create/sign in to a GitHub account in your mobile browser.
2. Create a new repository named `JARVIS-AI`.
3. Upload the contents of this project ZIP to the repository (including the hidden `.github` folder).
4. Open the repository's **Actions** tab.
5. Select **Build JARVIS AI APK** and tap **Run workflow**. You can also push to `main` to trigger a build.
6. When the run completes, open it and download the `JARVIS-AI-debug-APK` artifact.
7. Extract the artifact ZIP and install `app-debug.apk` on your Android phone. Android may ask you to allow installation from that browser/file manager.

This produces a debug APK for testing, not a Play Store release.

### Important backend note for phone-only testing

The app's default `http://10.0.2.2:8000` address is for an Android emulator and will not work on a physical phone. Deploy the FastAPI backend to an HTTPS host, set provider API keys as server-side secrets there, then paste its HTTPS URL into the app's Backend URL field. Do not put API keys in the Android app or upload a real `.env` file to GitHub.

## Voice Core — first implementation

The Android app now includes:
- Microphone permission request and push-to-talk speech recognition
- Hindi (`hi-IN`), Nepali (`ne-NP`) and English (`en-US`) language choices
- Recognized speech is placed in the input box for review before sending
- Optional spoken AI replies using Android TextToSpeech
- Voice and text both use the existing cloud AI backend

Notes:
- Speech recognition availability and offline support depend on the speech service installed on the phone. Some services may require internet.
- Text-to-speech voice quality and language availability depend on Android's installed TTS engine and voice packs.
- This is push-to-talk, not an always-listening wake-word implementation yet.

## Next: Hey JARVIS wake word

The next module will evaluate an on-device wake-word engine such as Picovoice Porcupine. It requires an AccessKey and a custom keyword model for a custom phrase. Android versions impose microphone and foreground-service restrictions, especially when starting microphone services from the background. We will implement wake-word mode only after the user explicitly enables it, shows a persistent notification while active, and grants microphone permission. Do not promise that Android will allow unrestricted always-on listening on every phone.
