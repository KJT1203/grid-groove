# Grid Groove

A drum machine, an instrument sequencer and a little song arranger, living in one HTML file. No samples, no libraries, no build step — every sound is synthesised live with the Web Audio API.

**Live:** https://kjt1203.github.io/grid-groove/

![no dependencies](https://img.shields.io/badge/deps-0-ff3d81) ![one file](https://img.shields.io/badge/files-1-39d9ff) ![57 instruments](https://img.shields.io/badge/instruments-57-ffd166)

## Use it

**Song** — four patterns, `A` to `D`. Click one to edit it, type the play order into CHAIN (`AAAB` plays A three times then B), DUPLICATE copies the pattern you are on into the next slot, STEPS sets how long a pattern is (8 to 32).

**Drums** — 12 lanes and 5 kits (classic, 808, 909, lo-fi, acoustic). Click a cell to cycle it: off → on → accent. `M` mutes a lane, shift-click it to solo, the small slider sets its level.

**Instruments** — up to 8 tracks, each with its own instrument, octave, note length, level and TONE. Click cells in the note grid to place notes; a column can hold as many as you like, so chords work. Notes are locked to the KEY and SCALE at the top, so nothing lands wrong. Notes belonging to the other tracks show as faint ghosts, so parts can be written against each other.

**Everything else** — SWING pushes off-beat 16ths late, SPACE is the reverb, ECHO is a tempo-synced ping-pong delay, RANDOM rolls a new version of the current pattern, COPY LINK puts the whole song in the URL, EXPORT WAV renders 1–8 passes of the chain to a file.

**Keys** — space plays and stops, `1`–`4` pick a pattern, ctrl+Z / ctrl+shift+Z undo and redo. Everything autosaves to localStorage.

## The instruments

57 of them, all synthesised, grouped in the picker:

| Family | |
|---|---|
| **Bass** | bass synth, sub bass, wobble bass, bass guitar, double bass, tuba |
| **Plucked** | guitar, e-guitar, ukulele, mandolin, banjo, sitar, koto, harp, pizzicato |
| **Keys** | piano, rhodes, harpsichord, clavinet, church organ, rotary organ, reed organ, accordion |
| **Strings** | strings, violin, viola, cello, tremolo strings |
| **Brass** | trumpet, muted trumpet, french horn, trombone, brass section |
| **Winds** | flute, pan flute, clarinet, oboe, saxophone, harmonica |
| **Tuned perc** | marimba, xylophone, vibraphone, glockenspiel, music box, kalimba, steel drum, bell, tubular bells, timpani |
| **Synth** | synth lead, supersaw, warm pad, synth pluck, chiptune, FM keys |
| **Voice** | choir aah, choir ooh |

They are honest synth approximations, not sampled recordings — a synthesised violin is a filtered saw with vibrato, not a violin. Within that limit they are built from the real mechanism of each sound.

## How it works

- **Scheduling** — a `setInterval` runs every 25 ms and schedules any step falling inside the next 120 ms directly on the audio clock (`ac.currentTime`). JS timers are jittery; the audio clock is not, so the groove stays tight even when the page is busy. `requestAnimationFrame` only paints the playhead and the scope.
- **Plucked strings** are Karplus-Strong: a ring buffer of noise, low-pass filtered as it recirculates, rendered once at 220 Hz and re-pitched with `playbackRate`. Damping is the difference between a banjo and a harp.
- **Bowed and blown** instruments are detuned oscillators through a resonant lowpass with an attack/hold/release envelope, a delayed vibrato LFO, and a little filtered noise for bow or breath. Brass adds a filter sweep so the tone opens as it starts.
- **Organs** are additive — one sine per drawbar. **Rhodes, bells and chimes** are two-operator FM. **Piano, marimba and music box** give every partial its own decay. **Choir** runs saws through three parallel bandpass formants.
- **Drums** are oscillators with pitch envelopes (kick, tom, conga, cowbell), filtered noise (snare, clap, hats, shaker) and six inharmonic squares for the ride. A kit is three multipliers over those voices — pitch, decay and brightness — so the whole set re-tunes without a second voice bank.
- **Loudness** — every instrument carries a measured trim so switching between them does not jump in volume, and the mix runs through a fixed headroom gain into a compressor.
- **Effects** — reverb is a convolver fed by a generated noise impulse; echo is a dotted-eighth delay pair, left feeding right and right feeding back through a lowpass so the tail darkens and alternates channels. Both are sends, with drums going in quieter than instruments.
- **Export** rebuilds the identical graph inside an `OfflineAudioContext`, renders faster than realtime and encodes 16-bit stereo PCM by hand.
- **Songs are a string**: `v3|bpm|swing|key|scale|reverb|chain|kit|patterns|echo|drumkit`, with each pattern's notes packed as 12-bit hex masks. That same string is the URL hash, the localStorage value and the undo history. Old `v1` and `v2` links still load.

## Run locally

```bash
npx -y serve . -l 4190
```

Then open http://localhost:4190. Add `?test=1` to run the built-in checks — pattern round-trips, old-link upgrades, chain parsing, step-count changes, undo, swing timing, and an offline render that must come out audible.

Built for fun. MIT.
