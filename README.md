# Auto Input (Geode mod for Geometry Dash)

Looks at the hazards and blocks ahead of the player and automatically **clicks, holds, or
spam-clicks** to avoid dying.

## Project layout
```
AutoInput/
├── mod.json                 Geode mod manifest + settings
├── CMakeLists.txt           Build script (standard Geode template)
├── logo.png                 Mod icon
├── about.md / changelog.md  Shown in the in-game mod list
├── src/
│   ├── main.cpp             Hooks (PlayLayer + GJBaseGameLayer::processCommands)
│   ├── AutoInput.hpp        Bot: reads game state, injects button input, per-gamemode logic
│   ├── Planner.hpp          Pure logic: cube-jump simulator + corridor path planner (DP)
│   └── LevelCache.hpp       Sorted cache of every hazard / solid / orb in the level
├── tests/planner_test.cpp   Stand-alone test of the planner (no Geode needed)
└── .github/workflows/build.yml   Builds Windows/macOS/Android in GitHub Actions
```

## Before you build
1. In `mod.json` change `"id"` (`yourname.auto-input`), `"developer"` and `"repository"`.
2. Install the Geode CLI and SDK: https://docs.geode-sdk.org/getting-started/

## Build locally
```bash
geode sdk install          # once
geode sdk install-binaries # once (Windows/macOS: downloads GD bindings)
geode build                # produces  build/yourname.auto-input.geode
```
Copy the `.geode` file into your Geometry Dash `geode/mods` folder (or use `geode package install`).

## Build without installing anything locally
Push this folder to a GitHub repo -> **Actions** tab -> *Build Auto Input* -> download the
artifact. It builds Windows, macOS and Android.

## Run the planner tests (optional)
```bash
cd tests
g++ -std=c++20 -O2 -I../src planner_test.cpp -o planner_test && ./planner_test
```

## How it works
* **Cube / Robot** - every physics step the bot forward-simulates the jump arc (with the
  game's real jump/gravity constants) against the cached hitboxes, finds the window of frames
  in which a click survives, and presses in the middle of that window. If a new jump is needed the
  instant it lands, it keeps the button **held** so the game buffers the re-jump.
* **Ship / Wave** - a dynamic-programming search finds a collision-free path through the next
  ~0.5 s of level; the bot **holds** or **releases** to follow it.
* **UFO** - same path search, but it **spam-clicks** (rate-limited) to flap upward.
* **Swing** - clicks to flip gravity toward the path.
* **Ball / Spider** - flips lane when a hazard is about to reach it.
* **Orbs** - optionally taps orbs it is touching when a hazard follows them.

## Settings
| Setting | Meaning |
|---|---|
| Enable Auto Input | master switch |
| Lookahead | planning distance in seconds |
| Safety margin | extra clearance around hazards |
| Use orbs | auto-hit orbs (experimental) |
| Don't save completions | attempts where the bot clicked are not saved/submitted |

## Honest limitations
* It is a heuristic bot, not a guaranteed level-beater.
* Not handled: moving / rotating / toggled objects, triggers, slopes, platformer mode, per-player
  gamemodes in dual mode (player 2 just mirrors the input), mini-size physics differences,
  speed-changing physics edge cases, and very tight ship/wave gaps.
* **API note:** this was written against the Geode/GD bindings from memory. If the compiler complains
  about a member name (e.g. `handleButton`, `m_isTestMode`, `m_isBird`), check the bindings for your GD
  version; input injection lives in one function (`Bot::sendButton`).
* Because it's a cheat-style mod, completions are **not saved** by default. Don't use it to submit
  records.
