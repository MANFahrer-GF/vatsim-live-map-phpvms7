# VATSIM Live Map for phpVMS 7

A fully featured live map override for [phpVMS 7](https://github.com/phpvms/phpvms) that adds real-time VATSIM integration, weather overlays, and a modern popup design.

---

## ✨ Features

### VATSIM Integration
- **Live pilots** with aircraft icons rotated by heading
- **Controllers** with color-coded badges (DEL / GND / TWR / APP / CTR)
- **FIR/UIR sector boundaries** from VATSpy data
- **TRACON / Approach Control** automatically merged into the nearest airport marker
- **ATIS** collapsed by default, expandable in the popup — no more giant popups
- Full airport name shown in controller popups (from VATSpy data)
- Refreshes every **30 seconds** (within VATSIM's fair-use policy)

### Badge Legend
| Badge | Color | Meaning |
|-------|-------|---------|
| **D** | 🔵 Blue | Delivery |
| **G** | 🟠 Orange | Ground |
| **T** | 🔴 Red | Tower |
| **Ai** | 🟢 Green | Approach + ATIS |
| **C** | 🩵 Teal | Center / FIR |
| **i** | 💙 Light Blue | ATIS only |

### Own VA Flights
- Large distinctive white/red aircraft icon — always on top of all VATSIM markers
- Click any aircraft (VA or VATSIM) → dashed red route line drawn to destination airport
- **Follow Flight** toggle — auto-pan to own flight or scroll freely

### Weather Overlays (OpenWeatherMap)
- Clouds, Radar, Storms, Wind, Temperature, Combo layers
- Opacity slider
- Dark map toggle
- Requires a free [OpenWeatherMap API key](https://openweathermap.org/api)

### Design
- Consistent card-style popups for pilots, controllers, FIR sectors
- Airline logos loaded from your phpVMS database (always current)
- Built-in badge legend in the VATSIM control panel
- Light/dark mode compatible

---

## 📦 Installation

### Standard phpVMS 7

1. Copy the file to your phpVMS installation:
```
resources/views/vendor/phpvms/maps/live_map.blade.php
```

2. Add your OpenWeatherMap API key (free at [openweathermap.org](https://openweathermap.org/api)):
```javascript
// In live_map.blade.php, find this line:
var OWM_API_KEY = "YOUR_OPENWEATHERMAP_API_KEY_HERE";
// Replace with your key:
var OWM_API_KEY = "abc123yourkeyhere";
```
> ⚠️ Without a valid key the weather buttons are hidden automatically. The map works fully without it.

3. Done — no other changes needed.

---

### SPtheme (optional)

If you are using [SPtheme](https://forum.phpvms.net/topic/...) the file goes in the same location. The map uses Bootstrap classes and is fully compatible. No additional changes needed.

---

## ⚙️ Configuration

All configurable values are at the top of the `<script>` block:

```javascript
// VATSIM refresh interval (minimum 15000 = 15s per VATSIM policy)
var VATSIM_REFRESH_MS = 30000;

// Default layer states on page load
var vatsimShowPilots  = false;   // true = Pilots visible on load
var vatsimShowCtrl    = true;    // true = Controllers visible on load
var vatsimShowSectors = false;   // true = FIR Sectors visible on load
```

---

## 🗺️ Data Sources

| Source | What for |
|--------|----------|
| [VATSIM Data API v3](https://data.vatsim.net/v3/vatsim-data.json) | Live pilots & controllers |
| [VATSpy Data](https://github.com/vatsimnetwork/vatspy-data-project) | Airport positions, FIR names & boundaries |
| [VATSIM Transceivers](https://data.vatsim.net/v3/transceivers-data.json) | Fallback controller positions |
| [OpenWeatherMap](https://openweathermap.org/api/weathermaps) | Weather tile overlays |
| phpVMS own database | Airline logos |

---

## 🙏 Credits

- **Weather overlay concept** based on [Weather Overlay on the Live Map](https://github.com/ncd200/Weather-Overlay-on-the-Live_Map) by **Rick Winkelman (Air Berlin Virtual)**
- VATSIM integration, popup redesign, badge system, route lines, TRACON merging: **German Sky Group**
- Built for [phpVMS 7](https://github.com/phpvms/phpvms)

---

## 📋 Requirements

- phpVMS 7 (any recent version)
- A web server serving over **HTTPS** (required for VATSIM API and weather tiles)
- Optional: Free [OpenWeatherMap API key](https://home.openweathermap.org/users/sign_up) for weather overlays

---

## 📄 License

MIT — free to use, modify and share. Credit appreciated but not required.
