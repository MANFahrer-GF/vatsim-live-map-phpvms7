# Changelog

All notable changes to this project are documented in this file.

---

## [3.0.0] — 2026-02-23

### New Features

#### IVAO Network Integration
- **Dual-network display** — VATSIM and IVAO can now be shown simultaneously on the same map
- **IVAO pilot markers** — orange SVG aircraft icons (visually distinct from VATSIM blue)
- **IVAO controller markers** — airport badges with orange outline and "IV" label
- **IVAO FIR sectors** — same VATSpy GeoJSON, rendered in a distinct darker teal colour
- **IVAO stats always loaded** — pilot/controller counts shown in the stats bar even when the IVAO layer is toggled off, matching VATSIM behaviour
- **Independent refresh cycles** — VATSIM 30 s, IVAO 15 s; run independently without interference
- **Network toggle buttons** — VATSIM (teal) and IVAO (orange) buttons; shared layer controls (Pilots / Controllers / FIR Sectors) apply to both active networks

#### VA Active Flights Panel
- **Collapsible top-centre panel** showing all active phpVMS/ACARS flights
- Columns: Flight · Route · Aircraft · Altitude · Speed · Distance · Status · Pilot
- **Live count badge** on the toggle button (red when flights active, grey when empty)
- **Distance column** showing flown / planned distance in nmi (`573 / 4895 nmi`)
- **Pilot name** column with first name + last name initial from phpVMS user data
- Refreshes at the same interval as your phpVMS `acars.update_interval` setting
- Status texts translated from phpVMS German locale to English (Unterwegs → En Route, Gelandet → Landed, etc.)
- Row highlight persists across refreshes (active flight stays visually selected)
- Dark map mode automatically darkens the panel (MutationObserver)

#### VA Info Card
- **New dedicated `#va-info-card`** (top-right) filled directly from ACARS API data
- Works independently of the phpVMS Rivets binding — no timing hacks, no marker simulation
- Displays: route, callsign, aircraft registration/type, altitude, speed, pilot name, status badge
- Shows airline logo from phpVMS database
- Close button (✕) on the card
- Clicking the map closes the card and clears the route line
- When a real map marker is clicked, the Rivets card takes over and the VA card hides automatically (MutationObserver)

### Improvements
- All visible UI text is now in English (was previously German in some labels and status texts)
- Stats bar shows `...` while loading (was `—`), `⚠ Error` on failure (was German `⚠ Fehler`)
- VA panel position moved from top-left to top-centre
- Panel width adapts to column count; max-height 400 px with scroll for many flights

### Bug Fixes
- Fixed: VA route line briefly showing wrong destination when clicking a second aircraft quickly (sequence counter + mandatory 150 ms Rivets delay)
- Fixed: Stats box height mismatch between VATSIM and IVAO boxes when text wrapped (nowrap + ellipsis)
- Fixed: IVAO stats showing `—` when network was toggled off (stats now always updated on fetch)

---

## [2.0.0] — 2026-02-23

### New Features
- **VA Flight Route Line** — Clicking a VA aircraft shows a dashed red line to the destination airport
- **VA Aircraft Icon** — phpVMS aircraft replaced with a distinctive white/blue SVG icon; rotation handled by leaflet-rotatedmarker
- **Dark Map persistent** — Dark mode state saved to localStorage and restored on reload
- **TRACON auto-merge** — TRACON / Approach Control facilities merged into the nearest airport marker (within 80 km)
- **Airport full names** — Full airport names from VATSpy data shown in controller popups
- **ATIS collapsible** — ATIS text shows a 60-character preview with "Show full ATIS" toggle
- **Route line destination badge** — Red ICAO label shown at the destination airport when route line is active
- **Badge legend** — Visual reference panel showing all badge types and colours

### Improvements
- Controller zoom thresholds lowered: badges visible from zoom 3, labels from zoom 5
- Default start state: Controllers active, Pilots and FIR Sectors off
- Airline logos loaded from phpVMS database (no external CDNs)
- Airport marker click area enlarged to 36 px for easier interaction
- APP/TRACON badge changed from orange to green; combined APP+ATIS shows "Ai" badge

### Bug Fixes
- Fixed: Dark Map button had no effect when OWM API key was missing
- Fixed: VA route line not shown on second click (scope bug in lastDrawnArr)
- Fixed: Duplicate `layeradd` handlers overwriting each other
- Fixed: Dead variable `vaCallsignSet` causing silent ReferenceError

---

## [1.0.0] — 2026-02-20

### Initial Release

- Real-time VATSIM pilot positions with popup (callsign, route, aircraft, altitude, speed, heading, pilot name)
- VATSIM controller markers with colour-coded facility badges (DEL, GND, TWR, APP, CTR)
- FIR sector boundaries as coloured polygons from VATSpy GeoJSON
- Controller positions from VATSIM Transceivers API
- Airport positions from VATSpy.dat (~7000 airports)
- Key normalisation: EWR ↔ KEWR, AU Y-prefix, Pacific P-prefix airports
- Pilot route line (dashed red) on aircraft click
- Follow Flight toggle
- OWM weather overlays: Clouds, Radar, Storms, Wind, Temperature, Combo + opacity slider
- Dark Map (CSS filter night mode)
- Airline logos in VATSIM pilot popups
- VATSIM live indicator dot with pilot/controller counts
- 30-second VATSIM refresh interval
