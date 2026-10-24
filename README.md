<div align="center">

# 🦔💨 zg-switch

**Sonic Riders: Zero Gravity (Wii) on Nintendo Switch homebrew**
*Experimental · ahead-of-time translation of the game's code to ARM64 · Dolphin-based hardware core*

<img width="4080" height="1884" alt="20261024_004412" src="https://github.com/user-attachments/assets/aa0d8e9c-bf52-4027-8886-800f5a1b4564" />

![Status](https://img.shields.io/badge/status-beta-orange)
![Platform](https://img.shields.io/badge/platform-Nintendo%20Switch-e60012)
![Speed](https://img.shields.io/badge/speed-~41%25%20of%20Wii-yellow)
![License](https://img.shields.io/badge/license-GPL--3.0-blue)

**English** · [Português (BR)](README.pt-BR.md)

</div>

---

> [!WARNING]
> **Beta.** Speed is currently **~41–44% of the original Wii speed in races (~25 FPS)** and **~39–41% in cutscenes**. It is **not yet playable at full speed**. Current goal: **stable 30 FPS (50%)**, then closer to 60 FPS.

## 📌 What is this?

An experimental project that runs **Sonic Riders: Zero Gravity (Wii)** on a **homebrew Nintendo Switch**.

The game's PowerPC code is **statically translated ahead of time** into native ARM64 (C parts compiled with clang) and linked into a homebrew `.nro`. Wii hardware (GPU/FIFO, DSP audio, timing) is still handled by an **adapted Dolphin-based core**. So this is a **hybrid**: native game code on top of an emulated console.


## ✨ Highlights

- 🎯 **Ahead-of-time translation** of the game's code to native ARM64
- 🐬 **Dolphin-based core** for the Wii's hardware
- ⚙️ **Runtime options** you can toggle and measure (see below)
- 📊 **Honest measurements** with the variation reported

## 📈 Performance

```
Wii speed (100% = 60 FPS)
Now    ████████████████░░░░░░░░░░░░░░░░░░░░░░░░  ~41%   (~25 FPS)
Goal   ████████████████████░░░░░░░░░░░░░░░░░░░░   50%   (30 FPS)  ← next
Full   ████████████████████████████████████████  100%   (60 FPS)
```

| Round | Race (recorded input replay) | Cutscene |
|---|---:|---:|
| 28 | 35% | 40% |
| 29 | 41–44% | 39–41% |

**Progress on the same automatic race start:** R24 ~15% → R25 ~24% → R27 ~21% → **R29 ~40%**

> [!NOTE]
> Results **vary between runs**: we have measured anywhere from 32% to 44% on the same build. Compare numbers only within the same scene and conditions (dock/handheld, charger, temperature).

### Where the time goes (cutscene, round 29)

| Category | Share |
|---|---:|
| Translated game code | 46.5% |
| Dolphin-based core | 45.4% |
| Slow memory path / module support | 6.9% |
| Floating point | 0.7% |
| Hardware (FIFO/MMIO) | 0.3% |

## 🎛️ Runtime options (measured on a race scene)

Baseline: **39.5%**.

| Option | Tested value | Result |
|---|---|---|
| `RIDERSZG_DIRECT_CALLS` | `0` | **28%** (−11 pts: matters a lot) |
| | `16` / `96` | 39% / 39% (same) |
| `RIDERSZG_SYNC_IDLE` | `1` | 33.5% (−6 pts) |
| `RIDERSZG_CACHE_DATA` | `0` | 35.5% (−4 pts) |
| `RIDERSZG_FAST_FP` | `0` | 37% (−2.5 pts) |
| `RIDERSZG_AUTO_IDLE` | `0` | 39% (same) |
| `RIDERSZG_PIN_THREADS` | `0` | 39.5% (same) |
| Build parameter `K` | 4 / 5 / 6 / 7 | **41.5%** / 39.5% / 39.5% / 38.5% |
| Compiler flags | `-Os` / `-fno-slp-vectorize` / `-O3` | 38% / 38.5% / 38.5% |

`K=4` is the current default (+2 pts, close to run-to-run noise). The easy options are exhausted; further gains need new optimizations.


## 🗺️ Roadmap of Development.

- [x] Round 29: current version
- [ ] **Round 30** (in validation):
  - [ ] Game functions without call entry/exit overhead (`preserve_none`)
  - [ ] Leaner paired-single (`psq`) and MEM2 access
  - [ ] Cache flushing only where code exists
- [ ] Direct MEM2 access (cutscenes: ~7M slow accesses per second measured)
- [ ] Trim overhead in the core's dispatch loop
- [ ] **Stable 30 FPS** (≥50% average, ≥45% floor over 5 s windows)
- [ ] Toward 60 FPS

## 🔧 Requirements

- Nintendo Switch with homebrew (e.g., Atmosphère) and an SD card
- Free disk space: **3,54GB**

## 🚀 Build and install

The repository contains the small files of the "homebrew". The **4 large files** (about 3.5 GB) are published in the [Releases page](https://github.com/MegaTail/zg-switch/releases/latest) because they exceed GitHub's 100 MB per-file limit.

1. Download this repository (**Code → Download ZIP**) and extract it.
2. Download the large files from the [latest release](https://github.com/MegaTail/zg-switch/releases/latest):
   - `riderszg.nro`
   - `Image1.cvm`, `Image2.cvm`, `Image3.cvm`
3. Put the files in this structure:

```
riderszg/
├── riderszg.nro            ← from the release
├── game/
│   └── files/
│       ├── Image1.cvm      ← from the release
│       ├── Image2.cvm      ← from the release
│       └── Image3.cvm      ← from the release
└── ... (all other files from this repository)
```

4. Copy the whole `riderszg` folder to the `switch` folder on your SD card.
5. Launch it from the Homebrew Menu holding the R button on the console.

or

use Sphaira and create a Forwarder to open it from the Main menu.

## 📄 License

GPL-3.0. See [LICENSE](LICENSE). Includes code derived from the Dolphin Emulator.
