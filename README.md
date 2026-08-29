# Grid Groove

A 16-step drum machine + bassline sequencer that lives in one HTML file. No samples, no libraries, no build step — every sound is synthesised live with the Web Audio API.

**Live:** https://kjt1203.github.io/grid-groove/

![grid + bass sequencer](https://img.shields.io/badge/deps-0-ff3d81) ![one file](https://img.shields.io/badge/files-1-39d9ff)

## Use it

- **Click a drum cell** to cycle it: off → on → accent (accents hit louder).
- **Click a bass cell** to place a note; one note per column, click again to clear.
- **Space** or the PLAY button starts/stops.
- **M** mutes a lane, the small slider next to it sets its level.
- **SWING** pushes every off-beat 16th late — 0 is straight, ~20 is a shuffle.
- **COPY LINK** puts the whole pattern in the URL. Paste it anywhere, it loads back.
- Patterns also autosave to localStorage, so a reload keeps what you made.

## How it works

- **Scheduling** — a `setInterval` runs every 25 ms and schedules any step falling inside the next 120 ms directly on the audio clock (`ac.currentTime`). JS timers are jittery; the audio clock is not, so the groove stays tight even when the page is busy. `requestAnimationFrame` only paints the playhead.
- **Drums** — oscillators with pitch/gain envelopes (kick, tom, rim) and filtered white noise from a 2-second buffer (snare, clap, hats). The clap is three short noise bursts a few milliseconds apart plus a tail.
- **Bass** — saw + square an octave down, through a lowpass whose cutoff sweeps down over the note. Notes are locked to C minor pentatonic so nothing lands wrong.
- **Patterns** are a plain string: `v1|bpm|swing|lane digits per drum|bass row per step`. That same string is the URL hash and the localStorage value.

## Run locally

```bash
npx -y serve . -l 4190
```

Then open http://localhost:4190. Add `?test=1` to run the built-in self-check (pattern round-trip, clamping, swing timing) — it prints to the console and shows a banner.

Built for fun. MIT.
