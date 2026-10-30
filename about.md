# Auto Input

Looks at the hazards and blocks in front of you and **presses, holds or spam-clicks for you** so you don't die.

| Mode | What the bot does |
|------|-------------------|
| Cube / Robot | Simulates the jump arc and picks the safest moment to click. Holds the button to buffer an instant re-jump on landing. |
| Ship | Holds / releases to steer through the safest vertical corridor. |
| Wave | Holds / releases to follow a collision-free path. |
| UFO | Spam-clicks to stay on that path. |
| Swing | Flips gravity (clicks) toward the safe path. |
| Ball / Spider | Flips lane when a hazard approaches. |

Orbs can be hit automatically when a hazard lies right after them.

## Settings
- **Lookahead** - how far ahead it plans.
- **Safety margin** - extra clearance around hazards.
- **Use orbs** - auto-hit orbs (experimental).
- **Don't save completions** - attempts where the bot clicked are *not* saved/submitted.

## Limitations
It is a heuristic bot, not a perfect one. Moving/rotating objects, triggers, slopes, dual-mode with
different gamemodes per player, platformer mode and very tight ship/wave sections are not fully
handled, so it will not beat every level.
