# Changelog

All notable changes to this project are documented in this file.

---

## [4.0.0] — 2026-02-28

### New Features

#### VA Planned Flights Panel
- **New "Planned" tab** alongside the existing "Active Flights" tab in the VA panel
- Displays scheduled bids fetched from phpVMS `/api/user/bids`
- **2-column grid layout** — route display spans the full panel width, details in compact columns
- **Boarding pass–style flight info card** with animated progress bar and aircraft icon
- Airline logos per flight loaded from phpVMS database
- Pilot name, flight number, aircraft type, departure/destination with full airport names
- Scheduled departure time with UTC display
- Click any planned flight to open the matching pilot marker on the map (if active)

#### Mobile Responsive Design
- **Dedicated toggle button bar** for small screens (✈ Flights · Network)
- VA panel, network selector and weather overlay box collapse into hidden drawers on mobile
- Side-tab controls remain accessible without blocking the map
- Dynamic table height adapts to viewport
- Boarding pass card switches to single-column layout on narrow screens
- All button and icon sizing adjusted for touch targets

#### IVAO Controller Rating Badges
- Full IVAO rating badge system: OBS → DEL → GND → TWR → APP → CTR → FSS → SUP → ADM
- **VID links** to official IVAO tracker (`https://www.ivao.aero/Member.aspx?Id=…`)
- Unified popup design for VATSIM and IVAO controllers (dark title bar, white content area)
- Online time display for IVAO controllers

#### Portable Domain Support
- Removed all hardcoded domain references (`german-sky-group.eu`)
- phpVMS API calls now use `window.location.origin` — works on **any** phpVMS installation without changes
- `PHPVMS_BASE` config variable added for transparency
- Clear config comment block for other admins

### Security — Critical Fixes

**12 XSS vulnerabilities patched** — all external API data (VATSIM, IVAO, phpVMS) was previously injected into `innerHTML` without escaping.

New security helper functions:

```javascript
h(str)           // HTML-escape all innerHTML output
safeUrl(url)     // Accept only HTTPS URLs without special characters
safeCallsign(s)  // Allow only A–Z, 0–9, _, - (max 20 chars)
safeFreq(s)      // Allow only digits and dot (max 8 chars)
```

| Severity | Issue | Fix |
|----------|-------|-----|
| 🔴 Critical | XSS via `innerHTML` — VATSIM/IVAO/phpVMS data (12 locations) | `h()` wrapper on all output |
| 🔴 Critical | CSS injection via `querySelector` with unsanitised callsign | `safeCallsign()` whitelist |
| 🔴 Critical | Open redirect / `javascript:` URI in IVAO VID link `href` | `safeUrl()` HTTPS-only validation |
| 🟡 Medium | Frequency string injected without sanitising (4 locations) | `safeFreq()` numeric-only filter |
| 🟡 Medium | Rating type coercion — string vs. number comparison | `parseInt()` coercion before comparisons |
| 🟡 Medium | Blade variables (center lat/lng, zoom) inserted as raw strings | `parseFloat()` / `parseInt()` with safe fallbacks |
| 🟢 Low | `target="_blank"` links missing `rel="noopener noreferrer"` | Added to all external links |

### Bug Fixes
- Fixed: Boarding pass card flickering on tab switch (CSS `visibility` instead of `display` toggle)
- Fixed: VATSIM/IVAO controller popup rating badge not rendering at OBS level
- Fixed: Weather/network panel not expanding on mobile due to `overflow: hidden` on parent container
- Fixed: Airline logo fallback when logo URL returns 404
- Fixed: Planned flights panel showing stale data after bid changes without manual refresh
- Fixed: `⚠ Unavailable` error message replaced with `⚠ phpVMS API not reachable` for clarity

---

## [3.0.1] — 2026-02-24

### Bug Fixes & Code Quality
- Removed: All 9 `console.log` debug statements from production code (intentional `console.warn`/`console.error` in error handlers retained)
- Improved: Airline logo output changed from `{!! json_encode() !!}` (unescaped) to `@json()` (Blade-escaped, safer)
- Fixed: Outdated comment referencing jsDelivr CDN (logo source had changed but comment was never updated)

---

## [3.0.0] — 2026-02-24

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
- **VA Flight Route Line** — clicking a VA aircraft shows a dashed red line to the destination airport
- **VA Aircraft Icon** — phpVMS aircraft replaced with a distinctive white/blue SVG icon; rotation handled by leaflet-rotatedmarker
- **Dark Map persistent** — dark mode state saved to localStorage and restored on reload
- **TRACON auto-merge** — TRACON / Approach Control facilities merged into the nearest airport marker (within 80 km)
- **Airport full names** — full airport names from VATSpy data shown in controller popups
- **ATIS collapsible** — ATIS text shows a 60-character preview with "Show full ATIS" toggle
- **Route line destination badge** — red ICAO label shown at the destination airport when route line is active
- **Badge legend** — visual reference panel showing all badge types and colours

### Improvements
- Controller zoom thresholds lowered: badges visible from zoom 3, labels from zoom 5
- Default start state: Controllers active, Pilots and FIR Sectors off
- Airline logos loaded from phpVMS database (no external CDNs)
- Airport marker click area enlarged to 36 px for easier interaction
- APP/TRACON badge changed from orange to green; combined APP+ATIS shows "Ai" badge

### Bug Fixes
- Fixed: Dark Map button had no effect when OWM API key was missing
- Fixed: VA route line not shown on second click (scope bug in `lastDrawnArr`)
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
