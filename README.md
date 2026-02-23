# VATSIM + IVAO Live Map for phpVMS 7

A feature-rich live map widget for phpVMS 7 that integrates real-time **VATSIM and IVAO** data alongside your Virtual Airline flights — with an active VA flights panel, route lines, weather overlays, and dark mode.

![Live Map Screenshot](screenshot.png)

**Live Demo:** [german-sky-group.eu/livemap](https://german-sky-group.eu/livemap)

---

## Features

### Dual Network — VATSIM + IVAO
- **VATSIM & IVAO simultaneously** — toggle each network on/off independently; stats (pilots / controllers) always shown even when a network is hidden
- **Color-coded markers** — VATSIM pilots in blue, IVAO pilots in orange; instantly distinguishable at a glance
- **Independent refresh cycles** — VATSIM refreshes every 30 s, IVAO every 15 s
- **FIR Sectors** — Active airspace boundaries from VATSpy GeoJSON for both networks

### VATSIM
- Real-time pilot positions with callsign, route, aircraft type, altitude, speed, heading and pilot name
- Airport markers with colour-coded controller badges: Delivery, Ground, Tower, Approach/ATIS, Center
- TRACON / Approach Control auto-merged into nearest airport marker (within 80 km)
- ATIS collapsible (60-char preview + "Show full ATIS")
- Full airport names from VATSpy data in popups
- Controller name, CID, rating, and online time in popups

### IVAO
- Real-time pilot positions with flight plan data
- Airport markers with orange-outline IVAO badge style
- ATC positions mapped to IVAO facility types (DEL/GND/TWR/APP/DEP/CTR/FSS)

### VA Active Flights Panel
- **Top-centre collapsible panel** listing all active phpVMS/ACARS flights
- Columns: Flight · Route · Aircraft · Altitude · Speed · Distance · Status · Pilot
- Click any row → map zooms to aircraft + dedicated info card opens with full flight details + dashed route line to destination
- Live badge showing total active flight count
- Refreshes in sync with your ACARS update interval

### Route Lines & Info Card
- Click any VA aircraft on the map for a dashed red route line to the destination airport
- **VA Info Card** — dedicated card (top-right) filled directly from ACARS API data, independent of the phpVMS Rivets binding — works reliably every time
- Destination airport highlighted with a red ICAO badge
- Close button on card; clicking the map also clears the route line

### Weather Overlays
- 6 OWM layers: Clouds, Radar, Storms, Wind, Temperature, Combo
- Opacity slider
- **Dark Map** — CSS night mode with persistent state (localStorage)

### General
- Follow Flight — keeps map centred on active VA flights
- Badge legend showing all controller badge types
- Airline logos from your phpVMS airline database
- All UI labels in English

---

## Requirements

- phpVMS 7 (any recent version)
- HTTPS (required for VATSIM/IVAO APIs and OWM tiles)
- OpenWeatherMap API key (free) — **optional**, only needed for weather overlays

---

## Installation

Copy `live_map.blade.php` to the correct path for your theme:

| Theme | Path |
|-------|------|
| seven (default) | `resources/views/layouts/seven/widgets/live_map.blade.php` |
| beta | `resources/views/layouts/beta/widgets/live_map.blade.php` |
| default | `resources/views/layouts/default/widgets/live_map.blade.php` |
| SPTheme | `resources/views/layouts/SPTheme/widgets/live_map.blade.php` |
| Disposable_v3 | `resources/views/layouts/Disposable_v3/widgets/live_map.blade.php` |

> The file is identical for all themes — only the installation path differs.

---

## OpenWeatherMap API Key (optional)

Weather overlays require a free API key from [openweathermap.org](https://openweathermap.org/api).

Open `live_map.blade.php` and find this line near the top of the `<script>` section:

```javascript
var OWM_API_KEY = "YOUR_OPENWEATHERMAP_API_KEY_HERE";
```

Replace `YOUR_OPENWEATHERMAP_API_KEY_HERE` with your key. If you leave the placeholder, all weather buttons are hidden automatically — no errors.

---

## IVAO

IVAO data is fetched from the public IVAO Tracker API (`https://api.ivao.aero/v2/tracker/whazzup`). No API key required. Pilot/controller stats are always loaded in the background regardless of whether the IVAO network toggle is active.

---

## Configuration

| Variable | Default | Description |
|----------|---------|-------------|
| `OWM_API_KEY` | `"YOUR_..."` | OpenWeatherMap key (optional) |
| `VATSIM_REFRESH_MS` | `30000` | VATSIM refresh interval in ms |
| `IVAO_REFRESH_MS` | `15000` | IVAO refresh interval in ms |

The VA flights panel refresh interval uses your phpVMS `acars.update_interval` setting automatically.

---

## Compatibility

Tested with:
- phpVMS 7 (dev branch)
- Themes: seven, Disposable_v3, SPTheme
- ACARS clients: vmsACARS, smartCARS 3

---

## Credits

- [VATSIM](https://vatsim.net/) — live network data
- [IVAO](https://ivao.aero/) — live network data
- [VATSpy Data Project](https://github.com/vatsimnetwork/vatspy-data-project) — FIR boundaries and airport positions
- [Leaflet](https://leafletjs.com/) — map library
- [OpenWeatherMap](https://openweathermap.org/) — weather tile layers
- [phpVMS](https://phpvms.net/) — virtual airline management platform

---

## License

MIT — free to use and modify. A credit link back to this repository is appreciated but not required.
