# Bass Drop Creator

![Bass Drop Creator](banner.svg)

[![Live Demo](https://img.shields.io/badge/demo-live-brightgreen?style=flat-square)](https://slmingol.github.io/bass-drop-creator/)
[![GitHub Pages](https://img.shields.io/badge/hosted-GitHub%20Pages-blue?style=flat-square&logo=github)](https://slmingol.github.io/bass-drop-creator/)
[![Last Commit](https://img.shields.io/github/last-commit/slmingol/bass-drop-creator?style=flat-square)](https://github.com/slmingol/bass-drop-creator/commits/main)
[![License](https://img.shields.io/github/license/slmingol/bass-drop-creator?style=flat-square)](LICENSE)

**Browser-based bass drop synthesizer.** Select a musical note, shape five independent synthesis layers, preview the result in-browser, and export a 16-bit WAV file ready for MainStage, Logic, or any DAW as a sampler instrument.

**[Try it live →](https://slmingol.github.io/bass-drop-creator/)**

---

## What it does

You pick a note — say B♭1 or G2 — and the app derives the right frequencies across every synthesis layer so the drop is harmonically grounded to that pitch. Every parameter is exposed as a slider, nothing is hidden. When it sounds right, you download a `.wav` named after the note (`bass_drop_Bb1.wav`) and drop it straight into MainStage's EXS24 or Alchemy as a one-shot sample.

---

## Synthesis layers

The signal is built from five parallel layers mixed together, then passed through a normalizer, soft saturator, and output gain stage before encoding.

| Layer | Role | How it works |
|-------|------|-------------|
| **Sub** | Foundational low-end | Pure sine wave with exponential amplitude decay. Sets the fundamental pitch. |
| **Sweep** | The "drop" character | A second sine whose frequency glides from a high starting pitch down to the sub frequency. Phase-continuous — no clicks. Five glide shapes: Exponential, S-Curve, Logarithmic, Exp Squared, Bounce. |
| **Punch** | Attack transient | Two sines at subFreq×2 and subFreq×3 summed with a fast attack/slow decay envelope. Creates the initial thud. |
| **Rumble** | Texture and weight | White noise passed through two cascaded biquad IIR lowpass stages (~4th-order Butterworth). Adds the physical sub-floor sensation. |
| **Body** | Midrange warmth | A third sine — typically tuned to the fifth above the sub — with a slow attack envelope that fills out the tone after the initial hit. |

After mixing, the chain runs:

1. **Normalize** — peak-normalize to ±1.0 before saturation
2. **Tanh saturation** — `tanh(x · drive) / tanh(drive)` soft clipper; drive 1.0 = clean, 3.0+ = heavy
3. **Output gain** — post-saturation level trim, hard-clipped at ±1.0
4. **Encode** — manual RIFF/WAV header + 16-bit PCM at the selected sample rate (44.1 or 48 kHz), mono

---

## Parameters

### Sub
| Parameter | Range | Default | Description |
|-----------|-------|---------|-------------|
| Frequency | 10–200 Hz | 40 Hz | Fundamental pitch (10 Hz floor enables infrasound sub drops) |
| Decay | 0.1–5 | 1.0 | Higher = shorter tail |
| Volume | 0–1 | 0.9 | Layer mix level |

### Sweep
| Parameter | Range | Default | Description |
|-----------|-------|---------|-------------|
| Start Freq | 50–500 Hz | 160 Hz | Pitch at t=0 |
| End Freq | 10–200 Hz | 35 Hz | Pitch after glide — set to 10–20 Hz for true sub drop effect |
| Glide Time | 0.1–8 s | 2.5 s | Duration of the frequency sweep |
| Shape | 5 options | Exponential | Exponential, S-Curve, Logarithmic, Exp Squared, Bounce |
| Decay | 0.1–5 | 0.9 | Amplitude decay rate |
| Volume | 0–1 | 1.0 | Layer mix level |

### Punch
| Parameter | Range | Default | Description |
|-----------|-------|---------|-------------|
| Decay | 0.5–12 | 3.5 | How fast the transient fades |
| Volume | 0–1 | 0.4 | Layer mix level |

### Rumble
| Parameter | Range | Default | Description |
|-----------|-------|---------|-------------|
| LPF Cutoff | 20–300 Hz | 70 Hz | Lowpass filter cutoff for the noise |
| Decay | 0.1–5 | 1.2 | Noise tail length |
| Volume | 0–1 | 0.25 | Layer mix level |

### Body
| Parameter | Range | Default | Description |
|-----------|-------|---------|-------------|
| Frequency | 40–400 Hz | 90 Hz | Resonant tone pitch |
| Decay | 0.1–5 | 2.0 | Amplitude decay rate |
| Volume | 0–1 | 0.35 | Layer mix level |

### Master
| Parameter | Range | Default | Description |
|-----------|-------|---------|-------------|
| Pre-Attack | 0–5 s | 0 s | Ambient swell before the main hit |
| Duration | 1–12 s | 6.0 s | Total sample length |
| Drive | 1–5 | 2.2 | Saturation intensity |
| Gain | 0.1–2 | 1.0 | Post-saturation output level |
| Sample Rate | 44.1 / 48 kHz | 44.1 kHz | Use 48 kHz for video or MainStage |

---

## Presets

48 factory presets organized into three functional groups:

| Group | Count | Description |
|-------|-------|-------------|
| **Hits** | 19 | Short punchy impacts — pad-triggerable, drill accents, quick scene changes |
| **Sub Drops** | 18 | Pitch falls from audible bass down to infrasound (up to 196 Hz → 12 Hz) |
| **Builds** | 11 | Long atmospheric sounds — ballads, climax moments, show openers and closers |

Hover any preset chip for a description. Search by keyword (name or description text). Preset area is collapsible. Save your own presets; they appear in a Saved section and persist in `localStorage`.

<details>
<summary><strong>Full preset list (48 presets)</strong></summary>

### Hits

| Preset | Description |
|--------|-------------|
| **808 Punch** | 808-style kick. Fast 0.35s sweep, balanced punch. E2 (83 Hz), 3.5s. |
| **Aggressive Impact** | High thin character (A2, 110 Hz). Aggressive but controlled. 3.5s. |
| **Bass Hit** | Ultra-short 0.25s sweep — immediate punchy thud. Bb1, 4s. |
| **Beat Drop** | Punchy E2 beat drop hit with fast clean sweep. 3s. |
| **Brass Tail** | Sits under a brass chord resolution. Short 2.5s, B1 (62 Hz). |
| **Cinematic Boom** | Film trailer impact. Heavy rumble and body, fast sweep. E1 (41 Hz), 3.5s. |
| **Clean Sub** | Minimal distortion, soft punch, studio-clean tone. C2 (66 Hz), 6s. |
| **Default** | Balanced starting point. All five layers active, moderate 2.5s sweep. Bb1. |
| **Drill Accent** | Ultra-short 1.5s hit for drill set changes and visual accents. E2. |
| **DTX Impact** | Optimized for DTX pad triggering. Ultra-short 1s, instant attack. A1 (55 Hz). |
| **Drum Feature** | Punch-dominant hit for percussion feature moments. A1 (55 Hz), 3s. |
| **Grimequake** | C1 (33 Hz). Extreme drive (4.8). Grime and dubstep character. |
| **Heavy Club** | Darker, heavier club hit. Bb1, strong punch and thick body. |
| **High Thin** | G2 (98 Hz). Thin, aggressive character. High drive (3.9). Dense attack decay. |
| **Hip Hop Sub** | Warm hip-hop bass. Short 0.5s sweep, low drive. C2 (66 Hz), 4s. |
| **Pit Impact** | Front ensemble thud from pit/synthesizer. Heavy punch, very short 2s. A1. |
| **Punchy Distorted** | D2 (74 Hz). Heavy distortion (drive 4.5), aggressive attack and decay. |
| **Tight Punch** | E2 (83 Hz). Fast 1.1s sweep, strong punch transient, 4s total. |
| **Transition Hit** | Versatile 3s hit for movement and section transitions. D2 (74 Hz). |

### Sub Drops

| Preset | Description |
|--------|-------------|
| **C Sub Drop** | C2 (65 Hz) → C1 (33 Hz) in 1.5s. Short punchy sub drop, clean decay. 4s. |
| **D Bass Drop** | D3 (147 Hz) → C1 (33 Hz) over 2s. Heavy saturated version, longest tail. 6s. |
| **D Drop** | D3 (147 Hz) → C1 (33 Hz) over 2s. Medium drive, balanced weight. 6s. |
| **D Drop Clean** | D3 (147 Hz) → C1 (33 Hz) over 2s. Low drive, pure sweep tone. 6s. |
| **Dark Descent** | Slow 6s atmospheric descent. A1 (55 Hz), dark filtered tail, 10s total. |
| **Deep Sub** | G1 (49 Hz). Long 3.5s sweep, 8s total. Classic deep sub drop feel. |
| **Downlift** | Logarithmic sweep shape — fast start, slow end. E2, 1.5s fall, 4s total. |
| **F Deep Sub** | F2 (87 Hz) → F0 (22 Hz) over 4s. Full-length sweep to infrasound, hot signal. 4.5s. |
| **F Minor Sub** | F2 (87 Hz) → F0 (22 Hz) over 3s. Smooth sweep-dominant drop, F minor tonality. 5s. |
| **Field Sub** | C1 (33 Hz) for field subwoofers. Heavy rumble, 1s sweep, 6s total. |
| **G Drop** | G3 (196 Hz) → G0 (25 Hz) over 2.5s. Long clean G sweep to infrasound. 6s. |
| **Half-Time Sub** | G1 (49 Hz), short 0.6s sweep, 5s total. Fits half-time phrase endings. |
| **Heavy C** | F3 (174 Hz) → C1 (33 Hz) in 0.2s, then 5.5s heavy sustain. Hard hit + long tail. |
| **Massive Sub Drop** | Maximum impact. 150 Hz → 12 Hz in 3s. Near-infrasound — felt, not heard. |
| **Pure Sub** | Very clean — minimal punch, minimal rumble. S-curve sweep, 2s pre-attack. C1. |
| **Quick Sub Drop** | Fast version. 110 Hz → 15 Hz in 1.2s. For quick transitions between phrases. |
| **Sub Drop** | The classic marching band sub drop. 120 Hz → 18 Hz in 2.5s. Sweep-dominant. |
| **Sub Sweep** | Wide range: Bb4 to Bb1 over 4.5s. Very musical, 8s total. |

### Builds

| Preset | Description |
|--------|-------------|
| **Ballad Sub** | 2.5s pre-attack swell, barely any punch. Clean and musical. A1 (55 Hz), 8s. |
| **Climax Hit** | B1 (62 Hz). Heavy punch and rumble. For the biggest show climax moments. 5s. |
| **Epic Build** | 3s pre-attack swell, then 5s drop. G1 (49 Hz), 10s total. Dramatic show moments. |
| **Guard Feature** | Light and warm. G2 (98 Hz), minimal punch. For color guard feature moments. 5s. |
| **Lyrical Sub** | Very clean, 1s pre-attack swell, near-zero punch. E1, 8s. For ballad underscoring. |
| **Rise & Hit** | 2s ambient swell then a hard hit. E1 (41 Hz), 5.5s total, S-curve sweep. |
| **Riser Drop** | 4s pre-attack swell then drop. G1, 10s total. For the biggest show moments. |
| **Show Closer** | C1 (33 Hz). Long 8s tail with strong body. For the final chord of the show. |
| **Show Opener** | High-energy immediate impact with body warmth. E1 (41 Hz). Show opening hit. |
| **Stadium Boom** | C1 (33 Hz), maximum rumble. Designed for large venues with full PA and subs. 7s. |
| **Sub Swell** | Gentle 1.5s pre-attack swell, G1 (49 Hz). Atmospheric, slow-build texture. 6s. |

</details>

---

## How to use

1. **Pick a note** — choose note name (C through B) and octave (1–4). Frequency display updates in real time.
2. **Transpose to Note** — scales all frequency parameters (Sub, Sweep Start/End, Body, Rumble) proportionally to the selected note, preserving the preset's octave relationships and character.
3. **Load a preset** — pick from 48 factory presets or save your own. Hover for a description; search to filter by keyword.
4. **Dial in the layers** — adjust any slider to taste. Changes invalidate the cached render so Preview always regenerates.
5. **Preview** — plays synthesized audio via Web Audio API. Press Escape to stop.
6. **Download WAV** — synthesizes and downloads `bass_drop_<note><octave>.wav`.
7. **Reset All** — returns every parameter to its default value.

All settings persist automatically in `localStorage` between sessions.

---

## Technical details

- **Language**: vanilla HTML/CSS/JS — zero build step, zero dependencies
- **Synthesis**: `Float64Array` sample-by-sample computation in the main thread
- **Sweep shapes**: five glide curves — Exponential, S-Curve (smoothstep), Logarithmic (log1p), Exp Squared (u²), Bounce
- **Sweep settle fade**: sweep amplitude fades out after sweep completes to prevent beating against the sub
- **Filter design**: bilinear-transform biquad lowpass (Q = 0.7071) applied twice for ~4th-order Butterworth rolloff
- **Phase continuity**: Sweep layer accumulates phase from frequency, so any glide curve produces no discontinuities
- **WAV encoding**: manual `ArrayBuffer` / `DataView` construction of the RIFF header; no Blob API tricks needed
- **Playback**: `AudioContext.createBuffer()` → `AudioBufferSourceNode` for in-browser preview
- **Persistence**: `localStorage` keys — `bdc-params`, `bdc-note-idx`, `bdc-note-oct`, `bdc-theme`, `bdc-simple`, `bdc-user-presets`, `bdc-presets-open`

---

## Running locally

No server required — open directly in any modern browser:

```bash
open index.html
```

Or serve it (useful if your browser blocks `file://` audio):

```bash
python3 -m http.server 8000
# then open http://localhost:8000
```

## Repo structure

```
index.html       # The entire app — single file, no build step
banner.svg       # README banner
bass_drop.py     # Original Python prototype (numpy/scipy)
```

---

## Origin

This app is a port of a Python prototype (`bass_drop.py`) that used `numpy` and `scipy.signal` for the same five-layer design. The browser version replaces `numpy` with typed arrays and `scipy.signal.butter` + `lfilter` with a hand-rolled biquad IIR, producing bit-identical output at the same sample rate.
