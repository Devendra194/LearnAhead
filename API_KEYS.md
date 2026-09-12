# 🔑 LearnAhead — Required API Keys & Services

This document lists every external API / service this project uses, where to get the credentials, and which file each key goes into.

---

## Summary Table

| # | Service | Required? | Who uses it | Where to get it |
|---|---------|-----------|-------------|-----------------|
| 1 | **Groq API** | ⚠️ Recommended | Backend AI features | [console.groq.com](https://console.groq.com) |
| 2 | **Firebase Auth** | ✅ For production | Backend + Android + Web | [console.firebase.google.com](https://console.firebase.google.com) |
| 3 | **Firebase Firestore** | ✅ For production | Backend (database) | Same Firebase project |
| 4 | **Firebase Storage** | ✅ For production | Backend (PDF/file uploads) | Same Firebase project |
| 5 | **Firebase Cloud Messaging (FCM)** | Optional | Android push notifications | Same Firebase project |
| 6 | **Local Whisper** | Optional | Audio transcription (self-hosted) | No key needed — runs locally |

---

## 1. 🤖 Groq API
**Used for:** AI transcript cleaning, lecture note generation, quiz generation
**Model used:** `llama-3.3-70b-versatile`
**File:** `backend/.env`

```env
GROQ_API_KEY=gsk_xxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

### How to get it:
1. Go to https://console.groq.com
2. Sign up / Log in
3. Click **API Keys** → **Create API Key**
4. Copy the key and paste it above

> Without this key — the app still runs! AI features fall back to plain transcript display (no notes/quiz generation).

---

## 2–5. 🔥 Firebase (Auth + Firestore + Storage + FCM)

Firebase powers: **user authentication**, **database**, **file storage**, and **push notifications**.

### 2a. Backend Firebase Admin SDK
**File:** `backend/.env`

```env
FIREBASE_PROJECT_ID=your-project-id
FIREBASE_CLIENT_EMAIL=firebase-adminsdk-xxxxx@your-project.iam.gserviceaccount.com
FIREBASE_PRIVATE_KEY="-----BEGIN PRIVATE KEY-----\nMIIE...\n-----END PRIVATE KEY-----\n"
FIREBASE_STORAGE_BUCKET=your-project.firebasestorage.app

# OR use a downloaded JSON credentials file instead:
FIREBASE_CREDENTIALS_FILE=C:/path/to/serviceAccountKey.json
```

### How to get Firebase Admin credentials:
1. Go to https://console.firebase.google.com
2. Create a project (or use existing: `class-ai-3489c`)
3. Go to **Project Settings** → **Service Accounts**
4. Click **Generate new private key** → download the JSON file
5. Either paste values into `.env`, or set `FIREBASE_CREDENTIALS_FILE` to the path of the JSON

---

### 2b. Frontend Firebase Web Config
**File:** `teacher-web/.env`

```env
VITE_FIREBASE_API_KEY=AIzaSy...
VITE_FIREBASE_AUTH_DOMAIN=your-project.firebaseapp.com
VITE_FIREBASE_PROJECT_ID=your-project-id
VITE_FIREBASE_STORAGE_BUCKET=your-project.firebasestorage.app
VITE_FIREBASE_MESSAGING_SENDER_ID=123456789
VITE_FIREBASE_APP_ID=1:123456789:web:abcdef
```

### How to get Firebase Web config:
1. In Firebase Console → **Project Settings** → **General**
2. Scroll to **Your apps** → click the web app (or create one with `</>`)
3. Copy the `firebaseConfig` object values into the `.env` above

---

### 2c. Android Firebase Config
**File:** `classai/app/google-services.json`
*(Already present in repo — update if you change Firebase projects)*

---

## 6. 🎙️ Local Whisper (Self-hosted — No API Key)
**Used for:** Audio/lecture transcription
**File:** `local-whisper/main.py`

No API key needed — Whisper runs entirely on your machine.
The frontend points to it via:

```env
# teacher-web/.env
VITE_WHISPER_URL=http://localhost:8001
```

To run it:
```bash
cd local-whisper
pip install -r requirements.txt
uvicorn main:app --port 8001
```

> Requires a decent GPU or CPU. Downloads the Whisper model (~1.5 GB) on first run.

---

## ✅ Minimum Keys for Local Dev

The backend runs in **in-memory mode** by default (`USE_IN_MEMORY=true`).
Firebase is NOT required locally — just add Groq for AI features:

```env
# backend/.env  (minimum)
GROQ_API_KEY=gsk_...         # get from console.groq.com (free tier available)
USE_IN_MEMORY=true            # already set — no DB needed
```

```env
# teacher-web/.env  (minimum)
VITE_API_URL=http://localhost:8000
VITE_WHISPER_URL=http://localhost:8001
VITE_FIREBASE_API_KEY=...    # required for teacher login
VITE_FIREBASE_AUTH_DOMAIN=...
VITE_FIREBASE_PROJECT_ID=...
VITE_FIREBASE_STORAGE_BUCKET=...
VITE_FIREBASE_MESSAGING_SENDER_ID=...
VITE_FIREBASE_APP_ID=...
```

---

## 🚀 Running the Project

```bash
# Terminal 1 — Backend (http://localhost:8000)
cd backend
python -m uvicorn app.main:app --reload --port 8000

# Terminal 2 — Frontend (http://localhost:5173)
cd teacher-web
npm run dev

# Terminal 3 — Whisper transcription (optional, http://localhost:8001)
cd local-whisper
uvicorn main:app --port 8001
```

Backend Swagger docs: http://localhost:8000/docs
