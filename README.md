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
| **Sub** | Foundational low-end | Pure sine wave with an exponential amplitude decay. Sets the fundamental pitch of the drop. |
| **Sweep** | The "drop" character | A second sine whose frequency glides exponentially from a high starting pitch down to the sub frequency over a configurable time window. Phase-continuous — no clicks. |
| **Punch** | Attack transient | Two sines (100 Hz + 80 Hz) summed with a fast attack / slow decay envelope. Creates the initial "thud" that registers on small speakers. |
| **Rumble** | Texture and weight | White noise passed through two cascaded biquad IIR lowpass stages (~4th-order Butterworth response). Adds the physical, sub-floor sensation. |
| **Body** | Midrange warmth | A third sine at a configurable frequency — typically tuned to the perfect fifth above the sub — with a slow attack envelope that fills out the tone after the initial hit. |

After mixing, the chain runs:

1. **Normalize** — peak-normalize the mix to ±1.0 before saturation
2. **Tanh saturation** — `tanh(x · drive) / tanh(drive)` soft clipper; drive 1.0 = clean, 3.0+ = heavy
3. **Output gain** — post-saturation level trim, hard-clipped at ±1.0
4. **Encode** — manual RIFF/WAV header + 16-bit PCM at the selected sample rate (44.1 or 48 kHz), mono

---

## Parameters

### Sub
| Parameter | Range | Default | Description |
|-----------|-------|---------|-------------|
| Frequency | 20–200 Hz | 40 Hz | Fundamental pitch |
| Decay | 0.1–5 | 1.0 | Higher = shorter tail |
| Volume | 0–1 | 0.9 | Layer mix level |

### Sweep
| Parameter | Range | Default | Description |
|-----------|-------|---------|-------------|
| Start Freq | 50–500 Hz | 160 Hz | Pitch at t=0 |
| End Freq | 20–200 Hz | 35 Hz | Pitch after glide (should match Sub Freq) |
| Glide Time | 0.1–8 s | 2.5 s | Duration of the frequency sweep |
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
| Duration | 1–12 s | 6.0 s | Total sample length |
| Drive | 1–5 | 2.2 | Saturation intensity |
| Gain | 0.1–2 | 1.0 | Post-saturation output level |
| Sample Rate | 44.1 / 48 kHz | 44.1 kHz | Output sample rate — use 48 kHz for video or MainStage |

---

## How to use

1. **Pick a note** — choose note name (C through B, with enharmonic spellings) and octave (1–4). The frequency display updates in real time.
2. **Apply to Layers** — click "Apply to Layers" to set smart defaults: Sub and Sweep End at the note's fundamental (or one octave below if >80 Hz), Sweep Start two octaves up, Body at the perfect fifth.
3. **Load a preset** — choose from five factory presets (Default, Deep Sub, Heavy Club, Clean Sub, Tight Punch) to start from a tuned patch, then adjust from there. Save your own with the name input and Save button.
4. **Dial in the layers** — adjust any slider to taste. Changes invalidate the cached render so Preview always regenerates.
5. **Preview** — plays the synthesized audio directly in the browser via Web Audio API. Synthesizes on first click if not yet generated.
6. **Download WAV** — synthesizes, draws the waveform, and downloads a `.wav` file named `bass_drop_<note><octave>.wav`.
7. **Reset All** — returns every parameter to its default value.

All slider positions, note selection, theme preference, and user presets persist automatically in `localStorage`.

---

## Technical details

- **Language**: vanilla HTML/CSS/JS — zero build step, zero dependencies
- **Synthesis**: `Float64Array` sample-by-sample computation in the main thread
- **Filter design**: bilinear-transform biquad lowpass (Q = 0.7071) applied twice for ~4th-order Butterworth rolloff
- **Phase continuity**: the Sweep layer accumulates phase from frequency, so the exponential glide produces no discontinuities regardless of sample rate
- **WAV encoding**: manual `ArrayBuffer` / `DataView` construction of the RIFF header; no Blob API tricks needed
- **Playback**: `AudioContext.createBuffer()` → `AudioBufferSourceNode` for in-browser preview
- **Persistence**: `localStorage` stores all 20 synth parameters as JSON plus note index, octave, theme choice, and user-saved presets

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
