# Bass Drop Creator

![Bass Drop Creator](banner.svg)

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

40 factory presets organized into three functional groups:

| Group | Count | Description |
|-------|-------|-------------|
| **Hits** | 19 | Short punchy impacts — pad-triggerable, drill accents, quick scene changes |
| **Sub Drops** | 10 | Pitch falls from audible bass down to infrasound (120 Hz → 10–20 Hz) |
| **Builds** | 11 | Long atmospheric sounds — ballads, climax moments, show openers and closers |

Hover any preset chip for a description of what it does. The preset area is collapsible.

**Search**: Type any keyword in the filter box to narrow presets by name or description. Supports multiple space-separated terms (AND logic). Autocomplete suggests words from the full preset library as you type.

**Save your own**: Enter a name in the save field and click Save. Custom presets appear in a Saved section and persist in `localStorage`.

---

## How to use

1. **Pick a note** — choose note name (C through B) and octave (1–4). Frequency display updates in real time.
2. **Apply to Layers** — sets smart defaults: Sub and Sweep End at the note's fundamental, Sweep Start two octaves up, Body at the perfect fifth.
3. **Load a preset** — pick from 40 factory presets or save your own. Hover for a description; search to filter.
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
