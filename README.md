# VATSIM + IVAO Live Map for phpVMS 7

> An interactive real-time map for your Virtual Airline — VATSIM and IVAO pilots, ATC controllers, FIR sectors, weather overlays, VA active flights, planned flights with boarding pass cards, and full mobile support.

![Live Map Screenshot](screenshot.png)

**Live Demo:** [german-sky-group.eu/livemap](https://german-sky-group.eu/livemap)

---

## Table of Contents

1. [Features](#features)
2. [Requirements](#requirements)
3. [Installation](#installation)
4. [Configuration](#configuration)
5. [The Interface — every button explained](#the-interface--every-button-explained)
6. [Understanding Map Markers](#understanding-map-markers)
7. [Popups — what do they show?](#popups--what-do-they-show)
8. [VA Planned Flights — Boarding Pass Card](#va-planned-flights--boarding-pass-card)
9. [Dark Mode](#dark-mode)
10. [Common Problems & Solutions](#common-problems--solutions)
11. [Security](#security)
12. [Compatibility](#compatibility)
13. [Credits](#credits)

---

## Features

### Live Network Data
- **VATSIM Pilots** — real-time aircraft positions with callsign, route, aircraft type, altitude, speed, heading and pilot name
- **VATSIM Controllers** — airport markers with colour-coded badges (Delivery, Ground, Tower, Approach/ATIS, Center)
- **IVAO Pilots** — real-time IVAO aircraft shown in orange, simultaneously alongside VATSIM
- **IVAO Controllers** — airport badges with distinctive orange-outline IVAO style and full rating badge system (OBS → DEL → GND → TWR → APP → CTR → FSS → SUP → ADM)
- **IVAO VID links** — click any IVAO controller VID to open their official IVAO tracker profile
- **FIR Sectors** — active airspace boundaries as clickable coloured polygons with controller info (both networks)
- **TRACON / Approach Control** — auto-merged into nearby airport markers (within 80 km)
- **ATIS** — collapsible full ATIS text inside airport popup (60-char preview + "Show full ATIS")

### Virtual Airline Integration (phpVMS 7)
- **VA Active Flights Panel** — top-centre collapsible panel listing all active phpVMS/ACARS flights with route, altitude, speed, distance, status, pilot name and airline logo
- **VA Planned Flights Panel** — second tab showing all booked bids from phpVMS with boarding pass–style cards
- **VA Info Card** — dedicated flight info card (top-right) filled from ACARS API when clicking a panel row or pilot marker
- **Airline Logos** — loaded directly from your phpVMS airline database (no external CDN)
- **Route Line** — click any aircraft to show a dashed line to its destination airport

### Map Controls
- **Follow Flight** — keeps the map centred on active VA flights (1 pilot → pan, multiple → fit all, 0 → return to default)
- **Free Scroll** — toggle to browse the map manually without being snapped back
- Airport names from VATSpy data in all controller popups

### Weather Overlays (OpenWeatherMap)
Six toggleable layers: Clouds, Precipitation Radar, Thunderstorms, Wind Speed, Temperature, Combo — plus an opacity slider

### Visual & UX
- **Dark Mode** — night filter applied to map tiles
- **Mobile Responsive** — toggle button bar on small screens, collapsible panels, touch-friendly controls
- **Badge Legend** — visual reference for all controller badge types and colours

### Security
- All external API data (VATSIM, IVAO, phpVMS) sanitised before DOM insertion
- 12 XSS vulnerabilities fixed in v4.0.0
- Portable domain support — works on any phpVMS installation, no hardcoded URLs

---

## Requirements

| What | Why |
|------|-----|
| **phpVMS 7** (any recent version) | The system the map is embedded in |
| **HTTPS** | Required for VATSIM/IVAO APIs and OWM weather tiles |
| **OpenWeatherMap API key** (free) | Optional — only needed for weather overlays |

> Leaflet.js is loaded automatically from a CDN — nothing needs to be installed locally.

---

## Installation

### Step 1 — Copy the file into your theme

Copy `live_map.blade.php` to the correct path for your active theme:

| Theme | Path |
|-------|------|
| SPTheme | `resources/views/layouts/SPTheme/widgets/live_map.blade.php` |
| Disposable_v3 | `resources/views/layouts/Disposable_v3/widgets/live_map.blade.php` |

The file is identical for all themes — only the installation path differs.

### Step 2 — Create a page in phpVMS

1. In the phpVMS admin panel go to **Content → Pages**
2. Click **Add Page**
3. Enter a title, e.g. `Live Map`
4. Choose `live_map` as the template (filename without `.blade.php`)
5. Save

### Step 3 — Add a menu item (optional)

1. Go to **Content → Navigation**
2. Add a new menu entry and link it to the page above
3. Save

### Step 4 — Enter your OpenWeatherMap API key

See the [Configuration](#configuration) section below.

---

## Configuration

### OpenWeatherMap API Key (optional)

Weather overlays require a free API key from [openweathermap.org](https://openweathermap.org). The free tier is sufficient — no credit card required.

Open `live_map.blade.php` and find:

```javascript
var OWM_API_KEY = "YOUR_OPENWEATHERMAP_API_KEY_HERE";
```

Replace `YOUR_OPENWEATHERMAP_API_KEY_HERE` with your actual key.

> **Tip:** Use `Ctrl+F` and search for `YOUR_OPENWEATHERMAP_API_KEY_HERE` to find the line instantly.

Without a key, all weather overlay buttons are hidden automatically — the Dark Map button and all other controls still work normally. No errors are thrown.

**Activation time:** A newly created key takes up to 1–2 hours to become active. This is normal.

#### Step by step: getting a free API key

1. Open [openweathermap.org](https://openweathermap.org) and click **Sign In → Create an Account**
2. Register for free and confirm your email
3. Click your **username → My API Keys**
4. Copy the `Default` key (a long alphanumeric string)
5. Paste it into the file as shown above

---

### Admin Panel Configuration (Recommended)

Set your map's default position and behaviour in **Admin → ACARS**:

| Setting | Recommended (German VA) | Description |
|---------|------------------------|-------------|
| Center Coords | `51.1657,10.4515` | Geographic centre of Germany |
| Default Zoom | `5` | Shows Germany + neighbouring countries |
| Live Time | `0` | Only show flights currently in progress |
| Refresh Interval | `60` | Position update interval in seconds |

---

### Configuration Variables

All variables are at the top of the `<script>` section in the file:

| Variable | Default | Description |
|----------|---------|-------------|
| `OWM_API_KEY` | `"YOUR_..."` | OpenWeatherMap API key (optional) |
| `VATSIM_REFRESH_MS` | `30000` | VATSIM data refresh interval in ms |
| `IVAO_REFRESH_MS` | `15000` | IVAO data refresh interval in ms |

The VA flights panel refresh interval is taken automatically from your phpVMS `acars.update_interval` admin setting.

---

## The Interface — every button explained

### VA Flights Panel (top centre)

The panel at the top centre of the map shows all flights of your Virtual Airline. Click the **panel header** (dark bar with ✈ symbol) to collapse or expand it. The arrow (▼ / ▲) on the right shows the current state.

---

#### Tab: ✈ Active [number]

Shows all currently airborne pilots sending an active ACARS signal. Data comes from phpVMS `/api/acars`.

The number in square brackets (e.g. `Active [3]`) shows how many active flights are in progress.

| Column | Description |
|--------|-------------|
| **Flight** | Callsign / flight number |
| **Route** | DEP → ARR airport ICAO |
| **Aircraft** | Registration and type |
| **Altitude** | Current altitude in ft |
| **Speed** | Groundspeed in knots |
| **Distance** | Flown / planned in nmi (e.g. `573 / 4895 nmi`) |
| **Status** | Flight phase (En Route, Boarding, Landed, etc.) |
| **Pilot** | Pilot name from phpVMS user record |

**Clicking a row:**
- Zooms the map to the aircraft position
- Opens the VA Info Card (top-right) with full flight details
- Draws a dashed route line to the destination airport
- Highlights the row as active (persists across panel refreshes)

---

#### Tab: 📋 Planned [number]

Shows all booked but not yet started flights from phpVMS `/api/user/bids`.

| Column | Description |
|--------|-------------|
| **Airline logo** | From phpVMS database |
| **Flight number** | e.g. `DLH456` |
| **Route bar** | Full-width DEP → ARR graphical bar |
| **Scheduled departure** | Planned time in UTC |
| **Aircraft type** | The booked aircraft type |

**Click any row** → a [Boarding Pass Card](#va-planned-flights--boarding-pass-card) expands at the bottom of the panel.

---

### Flight Info Card (top right)

Appears automatically when you click a pilot marker or an Active tab row. Shows all real-time data for the selected flight.

| Field | Meaning |
|-------|---------|
| **Airline logo** | From phpVMS if available |
| **XXXX → YYYY** | Departure ICAO → destination ICAO |
| **Pilot** | Pilot's name |
| **Aircraft** | Type or registration |
| **ALT** | Current altitude in ft |
| **GS** | Ground speed in knots |
| **HDG** | Heading in degrees (0 = North, 90 = East, 180 = South, 270 = West) |
| **Dist** | Remaining distance to destination in nm |
| **Progress bar** | 0% = just departed · 100% = at destination |
| **Status badge** | Current flight status |

The card closes with **✕**, by clicking another marker, or clicking empty map space. Clicking a real VATSIM/IVAO marker directly causes the standard popup to take over and the VA card hides automatically.

---

### Weather Box (bottom left)

Click the title **"Weather Layers ▼"** to collapse or expand. Requires an OWM API key — without one, the weather buttons are hidden automatically.

| Button | Layer | Description |
|--------|-------|-------------|
| ☁️ **Clouds** | Cloud coverage | White/grey cloud cover overlay |
| 🌧️ **Radar** | Precipitation | Blue = light rain · dark blue = heavy |
| ⛈️ **Storms** | Thunderstorms | Orange/red = active convection |
| 💨 **Wind** | Wind speed | Darker = stronger wind |
| 🌡️ **Temp** | Temperature | Blue = cold · orange/red = warm |
| 🌀 **Combo** | Clouds + Radar + Storms | Combined overview |

**Opacity slider** — adjusts the transparency of all active weather layers simultaneously (0% = invisible · 100% = full opacity · default ≈ 70%).

**🌗 Dark Map** — applies a CSS night filter to the base map tiles. All markers, popups and panels remain fully visible. Click again to return to normal.

---

### Network Box (bottom left, above the weather box)

Click **"Network ▼"** to collapse or expand.

#### VATSIM / IVAO Network Toggles

Two toggle switches enable or disable each network independently. Both can be active simultaneously — VATSIM pilots appear in blue, IVAO pilots in orange, making them immediately distinguishable.

Pilot and controller counts are always loaded in the background regardless of whether the layer is toggled off — so you always see live stats for both networks.

- VATSIM refreshes every **30 seconds**
- IVAO refreshes every **15 seconds**

#### 👤 Pilots

Toggles all active pilot aircraft markers for the enabled networks. Each marker shows callsign, route, altitude, speed, heading and airline logo. Click → dashed route line drawn to the destination airport.

#### 🗼 Controller

Toggles airport markers with colour-coded ATC badges. Applies to both VATSIM and IVAO depending on which networks are enabled.

**VATSIM badge colours:**

| Badge | Colour | Facility |
|-------|--------|----------|
| **DEL** | Blue | Delivery |
| **GND** | Orange/Brown | Ground |
| **TWR** | Red | Tower |
| **APP** | Green | Approach (+ ATIS combined) |
| **ATIS** | Light blue | ATIS only |
| **CTR** | Teal/Purple | Center / FIR |

**IVAO badges:** same layout with an orange outline to visually distinguish IVAO positions. Full rating system: OBS → DEL → GND → TWR → APP → CTR → FSS → SUP → ADM.

If a badge has a small **red number** in its top corner (e.g. `TWR²`) → multiple controllers of that type are simultaneously active.

Click any airport marker to open a popup showing: frequency, controller name, CID/VID, rating, time online, and full ATIS text (collapsible).

#### 🗺️ FIR Sectors

Toggles active airspace boundaries as coloured dashed polygons. Only sectors with an active controller are shown. Clicking a sector opens a popup with controller details.

#### 🎯 Follow Flight / Free Scroll

| State | Behaviour |
|-------|-----------|
| **Follow Flight** (green, crosshair ⊕) | Map auto-centres on active VA flights: 1 pilot → pan to aircraft · multiple pilots → fit all into view · 0 pilots → return to default position |
| **Free Scroll** (grey, open lock 🔓) | Browse the map freely without being snapped back |

Click the button to toggle between modes.

---

### Mobile Toggle Buttons (smartphones / tablets only)

On screens below approximately 768 px the panel and network box are hidden automatically. Two toggle buttons appear at the **bottom of the screen**:

| Button | Opens |
|--------|-------|
| **✈ Flights** | VA Flights Panel (Active + Planned tabs) |
| **Network** | Network Box (VATSIM/IVAO toggles + all control buttons) |

The weather box is always accessible via the tappable **"Weather Layers"** title directly on the map.

---

## Understanding Map Markers

### Pilot Markers

Every flying pilot is shown as a small **aircraft icon** rotated to match the current heading. The callsign is displayed below the icon.

- **VATSIM pilots** → blue aircraft icon
- **IVAO pilots** → orange aircraft icon

### Airport Markers

Airports with at least one active ATC station appear as an **ICAO code** (e.g. `EDDF`) with coloured badges below (see badge table above). IVAO airports have an **orange border** around the marker.

### FIR Sectors

Active FIR/UIR sectors appear as semi-transparent coloured polygons. A label in the centre shows the controller callsign and frequency.

- Blue tones → Center controllers (CTR)
- Purple tones → Upper Airspace

### Route Line

- **VATSIM / IVAO aircraft** — click to show a dashed line from the aircraft to its flight plan destination
- **VA aircraft (panel click)** — same behaviour, destination read from phpVMS ACARS data
- A coloured ICAO badge marks the destination airport at the end of the line
- Click anywhere on the map to dismiss the line

---

## Popups — what do they show?

### Pilot Popup

| Field | Content |
|-------|---------|
| **Callsign** | Large and bold at the top |
| **Network badge** | Green `VATSIM` or orange `IVAO` |
| **Route** | `EDDF → EGLL` from the flight plan |
| **Pilot** | Name or CID/VID |
| **Aircraft** | Type (e.g. `A320`) |
| **Altitude** | Current altitude in ft |
| **Ground Speed** | In knots |
| **Heading** | In degrees (0–360) |
| **Squawk** | Transponder code |
| **Online since** | Connection time |

### Airport Popup (ATC & ATIS)

- ICAO code, full airport name, network badge, number of active stations
- Per controller: type badge, callsign, frequency, name, rating, online time
- ATIS section (if active): callsign, frequency, 60-char preview → **"▼ Show full ATIS"** expands complete text

### FIR Sector Popup

Controller callsign, FIR name, frequency, name, rating, online time. Purple `▲ Upper Airspace` note where applicable.

Click the **IVAO VID** in any IVAO popup → opens the official IVAO member profile in a new tab.

---

## VA Planned Flights — Boarding Pass Card

Click a booked flight in the **Planned tab** → a boarding pass card expands at the bottom of the panel:

```
┌────────────────────────────────────────────────────────────┐
│  [Airline Logo]          DLH 456 · Airbus A320        [✕] │
├────────────────────────────────────────────────────────────┤
│                                                            │
│    EDDF  ────────────────── ✈ ────────────────  EGLL      │
│  Frankfurt                               London Heathrow  │
│                                                            │
│  Dep: 14:30 UTC              Aircraft: D-AICE              │
│  ██████████████░░░░░░░░░░░░░░  45%                        │
│                                                            │
└────────────────────────────────────────────────────────────┘
```

| Element | Meaning |
|---------|---------|
| **Airline logo** | From phpVMS — ICAO code shown if no logo |
| **Flight number · Type** | e.g. `DLH 456 · Airbus A320` |
| **ICAO codes** | Large: left = departure · right = destination |
| **Airport names** | Full names below the ICAO codes |
| **Dep: [time] UTC** | Planned departure time |
| **Aircraft: [reg]** | Registration or type of the booked aircraft |
| **Progress bar** | Usually 0% before departure |
| **✕ Close** | Closes the card; the Planned tab stays open |

---

## Dark Mode

Activated via the **🌗 Dark Map** button in the weather box. Uses a CSS filter (`brightness(0.5) contrast(1.2) saturate(1.1)`) on the map tiles — no additional API requests, no performance impact. All markers, popups and panels remain fully readable.

> **Note:** Dark Mode is not saved between page reloads.

---

## Common Problems & Solutions

### Weather layers are not showing

1. **API key not entered** — open browser console (F12 → Console). If you see `[LiveMap] OWM API key not set`, the key is missing → see [Configuration](#configuration)
2. **Key not yet active** — new keys take up to 2 hours → wait and reload
3. **Wrong key** — log in at [home.openweathermap.org/api_keys](https://home.openweathermap.org/api_keys) and copy the correct key

### No pilots or controllers on the map

1. Check VATSIM/IVAO toggles are active (green/orange) in the Network Box
2. Check the Pilots button is active
3. Open browser console (F12 → Console) for error messages
4. Wait 10–15 seconds — first data load can take a moment

### VA flights not showing in Active tab

Normal if no VA pilot is currently sending ACARS. Panel shows `Active [0]`. Appears automatically when a pilot connects — no reload needed.

If pilots are definitely flying: check `https://your-domain.com/api/acars` directly in the browser — it should return JSON.

### Planned flights not showing

1. No member has a flight booked → normal
2. phpVMS `/api/user/bids` not reachable → check phpVMS status
3. phpVMS session expired → reload and log in again

### Airline logos missing

Upload logos in phpVMS admin under **Airlines → [Edit Airline]**. The first 3 letters of a VATSIM callsign are used as the ICAO prefix (e.g. `DLH187 → DLH`). Requirements: airline created in admin, ICAO code matches callsign prefix, logo uploaded.

Recommended logo size: ~200×60 px, PNG with transparent background.

### Panel invisible on mobile

Use the toggle buttons at the bottom of the screen: **✈ Flights** and **Network**.

---

## Security

This map has been through a full security audit. All data from external APIs is sanitised before being displayed in the browser.

**Fixed in v4.0.0:**

| Severity | Issue | Fix |
|----------|-------|-----|
| 🔴 Critical | XSS via `innerHTML` — VATSIM/IVAO/phpVMS data (12 locations) | `h()` HTML-escape wrapper |
| 🔴 Critical | CSS injection via callsign in `querySelector` | `safeCallsign()` whitelist |
| 🔴 Critical | Open redirect / `javascript:` URI in IVAO links | `safeUrl()` HTTPS-only validation |
| 🟡 Medium | Frequency injection (4 locations) | `safeFreq()` numeric-only filter |
| 🟡 Medium | Rating type coercion | `parseInt()` before comparisons |
| 🟢 Low | `target="_blank"` without `rel="noopener noreferrer"` | Added to all external links |

Full details: [SECURITY.md](SECURITY.md)

---

## Compatibility

Tested with:

- **phpVMS 7** (dev branch)
- **Themes:** Disposable\_v3, SPTheme
- **ACARS clients:** vmsACARS, smartCARS 3

---

## Credits

- Weather overlay concept inspired by: Rick Winkelman (Air Berlin Virtual)
- VATSIM live data: [VATSIM Network](https://www.vatsim.net/)
- IVAO live data: [IVAO Network](https://www.ivao.aero/)
- Airport positions and FIR boundaries: [VATSpy Data Project](https://github.com/vatsimnetwork/vatspy-data-project)
- Map library: [Leaflet](https://leafletjs.com/)
- Weather tiles: [OpenWeatherMap](https://openweathermap.org/)
- Virtual airline platform: [phpVMS](https://github.com/nabeelio/phpvms)
- VATSIM/IVAO integration, design & development: German Sky Group

---

## License

MIT License — free to use and modify. Attribution appreciated but not required.

See [LICENSE](LICENSE) for full terms.
