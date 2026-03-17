# NexTask — Offline-First Productivity System

> Single-file deployable app. Google Auth + Firebase Firestore. Works fully offline.

![NexTask Screenshot](https://via.placeholder.com/800x450/0a0a0f/7c5cfc?text=NexTask)

---

## ⚡ Deploy in 5 minutes

### Step 1 — Firebase Setup

1. Go to [console.firebase.google.com](https://console.firebase.google.com)
2. Create a new project (e.g. `nextask-prod`)
3. **Add a Web App** → copy the `firebaseConfig` object

### Step 2 — Paste Config

Open `index.html`, find `FIREBASE_CONFIG` near the top of the `<script>` and replace:

```js
const FIREBASE_CONFIG = {
  apiKey:            "YOUR_API_KEY",           // ← paste here
  authDomain:        "YOUR_PROJECT.firebaseapp.com",
  projectId:         "YOUR_PROJECT_ID",
  storageBucket:     "YOUR_PROJECT.appspot.com",
  messagingSenderId: "YOUR_SENDER_ID",
  appId:             "YOUR_APP_ID"
};
```

### Step 3 — Enable Google Authentication

Firebase Console → **Authentication** → **Sign-in method** → Enable **Google** → Save

Add your deployment domain to **Authorized domains** (Vercel auto-adds it).

### Step 4 — Enable Firestore

Firebase Console → **Firestore Database** → **Create database** → Production mode

**Paste these Security Rules** (Firestore → Rules tab):

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /users/{userId}/{document=**} {
      allow read, write: if request.auth != null
                         && request.auth.uid == userId;
    }
  }
}
```

### Step 5 — Deploy

**Option A — Vercel (recommended, free)**
```bash
# Push to GitHub, then:
# vercel.com → Import → select repo → Deploy
# ✅ Done. Your app is live with HTTPS.
```

**Option B — GitHub Pages (free)**
```bash
git init
git add index.html README.md
git commit -m "initial commit"
git remote add origin https://github.com/USERNAME/nextask.git
git push -u origin main
# GitHub → Settings → Pages → Source: main / root
# Live at: https://USERNAME.github.io/nextask
```

**Option C — Netlify drag-and-drop (free)**
1. Go to [app.netlify.com/drop](https://app.netlify.com/drop)
2. Drag `index.html` onto the page
3. Instantly live ✅

**Option D — Any static host**
Just upload `index.html` — it's a single file, zero build step.

---

## 🔌 Offline-First Architecture

| Scenario | Behaviour |
|---|---|
| **Online** | Reads/writes go to Firestore, synced across all devices in real-time |
| **Offline** | Firestore SDK stores everything in IndexedDB — reads/writes work instantly |
| **Back online** | All pending writes automatically sync to Firestore |
| **No Firebase config** | Demo mode activates — uses localStorage, fully functional |

### How it works

Firebase Firestore v10 with **persistent local cache** (IndexedDB) is enabled by default:

```js
firebase.firestore().enablePersistence({ synchronizeTabs: true })
```

This means:
- ✅ All CRUD operations work offline with zero latency
- ✅ Data survives browser refresh
- ✅ Multi-tab sync supported
- ✅ Automatic conflict resolution when coming back online

---

## 📱 Features

| Feature | Description |
|---|---|
| **Task Manager** | Create/edit/delete tasks with priority, deadline, tags, status, estimated hours |
| **Activity Tracker** | Animated timer, session logging, notes, donut chart summary |
| **Timetable** | Day view with schedule blocks, current-time indicator, add/delete blocks |
| **Analytics** | Bar charts, est vs actual hours, per-tag breakdown |
| **Architecture** | System design documentation |
| **DB Schema** | All 4 tables documented |
| **Sync Protocol** | MQTT topics, REST endpoints, event flow |
| **Edge Devices** | ESP32/RPi compatibility guide with firmware outline |
| **Roadmap** | 5-phase development plan |

---

## 🗄 Firestore Data Model

```
/users/{uid}/
  tasks/
    {taskId}  → { title, description, priority, deadline, tags[], status, estHours, loggedHours, createdAt, updatedAt }
  activities/
    {actId}   → { taskId, taskName, startTime, endTime, durationMin, notes, createdAt }
  schedules/
    {schedId} → { title, start, end, color, taskId, createdAt }
```

---

## 🔒 Security

- **Authentication**: Google OAuth via Firebase Auth
- **Database**: Firestore security rules enforce per-user isolation
- **Transport**: All Firebase traffic uses TLS 1.3
- **Offline data**: Stored in browser IndexedDB (sandboxed per origin)
- **No API keys exposed**: Firebase client keys are safe to be public (they're rate-limited by security rules)

---

## 📡 MQTT Topics (IoT/Edge)

```
newtask/{device_id}/tasks/sync       ← receive task updates
newtask/{device_id}/activity/start   ← publish session start
newtask/{device_id}/activity/stop    ← publish session stop
newtask/broadcast/push               ← server broadcasts to all
newtask/edge/esp32/heartbeat         ← device keepalive
```

---

## 🗺 Roadmap

| Phase | Status | Description |
|---|---|---|
| 1 — MVP | ✅ Done | Core tasks + local storage |
| 2 — Auth + Sync | 🔄 Active | Google Auth + Firebase offline sync (this file) |
| 3 — Mobile | 📋 Next | React Native app + MQTT |
| 4 — Edge | 📋 Future | ESP32 firmware + Raspberry Pi broker |
| 5 — AI | 📋 Full | AI insights + encrypted backup |

---

## 📄 License

MIT — use freely for personal or commercial projects.
