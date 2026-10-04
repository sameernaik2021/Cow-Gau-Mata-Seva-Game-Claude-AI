# Gau Seva — A Gaushala Journey

A calm, browser-based simulation of daily care in a gaushala (cow shelter), with the cultural and spiritual context of cow seva in Hinduism woven into gameplay. Built with plain HTML, CSS and JavaScript in a single file. No libraries, no build step, no network access needed.

Progress measures **responsible care and learning**, never "spiritual merit". Myth, devotion, scripture and modern practice are labelled separately.

## Quick start

1. Open `gau-seva-game.html` in any modern browser (Chrome, Edge, Firefox, Safari).
2. Choose **Begin Journey** (or **Continue Journey** if a save exists).
3. Play. Progress saves automatically in your browser's local storage.

## How to play

Each day lasts about 90 seconds. The loop is: **Observe → Decide → Perform Seva → Learn → Feedback → Progress**.

1. Walk to a store (Hay store, Water trough, Medical station) and **collect** supplies. You can carry up to 3 of each.
2. Walk near a cow and watch her needs: hunger, thirst, cleanliness, mood and health. Bubbles (💧 🌾 🧹 🤒) show what she needs.
3. Choose a seva action. The game checks it first. For example, it refuses to push food on a cow that isn't hungry (ahimsa).
4. Completing seva unlocks **learning moments** from the 20 concepts, which are saved to the Encyclopedia.
5. At the end of the day you see a summary, herd welfare, cow bonds and a reflection question.

### Seva actions

| Key | Action | Needs |
| --- | --- | --- |
| 1 | Offer hay | Hay in inventory, cow not full |
| 2 | Give water | Water in inventory, cow thirsty |
| 3 | Clean shelter | Area not already clean |
| 4 | Groom & comfort | Cow's mood not already high |
| 5 | Examine | Reports weakest need or illness |
| 6 | Give medicine | Medicine in inventory, cow unwell |

### Controls

| Input | Effect |
| --- | --- |
| WASD / Arrow keys | Move |
| Tap / click the field | Walk to that point |
| 1–6 | Seva actions (near a cow) |
| E | Collect supplies (near a store) |
| P / Esc | Pause / resume |
| 📖 / ⏸ buttons | Encyclopedia / pause |

The game auto-pauses when the browser tab is hidden.

## Architecture

The code follows the structure in the design document, in one file split into labelled sections:

| Section | Responsibility |
| --- | --- |
| Content system | `RAW` / `CONTENT`: the 20 concepts, activities and reflections as data |
| Core services | `Bus` (events), `Save` (localStorage), `Audio_` (optional tones) |
| Game state | `S` object; states `MENU`, `INTRO`, `PLAY`, `LEARN`, `PAUSE`, `DAYEND`, `ENCY` |
| Entities & components | Cows and stations as plain objects built from identity, needs, health, movement and AI parts (`makeCow`) |
| Welfare simulation | `Welfare.update`: need decay, illness, health recovery |
| AI system | `AI`: finite-state machine (wander, graze, rest, seek water, seek food), update throttling for distant cows, simple collision separation |
| Seva commands | `Rules` validate each action; `Seva.run` / `Seva.collect` apply it. Input never changes welfare directly |
| Progression | `ruleMet` / `checkUnlocks`: unlocking concepts, learning queue, save snapshot |
| UI | Panels, HUD, contextual action dock |
| Input → Interaction | `Input` (keys, pointer) feeds `Interaction.update` (focus on nearest cow or store) |
| Rendering | Canvas 2D drawing with y-sorted entities and a dusk tint |
| Main loop | Input → update → AI → gameplay → render via `requestAnimationFrame` |

Core pipeline: **Player input → Interaction → Seva action → Rules → Welfare/world state → Feedback → Knowledge → Progression**.

## Editing the content

All educational text lives in the `RAW` array near the top of the script. Each entry is:

```js
[title, sanskritTerm, category, kind, activity, unlockRule, learningText]
```

`kind` is one of `Cultural`, `Scriptural`, `Mythological`, `Devotional`, `Modern practice`.

Unlock rules:

| Rule | Meaning |
| --- | --- |
| `start` | Unlocked at the beginning |
| `day:N` | Reached day N |
| `seva:N` | Total successful seva actions ≥ N |
| `feed:N`, `water:N`, `clean:N`, `groom:N`, `heal:N`, `examine:N`, `collect:N` | That action performed N times |

Content can be changed or translated without touching gameplay code.

## Saved data

Stored in `localStorage` under the key `gauSeva.v1`: current day, action counts, discovered concepts, cow bonds, inventory and the sound setting. Choosing **New Journey** clears it. To reset manually, clear site data for the page in your browser.

## Testing

The game exposes a small `window.GauSeva` object for debugging and automated tests. It was validated headlessly with jsdom, covering concept data, collecting supplies, all seva actions, refusal rules, unlocks, day end and next day, save and load, and cows staying in bounds (22 checks passing). It has not been verified on real devices, so please check rendering and touch input in your own browser.

## Scope and known limits

- Single gaushala scene (the temple, trees and shelter are scenery); no separate village, farm or Vraja scenes.
- One fixed top-down camera.
- English only. The content structure is ready for translation.
- No multiplayer.
- Cows' needs, shelter state and in-day progress are not saved. Only the profile above is saved, and cows restart fresh on load.
- Sound is simple generated tones, with a toggle in the menu and pause screen.

## Possible extensions

Additional scenes and NPC dialogue, regional-language content files, isometric or cinematic cameras, community (multiplayer) seva with server-authoritative welfare, and a larger herd using stronger level-of-detail systems.

## Note on content

The learning text is written as a respectful, introductory summary. Traditions and practices vary across regions and communities, and mythological stories are presented as sacred narrative, not history. Claims about products such as panchagavya are noted as not established by modern medicine. Educators and subject experts should review and adapt the content before wider use.
