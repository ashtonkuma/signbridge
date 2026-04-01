# SignBridge Architecture

## Overview

SignBridge is a **zero-dependency, single-file web application**. Each phase is a standalone `.html` file that runs entirely in the browser with no build step, no server, and no backend.

```
Browser
  ├── getUserMedia() ─────────────────── Webcam stream
  ├── MediaPipe Hands (CDN) ──────────── 21-point landmark detection
  ├── Gesture Classifier ─────────────── Landmark → ASL letter/word
  ├── Hold Timer ─────────────────────── 900ms debounce before commit
  ├── Buffer ─────────────────────────── Letter accumulation
  ├── Word Predictor ─────────────────── Prefix-match against WORD_LIST
  ├── NLP Corrector (Phase 3) ────────── Regex rewrite rules
  ├── Canvas API ─────────────────────── Skeleton overlay + Avatar renderer
  ├── Web Speech API ─────────────────── STT (hearing side) + TTS (signed messages)
  └── DOM ────────────────────────────── Chat UI, log, settings
```

## Data Flow

### Signing Side (Deaf/Mute → Hearing)

```
Camera Frame
    ↓
MediaPipe Hands → 21 landmarks [{x,y,z}]
    ↓
Gesture Classifier → { letter: 'A', conf: 0.82 }
    ↓
Hold Timer (900ms) → commit if held long enough
    ↓
Buffer ['H','E','L','L','O'] → "HELLO"
    ↓
Word Predictor → ['hello','help','he','here'...]
    ↓
Send → NLP Correction → "Hello"
    ↓
Chat bubble (right panel) + Log entry (left panel) + TTS
    ↓
Avatar Player loads tokenized words → animates word-by-word
```

### Hearing Side (Hearing → Deaf/Mute)

```
Text input / Speech Recognition
    ↓
addMsg('recv', text)
    ↓
Chat bubble (right panel) + Log entry (left panel)
    ↓
Avatar bar loads tokenized words
    ↓
playAv3dWord() → drawAv(canvas, animId, frame, total)
    ↓
Word chips highlight sequentially
```

## Key Components

### Gesture Classifier

Located in the `classify()` function. Uses five boolean flags:
- `iE` — index finger extended
- `mE` — middle finger extended  
- `rE` — ring finger extended
- `pE` — pinky extended
- `tE` — thumb extended sideways

Plus two distance measurements:
- `tiD` — thumb-to-index tip distance (detects O, C, F shapes)
- `imD` — index-to-middle spread (detects V vs U)

Returns `{ letter, conf }` or `null`.

### Hold Timer

```
lastLetter = '' (reset when different letter detected)
holdStart = Date.now() (set when new letter first detected)

On each frame:
  if same letter as lastLetter:
    held = now - holdStart
    pct = (held / holdMs) * 100
    if held >= holdMs AND cooldown elapsed:
      commitSign(letter)
      reset holdStart, lastLetter
```

### Word Predictor

```javascript
function getPreds(partial) {
  const lastWord = partial.toLowerCase().split(' ').pop()
  return WORD_LIST
    .filter(w => w.startsWith(lastWord) && w !== lastWord)
    .sort((a,b) => (a.length - lastWord.length) - (b.length - lastWord.length))
    .slice(0, 6)
}
```

### NLP Corrector (Phase 3)

Regex-based rewrite rules applied before sending:
```javascript
[/^i want (.+)$/i, 'I want $1'],
[/^(.+) where\?*$/i, 'Where is $1?'],
[/^i not (.+)$/i, "I don't $1"],
// ... 15+ rules
```

### Avatar Renderer

`drawAv(canvas, animId, frame, total)` renders a stylized hand using:
- Finger extension values `[0.0–1.0]` per finger
- Wrist rotation angle in degrees
- Sinusoidal wave motion overlay: `Math.sin(t * Math.PI * 2) * amplitude`
- Linear gradient fills for depth (light top → dark bottom)
- Concentric circles for fingertip glow

## File Size Budget

| File | Size |
|------|------|
| phase1.html | ~44KB |
| phase2.html | ~64KB |
| phase3.html | ~95KB |

All inline — no external JS, CSS, or image dependencies beyond Google Fonts and MediaPipe CDN.

## Browser APIs Used

| API | Purpose | Fallback |
|-----|---------|----------|
| `navigator.mediaDevices.getUserMedia` | Camera | Demo mode |
| `HTMLCanvasElement.getContext('2d')` | Overlay + Avatar | — |
| `SpeechRecognition` | Hearing-side input | Type mode |
| `speechSynthesis` | TTS output | Silent |
| `navigator.clipboard` | Copy room code | Toast fallback |

## Phase 4 Extension Points

### WebRTC
`joinRoom()` and `createRoom()` in Phase 3 are UI stubs. To add real P2P:
1. Add a signaling server (WebSocket or existing service like PeerJS)
2. Replace the `setTimeout` simulation with `RTCPeerConnection` setup
3. Stream `canvas` frames or the raw landmark arrays over the data channel

### Full Sentence Classifier
Replace `classify(lm)` with a TensorFlow.js model:
```javascript
const model = await tf.loadLayersModel('models/asl_model.json')
const tensor = tf.tensor2d([flattenedLandmarks], [1, 63])
const prediction = model.predict(tensor)
```

### MediaPipe FaceMesh (Emotion)
Add alongside Hands:
```javascript
const faceMesh = new FaceMesh(...)
faceMesh.onResults(results => {
  const landmarks = results.multiFaceLandmarks[0]
  const emotion = classifyEmotion(landmarks) // AU-based classifier
})
```
