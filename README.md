# 🤟 SignBridge

**Real-time bidirectional sign language communication between deaf/mute and hearing individuals — entirely in the browser, no backend required.**

![SignBridge Phase 3](https://img.shields.io/badge/Phase-3%20%E2%80%94%20Production%20Ready-blueviolet?style=flat-square)
![No Backend](https://img.shields.io/badge/Backend-None%20Required-00D4B1?style=flat-square)
![License](https://img.shields.io/badge/License-MIT-green?style=flat-square)
![MediaPipe](https://img.shields.io/badge/MediaPipe-Hands%20v0.4-orange?style=flat-square)

---

## 🚀 Quick Start

```bash
git clone https://github.com/your-username/signbridge.git
cd signbridge

# Open directly (no server needed for basic use)
open phases/phase3.html

# Or serve locally for reliable camera access
npx serve .
# → visit http://localhost:3000
```

> **Camera Note:** Modern browsers require `https://` or `localhost` for camera access. Use `npx serve .` locally, or deploy to GitHub Pages / Netlify / Vercel for free HTTPS hosting.

---

## 📋 What Is SignBridge?

SignBridge is a single-page web app that enables **real-time, bidirectional communication** between:

- **Deaf/mute users** — who sign using ASL in front of their camera. The app detects hand landmarks, classifies signs, and translates them into text/speech.
- **Hearing users** — who type or speak. The app converts their words into animated sign language playback word-by-word.

Everything runs in the browser using:
- **MediaPipe Hands** — 21-point 3D hand landmark detection
- **Web Speech API** — speech-to-text and text-to-speech  
- **Canvas API** — hand skeleton overlay and avatar animation
- **Vanilla JavaScript** — no frameworks, no build tools

---

## 📁 Project Structure

```
signbridge/
├── index.html              ← Landing page / project overview
├── README.md               ← This file
├── LICENSE                 ← MIT License
├── CHANGELOG.md            ← Phase-by-phase changelog
├── .github/
│   └── ISSUE_TEMPLATE.md   ← Bug report / feature request template
├── docs/
│   ├── ARCHITECTURE.md     ← Technical architecture overview
│   ├── GESTURE_CLASSIFIER.md ← How the ASL classifier works
│   └── DEPLOYMENT.md       ← Deployment guide
└── phases/
    ├── phase1.html         ← Phase 1: Core prototype
    ├── phase2.html         ← Phase 2: Full feature set
    └── phase3.html         ← Phase 3: Production ready ← START HERE
```

---

## 🗺️ Phases

### Phase 1 — Foundation (`phases/phase1.html`)

The working prototype. Camera feed, MediaPipe integration, basic fingerspelling detection, and a chat UI.

**Features:**
- Live webcam with hand skeleton overlay (teal joint visualization)
- MediaPipe Hands landmark detection via CDN
- ASL A–Z fingerspelling gesture classifier
- Letter buffer → compose words → send as message
- Speech-to-text input (hearing side, Web Speech API)
- Text-to-speech for signed messages
- Chat bubbles with sent/received states
- Demo mode (simulates detection when camera unavailable)

---

### Phase 2 — Expanded (`phases/phase2.html`)

Full feature expansion with a wider left panel, richer UI, and a 200-word sign dictionary.

**New in Phase 2:**
- **200+ word ASL sign dictionary** (greetings, verbs, questions, emotions, places, food, numbers, colors)
- **Word prediction strip** — up to 6 completion chips as you spell letters
- **Hold-time guidance bar** — visual amber bar with step-by-step instructions
- **SVG hand avatar** — animated word-by-word sign playback for hearing users
- **Sign reference card** — shows current letter, hand emoji, and handshape description
- **Translated message log** — persistent left-panel history with timestamps and role labels
- **Quick-sign chips** — one-tap common phrases
- **Settings drawer** — hold duration, confidence threshold, TTS voice/speed, playback speed

---

### Phase 3 — Production Ready (`phases/phase3.html`) ⭐ Recommended

The flagship version. 50/50 split layout, NLP, emotion detection, training mode, and room sharing.

**New in Phase 3:**
- **True 50/50 split layout** — signing side (left) and hearing side (right) each occupy exactly half the screen
- **NLP grammar correction** — ASL word-order → natural English (15+ rewrite rules)
- **Emotion detection** — facial expression recognized and attached to messages as context
- **Sign Training Mode** — practice ASL letters with real-time accuracy scoring
- **Room sharing UI** — generate/join room codes, peer list (WebRTC-ready)
- **Enhanced 3D-style avatar** — gradient-shaded fingers, depth rendering, glow effects
- **Tab navigation** — Conversation / Sign Training / Stats
- **Session accuracy tracker** — top-5 practiced letters with hit counts

---

## 🔧 How the ASL Classifier Works

The classifier uses **geometric rules on MediaPipe Hands landmarks** — no neural network required:

```
Landmark layout (21 points per hand):
  4  = thumb tip        8  = index tip
  12 = middle tip       16 = ring tip      20 = pinky tip
  5  = index base       9  = middle base   13 = ring base   17 = pinky base
  0  = wrist            3  = thumb knuckle

Extension check:
  isExtended(finger) = tip.y < base.y - threshold

Distance check:
  dist(a, b) = √((a.x-b.x)² + (a.y-b.y)²)

Letter patterns:
  A = fist, thumb extended sideways
  B = all 4 fingers extended + thumb across
  C = open hand, thumb-index distance > 0.1
  D = only index extended
  I = only pinky extended
  L = index + thumb extended (L-shape)
  O = thumb-index distance < 0.055
  V = index + middle extended, spread > 0.07
  Y = thumb + pinky extended
  ILY = index + pinky + thumb (I Love You handshape)
  ... (20+ patterns total)
```

The hold timer prevents spurious commits — a sign must be held consistently for the configured duration (default 900ms) before the letter is added to the buffer.

---

## 🌐 Deployment

### GitHub Pages (Free)

```bash
# In your repo settings → Pages → Source: main branch / root
# Your app will be at: https://your-username.github.io/signbridge/
```

### Netlify / Vercel (Free, Recommended)

```bash
# Netlify
netlify deploy --dir . --prod

# Vercel
vercel --prod
```

### Local HTTPS (for camera testing)

```bash
# Using mkcert
mkcert -install
mkcert localhost
npx serve . --ssl-cert localhost.pem --ssl-key localhost-key.pem
```

> See [docs/DEPLOYMENT.md](docs/DEPLOYMENT.md) for full instructions.

---

## 🛠️ Customization

### Adjusting Hold Duration
In Settings (⚙), slide the Hold Duration control (400ms–2000ms). Shorter = faster signing, more false positives. Default 900ms works well for most users.

### Adding Signs to the Dictionary
In `phase3.html`, find the `SIGN_DB` object and add entries:
```javascript
'your_word': { a: 'animation_id' }
```
Then add the animation config to `HAND_CFGS`:
```javascript
'animation_id': { f: [thumb, index, middle, ring, pinky], r: rotation_degrees, l: 'DISPLAY LABEL' }
```
Where each finger value is `0.0` (fully curled) to `1.0` (fully extended).

### Adding NLP Rules (Phase 3)
Find `NLP_RULES` array in `phase3.html`:
```javascript
[/^your regex pattern$/i, 'Your replacement with $1'],
```

---

## 📊 Browser Compatibility

| Browser | Camera | Speech-to-Text | Text-to-Speech | MediaPipe |
|---------|--------|----------------|----------------|-----------|
| Chrome 90+ | ✅ | ✅ | ✅ | ✅ |
| Edge 90+ | ✅ | ✅ | ✅ | ✅ |
| Firefox 88+ | ✅ | ❌ | ✅ | ✅ |
| Safari 15+ | ✅ | ✅ | ✅ | ⚠️ Partial |

> Firefox does not support the Web Speech API. The app falls back gracefully — type mode still works fully.

---

## 🗺️ Roadmap (Phase 4+)

- [ ] **WebRTC P2P** — full peer-to-peer rooms with STUN/TURN servers
- [ ] **ISL / BSL variants** — Indian and British Sign Language classifiers
- [ ] **Full sentence classifier** — beyond fingerspelling to whole-word gesture recognition
- [ ] **3D rigged hand model** — Three.js skeletal avatar replacing canvas renderer
- [ ] **Offline PWA** — service worker caching for full offline operation
- [ ] **Mobile app wrapper** — Capacitor/Cordova packaging for iOS/Android

---

## 🤝 Contributing

1. Fork the repo
2. Create your feature branch: `git checkout -b feature/my-feature`
3. Commit your changes: `git commit -m 'Add my feature'`
4. Push to the branch: `git push origin feature/my-feature`
5. Open a Pull Request

Please see [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

---

## 📄 License

MIT License — see [LICENSE](LICENSE) for details.

---

## 🙏 Acknowledgements

- [MediaPipe](https://mediapipe.dev/) — Google's hand landmark detection
- [Web Speech API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Speech_API) — Browser speech capabilities
- ASL reference data from public sign language datasets

---

*Built with 🤟 — making communication accessible to everyone.*
