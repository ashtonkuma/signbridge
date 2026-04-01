# Changelog

All notable changes to SignBridge are documented here.

---

## [3.0.0] — Phase 3: Production Ready

### Added
- **50/50 split layout** — left panel (signing) and right panel (hearing) each exactly 50% width
- **NLP grammar correction** — 15+ rewrite rules transform ASL word order into natural English before sending. Raw sign sequence preserved in log for reference.
- **Emotion detection** — Simulated facial expression recognition (MediaPipe FaceMesh integration point). Detected emotion (Happy, Focused, Surprised, Calm, Concerned, Neutral) displayed in camera badge and attached to messages.
- **Sign Training Mode** — Dedicated tab for practicing ASL letters. Real-time accuracy scoring against live classifier. Session tracker shows top-5 practiced letters with hit counts.
- **Room sharing UI** — Generate 6-character room codes, join existing rooms, peer list with connection status. WebRTC P2P handshake UI fully built; STUN/TURN is Phase 4.
- **Tab bar navigation** — Conversation, Sign Training, and Stats tabs across top of right panel.
- **Enhanced 3D-style avatar** — Gradient-shaded finger bodies, palm depth shading, double-ring fingertip glow, knuckle joint markers.
- **NLP preview in buffer** — Shows corrected English sentence below buffer as you sign, before sending.
- **Emotion tag on messages** — Detected expression shown on sent bubbles in right panel.
- **Ambient multi-layer glow** — Three radial gradient layers (teal, violet, amber) for visual depth.
- **Grain texture overlay** — Subtle noise texture via inline SVG filter.

### Changed
- Left panel widened to 50% (was 480px fixed)
- Sign reference card now 140px (was 152px) to accommodate wider camera at equal ratio
- Hold guidance bar increased to 8px height for better visibility
- Avatar canvas is now the 3D bar at top of right panel (110×90px persistent display)
- Toast notifications repositioned to avoid overlap with 50% layout

### Improved
- Classifier adds G-shape detection (index pointing horizontally, no thumb)
- Skeleton overlay fingertip rings increased to 10px radius
- Confidence bar uses gradient fill (teal2 → teal) instead of flat color

---

## [2.0.0] — Phase 2: Full Feature Set

### Added
- **200+ word ASL sign dictionary** — greetings, pronouns, verbs, questions, feelings, time, places, food, numbers, colors, utility words
- **Word prediction strip** — up to 6 completion chips, best match highlighted with "best" badge. Tap to accept and auto-advance.
- **Hold-time guidance bar** — full-width amber→teal gradient bar, three labeled markers (0s, halfway, full duration). Three color-coded instruction steps with dot indicators.
- **Sign reference card** — 152px card beside camera showing detected letter at 56px, hand emoji, plain-English handshape description, and positioning hint.
- **Translated message log** — persistent left-panel history. Entries labeled 🤟 Signed / 🗣 Spoken with timestamps. Separate from hearing person's chat.
- **Hold timer in detection badge** — second amber bar shows hold progress inside camera overlay.
- **Quick-sign chips** — 15 most common signs (hello, thank you, please, yes, no, I love you…) for one-tap buffer insertion.
- **Settings drawer** — hold duration (400ms–2s), confidence threshold (40–95%), auto-send toggle, word prediction toggle, skeleton overlay toggle, TTS voice selector, TTS speed, playback speed, fingerspell toggle.
- **Multi-word phrase detection** — tokenizer checks 2-word phrases (e.g. "i love you", "thank you", "how much") before falling back to single words.
- **SVG hand avatar player** — word-by-word playback with 20 animation frames per sign, progress bar, word chips highlighting, Play/0.5×/1.5× controls.
- **Language variant selector** — ASL / BSL / ISL (classifier stays ASL; label change is placeholder for future model swap).

### Changed
- Left panel width increased from 320px to 480px
- Camera and sign card displayed side by side using flex row
- Detection letter in overlay increased to 42px (was 26px)
- ASL alphabet grid replaced with 2-row mini strip (13 columns) at panel bottom
- Chat thread width adapts to remaining right panel space

### Fixed
- Hold timer now resets correctly when a different letter is detected mid-hold
- TTS cancels previous utterance before speaking new message
- Word buffer cursor renders correctly after multi-character word chunks

---

## [1.0.0] — Phase 1: Foundation

### Added
- Live webcam feed via `getUserMedia`
- MediaPipe Hands CDN integration with automatic fallback to demo mode
- Hand skeleton overlay on canvas (21-point teal visualization with joint rings)
- ASL gesture classifier — 20+ letter patterns using landmark geometry
- 800ms hold threshold before letter commits to buffer
- Letter buffer with Space / Delete / Clear / Send actions
- Chat thread with sent/received bubble types
- Speech recognition (Web Speech API) for hearing side input
- Text-to-speech for signed messages (auto-read aloud)
- Three input modes: Sign / Type / Speak with pill switcher
- ASL alphabet reference grid (26 cells, highlights current detected letter)
- Camera enable/disable toggle
- Demo mode — simulates "HELLO WORLD" detection when MediaPipe unavailable
- Confidence meter (0–100%) displayed in detection badge
- Scan ring animation while camera is active
- Toast notification system

---

*SignBridge follows [Semantic Versioning](https://semver.org/).*
