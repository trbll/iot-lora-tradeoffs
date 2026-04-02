# IoT LoRa Spreading Factor Explorer

An interactive single-page web visualization for teaching LoRa/LoRaWAN concepts in an IoT course. Built to make the core tradeoffs of LoRa modulation tangible and explorable rather than just tabular.

## Overview

The tool covers three interconnected topics, each building on the last:

1. **Spreading Factor Explorer** — Interactive chirp waveform, range visualization, and tradeoff bars showing how SF affects bit rate, payload, time on air, and range
2. **Transmission Lifecycle** — Animated 11-state timeline of a single LoRaWAN uplink (wake → TX → RX windows → sleep) with a real-time current-draw graph
3. **Battery Life Calculator** — Duty cycle model with adjustable battery capacity, transmit interval, and live formula showing estimated device lifetime

All three panels are linked — changing SF or bandwidth in the controls updates everything downstream.

### Bonus: Audio Analogy

An audio section lets students *hear* the spreading factor concept: the same narrated message plays at 1x speed (SF7) through 0.03x speed (SF12), demonstrating how stretching a signal in time makes it easier to pick out of noise.

## Tech Stack

- **Single `index.html` file** — no build step, no dependencies, no framework
- Plain HTML, CSS, and vanilla JavaScript
- HTML5 Canvas for waveform and range visualizations
- Web Audio API for playback speed manipulation
- ElevenLabs TTS for the audio analogy clip (pre-generated, stored in `audio/`)

## Usage

Open `index.html` in any modern browser. No server required.

```bash
# Or serve locally if you prefer
python3 -m http.server 8000
# Then open http://localhost:8000
```

## File Structure

```
lora-toy/
├── index.html          # The entire application (single file)
├── audio/
│   ├── sf_analogy.mp3  # TTS narration for the SF audio analogy
│   └── sf_analogy.json # Word-level timestamps for highlight sync
├── .env                # ElevenLabs API key (not committed)
├── venv/               # Python venv for TTS generation (not committed)
└── README.md
```

## Data Sources

- **LoRaWAN Data Rates**: US 902-928 MHz band specification (DR0–DR3 at 125 kHz, DR8–DR13 at 500 kHz)
- **Air-time vs. Payload**: Adelantado, X. et al., "Understanding the Limits of LoRaWAN," *IEEE Communications Magazine*, Feb. 2017
- **Current Consumption & Lifecycle States**: Casals, L. et al., "Modeling the Energy Performance of LoRaWAN," *Sensors*, 2017, 17-2364
- **Transceiver Specs**: Semtech SX1272 datasheet (sleep: 0.045 mA, TX: 83 mA @ 20 dBm, RX: 10.5 mA)

## Battery Life Model

The calculator uses the simple duty cycle model from the course slides:

```
D = A / (A + S)                          # Duty cycle
L = B / (D × I_A + (1 - D) × I_S)       # Battery life in hours
```

Where:
- **B** = Battery capacity (mAh) — adjustable via slider
- **D** = Duty cycle — computed from active time and transmit interval
- **I_A** = Average active current (mA) — weighted average across all 11 lifecycle states (varies with SF)
- **I_S** = Sleep current (0.045 mA) — constant from SX1272 datasheet
- **A** = Active time per transmission cycle (ms) — sum of all non-sleep states, TX duration varies with SF
- **S** = Sleep time = transmit interval minus active time

## Version

v1 — April 1, 2026

## Author

Created by Claude Opus 4.6 (1M) in Claude Code for Prof. Bell's IoT course.
