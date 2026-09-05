# Issue #12: 🎙️ Abstract-to-Podcast Audio Summary (Two-Host Structured Dialogue & TTS)

**Labels**: `enhancement`, `help wanted`, `frontend`, `backend`, `ai`, `difficulty: medium`  
**Difficulty**: `Medium`  
**Target Files**: `netlify/functions/podcast-script.ts` (NEW), `public/js/tts.js` (NEW), [`public/app.js`](../public/app.js), [`public/index.html`](../public/index.html), [`public/styles.css`](../public/styles.css), `netlify/functions/__tests__/podcast_script.test.ts` (NEW), `public/__tests__/tts.test.js` (NEW)

---

## 📌 Problem & Context

Researchers frequently consume scientific literature while commuting or multitasking. However, feeding raw abstracts directly into a single TTS voice produces a dry, monotonous read-aloud experience that is difficult to absorb.

Per production guidance from community discussion, the most listenable format is an intermediate **two-host conversational dialogue** (similar to NotebookLM audio overviews). Converting paper metadata and consensus syntheses into structured dialogue turns (`[{"speaker": "A", "text": "..."}, {"speaker": "B", "text": "..."}]`) delivered by alternating voices creates an engaging, natural briefing.

---

## 🎯 Goal

1. Generate structured two-host dialogue scripts (`speaker: 'A' | 'B'`) directly from paper metadata or Consensus Meter syntheses using Google Gemini with structured JSON mode and Upstash Redis caching.
2. Provide a **"🎙️ Audio Briefing"** button on paper cards and the Consensus Meter panel.
3. Deliver audio using dual alternating voices (Host A / Host B) via the browser Web Speech API (`window.speechSynthesis`).
4. Provide an interactive dialogue transcript preview showing turns with live active-speaker highlighting, which doubles as an accessible text fallback if browser TTS voices are unavailable.

---

## ⚙️ Technical Specification

### 1. Dialogue Script Generation (`netlify/functions/podcast-script.ts`)
- **Route**: `POST /api/podcast-script`
- **Request Payload**:
  ```json
  {
    "type": "paper" | "consensus",
    "paper": { "id": "...", "title": "...", "authors": [...], "year": 2024, "abstract": "...", "consensusStance": "supports" },
    "query": "quantum machine learning",
    "consensus": { "consensusScore": 85, "summaryText": "..." }
  }
  ```
- **Output Schema**:
  ```json
  {
    "turns": [
      { "speaker": "A", "text": "Hey everyone, welcome back. Today we're looking at a fascinating paper on..." },
      { "speaker": "B", "text": "Right, and what caught my attention immediately was..." }
    ],
    "cached": false
  }
  ```
- **Prompt Engineering**: Prompt Gemini directly for minimal schema (`speaker` and `text`) with a worked spoken example containing natural contractions, quick questions, and conversational register.
- **Distributed Cache**: Hash dialogue turns and store in Upstash Redis via `cacheSet(key, turns, 86400 * 30)` for instant, zero-marginal-cost re-listening.
- **Deterministic Fallback**: Local two-host script generator if Gemini API key is not provided or network is offline.

### 2. Dual-Voice Playback Engine (`public/js/tts.js`)
- **`TTSManager` Class**:
  - Auto-detects and pairs distinct voices (Voice A for Speaker A, Voice B for Speaker B) prioritizing natural English voices.
  - Sequential turn-by-turn playback queue switching voices dynamically between turns.
  - Speed selector support: `1.0x`, `1.25x`, `1.5x`, `2.0x`.
  - Chrome speech synthesis keep-alive handling to prevent automatic 15-second pauses.
  - Event hooks: `onTurnChange(index, turn)`, `onStateChange(state)`, `onComplete()`, `onError(err)`.

### 3. UI Controls & Styling (`public/index.html`, `public/styles.css`, `public/app.js`)
- **Card & Panel Triggers**:
  - `🎙️ Audio Briefing` button on paper cards (`#results-list`) and Consensus Meter panel (`#consensus-progress-box`).
- **Podcast Player Dock (`#podcast-player-dock`)**:
  - Bottom docked player with Oxford Blue and Ivory Paper styling.
  - Active sound wave equalizer animation (`.audio-playing-wave`).
  - Play, Pause, Resume, Stop controls.
  - Speed toggle chips (`1.0x`, `1.25x`, `1.5x`, `2.0x`).
  - Customizable Host A and Host B voice dropdown selectors.
  - "View Transcript" drawer toggle button.
- **Dialogue Transcript Modal (`#podcast-transcript-modal`)**:
  - Renders the conversation turns with distinct Host A and Host B tags.
  - Dynamically highlights and scrolls to the currently active spoken turn.
  - Graceful fallback: automatically displays readable dialogue if speech synthesis is unsupported in the visitor's browser.

---

## ✅ Acceptance Criteria

- [ ] Add `🎙️ Audio Briefing` button on paper cards and Consensus Meter panel.
- [ ] Implement backend `/api/podcast-script` endpoint generating two-host structured dialogue turns (`speaker` and `text`) with Gemini JSON mode.
- [ ] Cache dialogue turns in Upstash Redis to prevent redundant LLM generations.
- [ ] Implement dual-voice playback engine alternating between Host A and Host B.
- [ ] Implement audio dock with Play, Pause, Resume, Stop controls and `.audio-playing-wave` equalizer animation.
- [ ] Add playback speed controls (`1.0x`, `1.25x`, `1.5x`, `2.0x`) and customizable Host A/B voice selectors.
- [ ] Provide interactive dialogue transcript drawer showing turns with live active-speaker highlighting.
- [ ] Gracefully handle browsers without Web Speech API support by rendering the dialogue transcript preview.
- [ ] Maintain test coverage >85% with unit tests for backend endpoint and frontend TTS manager.

---

## 💡 Code Guidance

- Keep schema minimal (`speaker` and `text`); avoid extra emotional tags that induce flat LLM prose.
- Provide a spoken example in the prompt with contractions and lively back-and-forth cadence.
- Cache on the turns hash; keep turns as the durable artifact so audio rendering is derived and disposable.
- Feature-detect `speechSynthesis` and available voices before attempting playback.
