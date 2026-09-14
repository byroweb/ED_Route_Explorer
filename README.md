# Elite Dangerous Route Explorer

A zero-install 3D route/corridor mapper for Elite Dangerous. **Double-click `index.html`** to open it in your browser — no server, no Python/Node.

## What it does
- Plots a route between a **From** and **To** system and draws it in a manipulable **3D map** (drag to rotate, scroll to zoom, right-drag to pan).
- Shows every **EDSM-known system within N jumps** of the route (the "corridor"), sampled around **every** route star — not just the start.
- **Colours** each star by class — scoopable **KGBFOAM** in warm colours; neutron / white-dwarf / brown-dwarf / black-hole in distinct cool tones.
- Draws stars **solid** when EDSM has scan data and **hollow** (wireframe) when it doesn't — a proxy for "likely untouched".
- Menu for your ship's **jump range**, a **Detour jumps** control (how far off-route to cast the net, 1–5), and a **neutron-boost** (4×) toggle, plus **Calculate** / **Cancel**.
- Click any star to drop a **flag** on it (moves to the next star you click) and open its details.
- The panel lists **uncharted stars** in view (systems with no EDSM scan data), sorted by distance from start. Click one to flag it, open its details, and **fly the camera to it** — so you can find it in the 3D field without hunting.
- **Verify** on a selected system pulls a **per-body breakdown** from EDSM: each body's first-discovery commander/date, which bodies are **landable**, and a **Traffic** line (ships seen this week / total → untravelled · lightly · well travelled).
- The uncharted list has a **Check traffic** button (one lookup per listed system, kept off the auto path): it annotates each with weekly traffic, **dims** systems that already see ships, and re-sorts quietest-first.

### About "Detour jumps"
Detour = how far off the route to include systems, measured in jumps. Internally it widens the corridor radius to `jump range × detour jumps` (capped at EDSM's 100 ly search limit) around each route star. Bigger detour = wider net = more chances to find untouched systems (at the cost of more data and more stars to render).

### About footfall
EDSM does **not** publish footfall (who first set foot on a body), so it can't be shown. Verify shows the closest available signals instead: first-**discovery** per body (✔/✖) and whether each body is **landable** (▼). Footfall itself can only be confirmed in-game.

## How routing works (and its limits)
The router is a client-side A\* pathfinder over **EDSM's database of visited systems** (EDSM is the only one of the big sites that allows in-browser requests). This is dense in the bubble and along established corridors, and works with zero install.

Because it can only see systems that *someone has already visited and uploaded*, it can't route through truly unexplored deep space. When it can't find a jump-by-jump path, it falls back to mapping the **straight-line corridor** and tells you so.

For authoritative long-haul / neutron-highway routes, click **Open in Spansh** — it opens spansh.co.uk pre-filled with your From/To/range. (Spansh's API can't be called from a browser page, so this hands off to their site instead.)

> **"Undiscovered" is a proxy.** No public API knows the game's true first-discovery flags for every system. Hollow stars = no scan data in EDSM. Always confirm in-game with the Discovery Scanner honk.

**Permit-locked systems are screened out** (map, routing, and the uncharted list) using EDSM's `requirePermit` flag. This removes false "uncharted" hits like the Pilots' Federation newbie district (Dromi, Mingfu, …) that veterans can't visit or upload, as well as other permit systems (Sol, Shinrarta Dezhra, …). Genuinely empty non-permit systems (e.g. LFT 509) are kept.

## Requirements
- Internet connection (for EDSM data and the three.js library, loaded from a CDN).
- A modern browser (Chrome/Edge/Firefox).

### Optional: run fully offline-capable UI
The 3D library loads from `unpkg.com`. To avoid the CDN, download `three.module.js`, `OrbitControls.js`, and `CSS2DRenderer.js` into a local `vendor/` folder and update the import map in `index.html`. (Data still needs internet.)

## Files
- `index.html` — everything: layout, three.js import map, and the app logic (EDSM client, A\* router, 3D scene, interactions) inlined so it runs from a double-clicked `file://` URL
- `styles.css` — theme
