# 🏠 HomeAssistant_Config

Custom **Home Assistant** config: heating with profile management, shutters, lights, Awtrix clock, remotes and dashboard.  
This folder is meant to be loaded from `configuration.yaml` (automations, scripts, helpers, dashboard).

---

## 📁 Structure

```
_HomeAssistant_Config/
├── automations/
│   ├── index.yaml              # Automation list (flat !include)
│   ├── Heaters/                # Heating, profiles, valves, safety
│   ├── Shutters/               # Shutters, sunshades & wind protection
│   ├── Lights/                 # Lights & switch bypass
│   ├── Remotes/                # Remote controls, PC & entry buttons
│   ├── Sensors/                # Sensor-triggered automations
│   ├── Awtrix/                 # Awtrix clock display automations
│   ├── Notifications/          # Mobile & persistent notifications
│   └── Vacuum/                 # Vacuum automations
├── dashboard/
│   ├── main.yaml               # Dashboard entry point (includes templates/ + views/)
│   ├── views/                  # Per-room & feature views
│   │   └── maintenance/        # Maintenance sub-views (batteries, system, roborock…)
│   ├── cards/                  # Shared dashboard cards
│   ├── chips/                  # Status chips (sleep, wind, presence, Bambu Lab…)
│   ├── rooms/                  # Per-room cards (overview + controls)
│   │   └── config.yaml         # Room metadata (name, icon, color, segment_id)
│   └── templates/              # Reusable button-card templates (auto-merged)
│       ├── overview_card_templates.yaml
│       ├── vacuum_card_templates.yaml
│       └── awtrix_card_templates.yaml
├── helpers/                    # All helpers defined in YAML (loaded via configuration.yaml)
│   ├── input_boolean.yaml
│   ├── input_number.yaml
│   ├── input_select.yaml
│   ├── input_datetime.yaml
│   └── timer.yaml
├── mappings/                   # Data-driven mappings by domain
│   ├── helpers.yaml            # Central index of all helper entity IDs
│   ├── heaters/                # Profiles, thermostats, valves, windows
│   ├── shutters/               # Covers, triggers, window sensors
│   ├── lights/                 # Toggle, scroll wheel & bypass mappings
│   ├── sensors/                # Water leak & window sensors
│   ├── awtrix/                 # Weather icon mappings
│   ├── matter_thread/          # Matter/Thread device list
│   └── vacuum/                 # Per-room vacuum selection booleans
├── scripts/                    # Scripts by domain (auto-merged via !include_dir_merge_named)
│   ├── vacuum.yaml             # Roborock segment cleaning
│   ├── heating.yaml            # Heating profile helpers
│   ├── awtrix.yaml             # Awtrix brightness & display helpers
│   ├── lights.yaml             # Light bypass resync
│   └── pc.yaml                 # PC toggle
├── documentation/              # Device datasheets & setup guides
│   ├── index.md
│   └── devices.md
└── scenes.yaml
```

---

## ⚙️ Features

### 🔥 Heating

#### Profile management

The heating system is driven by a **profile** (`input_select.heating_profile`) resolved automatically with a priority chain, then applied to all thermostats. Manual overrides are detected and respected.

| Automation | Role |
|------------|------|
| **Heating profile resolver** | Priority: collective off → `off`, away or outdoor temp > threshold → `eco`, night (23:00–sunrise+30 min) → `night`, default → `comfort`. Never overrides `boost`. |
| **Heating profile applier** | Applies profile temperatures to all thermostats (on profile change + every 30 min). Skips unavailable thermostats and manually overridden rooms. |
| **Heating boost manager** | Starts a countdown timer on boost activation; reverts to `comfort` when timer ends. |
| **Manual override detector** | Detects physical thermostat adjustments (delta > 0.1 °C) and flags the room so the applier won't overwrite it. |

#### Hardware sync & safety

| Automation | Role |
|------------|------|
| **Heat sync – Global** | Syncs external temperature sensors to valves. Disables external sensor if sensor goes offline/invalid. |
| **Heat sync – Living room & general thermostat** | Bidirectional sync between general thermostat and living room valve. |
| **Collective heating master switch** | Checks daily if current date is within the heating season; turns collective heating and all valves on/off. |
| **Climate range limiter** | Clamps thermostat setpoints to global min/max helpers. |
| **Climate hardware range limiter sync** | Pushes helper min/max limits to physical valve hardware when helpers change. |
| **Window open → valve off** | Turns off the associated valve when a window opens; restores temperature when closed. |

#### Heater mappings (`mappings/heaters/`)

| File | Content |
|------|---------|
| `profiles.yaml` | Room ↔ thermostat mapping, per-room manual override booleans, temperature targets per profile. |
| `mapping.yaml` | Temperature sensor → valve external-sensor-control mapping. |
| `sensors.yaml` | Temperature sensor → valve association. |
| `thermostats.yaml` | List of climate entities. |
| `valves.yaml` | List of valve switch entities. |
| `window_valve_mapping.yaml` | Window sensor → valve mapping. |
| `window_sensors.yaml` | List of window sensors. |
| `manual_overrides.yaml` | List of per-room manual override booleans. |

---

### 🪟 Shutters

| Automation | Role |
|------------|------|
| **Close – Global** | Closes all shutters 30 min after sunset, excluding covers whose window sensor is open. |
| **Close Living Room – Media Active** | Closes the living room shutter when TV media becomes active. |
| **Wake UP – Time (Bedroom)** | Opens bedroom shutter at sunrise + 30 min (weekdays) or 11:00 (weekends), only if phone alarm > 5 h away. |
| **Wake UP – Sun (Office / Living / Kitchen)** | Opens office, living room and kitchen shutters 30 min after sunrise. |
| **Wake UP – Phone (Global)** | Opens all shutters 2 min before phone alarm. |
| **Wind Alert** | Creates a persistent notification when wind speed exceeds 35 km/h; dismisses it when wind drops. |
| **Wind Protection – Raise / Enforce / Release** | Raises sunshades when wind protection is active; enforces on shutter movement; releases when wind drops. |

#### Shutter mappings (`mappings/shutters/`)

| File | Content |
|------|---------|
| `mapping.yaml` | Remote button → cover groups (per room). |
| `cover_window_sensors.yaml` | Cover → window sensor mapping (exclude from auto-close). |
| `toggle_triggers.yaml` | Button 6 toggle trigger entities. |
| `hold_triggers.yaml` | Button 6 hold trigger entities. |
| `scroll_wheel_triggers.yaml` | Buttons 4 & 5 trigger entities. |

---

### 💡 Lights

| Automation | Role |
|------------|------|
| **Switch bypass – Global** | Controls smart bulbs when a Shelly bypass switch is toggled. |
| **Switch bypass resync on start** | Resyncs bulb states to bypass switch states on HA restart. |
| **Bedroom switch – Light toggle** | Switch toggles ceiling lamp + LED strip. On bulb off: LED strip turns off after 60 s. |
| **Balcony lamp – French window** | Turns on when French window opens after sunset in winter (Nov–Mar); turns off when window closes. |
| **Streetlight ↔ Mushroom sync** | Syncs the mushroom lamp on/off with the streetlight outlet switch. |
| **Sleep mode detection ON** | All lights off for 30 min + all shutters closed + person home → sleep mode ON. |
| **Sleep mode detection OFF (backoff)** | Bedroom sunshade opens manually while sleep mode is ON → sleep mode OFF. |
| **Sleep mode apply** | ON → activates sleep mode on all Adaptive Lighting instances + turns off Awtrix. OFF → deactivates AL sleep + turns on Awtrix. |

#### Adaptive Lighting

Integration: `custom_components/adaptive_lighting`. Configured via UI (not YAML).

| Instance | Lights | Brightness | Color temp |
|----------|--------|------------|------------|
| Bedroom | Ceiling bulb + LED strip | 1–80 % | 2000–4000 K |
| Office | Ceiling bulb | 10–100 % | 2000–5500 K |
| Living Room | Ceiling + streetlight + mushroom bulbs | 1–100 % | 2000–5500 K |

Sleep mode: `input_boolean.sleep_mode` — detected automatically, toggleable from the maintenance dashboard.

#### Light mappings (`mappings/lights/`)

| File | Content |
|------|---------|
| `toggle_mapping.yaml` | Button 3 → light/switch entities (per room). |
| `toggle_triggers.yaml` | Button 3 trigger entities. |
| `scroll_wheel_mapping.yaml` | Buttons 1 & 2 → dimmable lights. |
| `scroll_wheel_triggers.yaml` | Buttons 1 & 2 trigger entities. |
| `switch_bypass_mapping.yaml` | Bypass switch → smart bulb mapping. |
| `switch_bypass_triggers.yaml` | Bypass switch trigger entities. |
| `all_lights.yaml` | Complete list of light entities (used by Awtrix power-off). |

---

### 🖱️ Remotes

| Automation | Role |
|------------|------|
| **Button 3 – Light toggle (Global)** | Single press = toggle room lights. Double press = turn off all. |
| **Buttons 1 & 2 – Light scroll wheel (Global)** | Adjust brightness (8 presses = 90 % range, min 10 %). |
| **Button 6 – Shutter toggle (Global)** | If moving → stop; if any open → close all; if all closed → open all. |
| **Button 6 – Shutter hold (Global)** | Hold = move (up if all closed, down otherwise), release = stop. |
| **Buttons 4 & 5 – Shutter scroll wheel (Global)** | Adjust position (12.5 % per press). |
| **Button 9 – Computer toggle (Office)** | Double press = toggle PC on/off (Wake-on-LAN or shutdown). |
| **Button 9 – 3D Printer outlet toggle (Office)** | Hold = toggle printer outlet on/off. Safeguard: refuses to power off while a print is running/paused/preparing. |
| **Entry – Button 1 – Leave Home** | Single/double/hold = turn off all lights + Awtrix + shutdown PC. Notifies if french window is open. |
| **Entry – Button 2 – Vacuum** | Single = vac bathroom; double = vac & mop bathroom; hold = vac & mop entire home. |

---

### 📡 Sensors

| Automation | Role |
|------------|------|
| **French window → sunshade** | Raises living room sunshade to 70 % when French window opens (if closed); lowers at night when window closes. |

---

### 🔔 Notifications

All notifications send to `notify.mobile_app_pixel_9_pro`.

| Automation | Trigger |
|------------|---------|
| **Water Leak Alert** | Water leak sensor detects water. |
| **Window Open Alert** | Window open for more than 30 minutes. |
| **AMS Humidity Alert** | AMS humidity stays above 20 % for 5 minutes. |
| **Toothbrush Battery Low** | Battery drops below 10 %. |
| **Bambu Lab Print Done** | Print finishes. |
| **Bambu Lab Print Error** | Print error detected. |
| **Matter/Thread Connectivity Lost** | Matter/Thread device unavailable for > 2 minutes. |

---

### 🕐 Awtrix Clock

The Awtrix clock (`light.clock_awtrix_matrix`) displays contextual apps and auto-adjusts brightness throughout the day.

| Automation | Role |
|------------|------|
| **Power On – Home** | Turns on Awtrix when arriving home or on HA restart if home. |
| **Power Off – Lights Out** | Turns off Awtrix after sunset when all lights have been off for 30 min. |
| **Power Off – Media Active** | Turns off Awtrix matrix when TV media becomes active (PS5, Plex, YouTube, Twitch). |
| **Power – Guest Mode** | Turns on when guest mode enables; turns off when guest mode disables and owner is away. |
| **Power On – Sunrise** | Turns on Awtrix at sunrise. |
| **Brightness – Time of Day** | Adjusts brightness in steps based on sun progress (8 % night → up to 90 % midday). |
| **Outdoor Weather** | Shows outdoor temperature with dynamic weather icon + day/night progress bar. Refreshes every 10 min. |
| **Living Room Temperature** | Shows living room temperature permanently. Refreshes every 5 min. |
| **Spotify** | Shows current track + playback progress bar when playing or paused. |
| **Vacuum** | Shows robot status and current room while cleaning; clears when docked. |
| **PS5 Game** | Shows current PS5 game title when the console is active. |
| **Bambu Lab Printing** | Shows 3D print progress and remaining time during a print. |
| **Heating Boost Countdown** | Shows remaining boost time on the clock. |

#### Awtrix scripts (`scripts/awtrix.yaml`)

| Script | Role |
|--------|------|
| **awtrix_apply_time_brightness** | Sets brightness based on sun progress through the day (6 paliers: 8/25/65/90/65/25 %). |

#### Awtrix mappings (`mappings/awtrix/`)

| File | Content |
|------|---------|
| `weather_icons.yaml` | Weather condition → Awtrix icon ID mapping. |

---

### 🤖 Vacuum

| Automation | Role |
|------------|------|
| **Vacuum – Deselect rooms when docked** | Turns off all room selection toggles when the robot docks. |

#### Vacuum mappings (`mappings/vacuum/`)

| File | Content |
|------|---------|
| `room_selects.yaml` | Per-room vacuum selection input_boolean entity list. |

---

### 🔌 Devices

| Automation | Role |
|------------|------|
| **3D Printer outlet auto-off** | Turns off `switch.office_3d_printer_outlet` after 3 h of `sensor.bambu_farm_printer_print_status` being `idle`/`finish`/`failed`. Ambiguous statuses (unknown/unavailable) never trigger a shutdown. |

---

## 🖥️ Dashboard

The dashboard is defined in `dashboard/main.yaml` and uses reusable **button-card templates** and **chips**.

### Views

| View | Content |
|------|---------|
| **Home** | Weather overview, heating status, chips row, room overview cards. |
| **Living Room / Office / Bedroom / Kitchen / Bathroom / Restroom / Balcony / Entry** | Per-room view with climate, controls (lights, covers, switches) and heating sections. |
| **Heating** | Profile selector, boost timer, outdoor threshold, collective heating status, per-room temperature cards. |
| **Maintenance** | Sub-views: batteries, system, PC, Roborock, Bambu Lab, Livebox, AdGuard, misc. |

### Chips (`dashboard/chips/`)

Status chips displayed on the home view (visible only when relevant):

`sleep_mode` · `wind` · `alban` (presence) · `guest_mode` · `roborock_cleaning` · `roborock_water` · `bambulab_printing` · `ams_humidity` · `spotify` · `plex` · `ps5` · `steam` · `pc` · `battery` · `maintenance`

### Shared cards (`dashboard/cards/`)

| Card | Purpose |
|------|---------|
| `weather_card.yaml` | Weather overview. |
| `heating_overview_card.yaml` | Heating status: room temps, active profile, boost timer, collective heating. |
| `balcony_weather_card.yaml` | Balcony/outdoor weather card. |
| `pc_power_card.yaml` | Desktop PC power controls. |
| `nas_card.yaml` | NAS status card. |
| `bambulab_card.yaml` | Bambu Lab printer status card. |

### Templates (`dashboard/templates/`)

| Template file | Templates defined |
|---------------|-------------------|
| `overview_card_templates.yaml` | `room_card`, `room_card_action` |
| `vacuum_card_templates.yaml` | `vacuum_room_card`, `vacuum_btn` |
| `awtrix_card_templates.yaml` | `awtrix_card`, `awtrix_btn` |

### Room config (`dashboard/rooms/config.yaml`)

| room_id | Name | Icon | Color | segment_id |
|---------|------|------|-------|------------|
| `living_room` | Living Room | `mdi:sofa-outline` | #4CAF50 | 6 |
| `office` | Office | `mdi:microsoft-office` | #C9A96E | 2 |
| `bedroom` | Bedroom | `mdi:bed-outline` | #B55233 | 7 |
| `kitchen` | Kitchen | `mdi:coffee-maker-outline` | #E6B800 | 5 |
| `bathroom` | Bathroom | `mdi:bathtub-outline` | #45B5C4 | 4 |
| `restroom` | Restroom | `mdi:paper-roll-outline` | #D4759E | 3 |
| `balcony` | Balcony | `mdi:balcony` | #8BC34A | — |
| `entry` | Entry | `mdi:door-open` | #78909C | 1 |

---

## 📋 Scripts

Scripts are split by domain in `scripts/` and auto-loaded via `!include_dir_merge_named`.

### `vacuum.yaml` — Roborock segment cleaning

Each script takes `segment_id` (int) as input:

| Script | Role |
|--------|------|
| **vacuum_clean_segment** | Sets mop off, vacuums the segment. |
| **vacuum_mop_segment** | Sets fan off + mop on, mops the segment. |
| **vacuum_clean_mop_segment** | Sets fan max + mop on, cleans & mops the segment. |

### `heating.yaml`

| Script | Role |
|--------|------|
| **Toggle Heating Boost** | Activates boost profile or reverts to comfort if already boosted. |
| **Reset Heating Profile** | Clears manual overrides and re-applies current profile temperatures. |

### `awtrix.yaml`

| Script | Role |
|--------|------|
| **awtrix_apply_time_brightness** | Sets Awtrix brightness based on sun progress through the day. |

### `lights.yaml`

| Script | Role |
|--------|------|
| **light_bypass_resync** | Forces bulb states to match their bypass switch states (bedroom, living room, office). |

### `pc.yaml`

| Script | Role |
|--------|------|
| **Toggle ON/OFF PC Fix** | Turns PC on (WoL) when off, otherwise triggers shutdown. |

---

## 🧩 Helpers

All helpers are defined in YAML under `helpers/` and loaded via `configuration.yaml`. Never create helpers via the UI.

| File | Integration key |
|------|----------------|
| `helpers/input_boolean.yaml` | `input_boolean` |
| `helpers/input_number.yaml` | `input_number` |
| `helpers/input_select.yaml` | `input_select` |
| `helpers/input_datetime.yaml` | `input_datetime` |
| `helpers/timer.yaml` | `timer` |

---

## 🚀 Usage

1. Include this folder in your Home Assistant config:
   ```yaml
   automation: !include _HomeAssistant_Config/automations/index.yaml
   script: !include_dir_merge_named _HomeAssistant_Config/scripts/
   input_boolean: !include _HomeAssistant_Config/helpers/input_boolean.yaml
   input_number: !include _HomeAssistant_Config/helpers/input_number.yaml
   input_select: !include _HomeAssistant_Config/helpers/input_select.yaml
   input_datetime: !include _HomeAssistant_Config/helpers/input_datetime.yaml
   timer: !include _HomeAssistant_Config/helpers/timer.yaml
   ```
2. For the dashboard, use YAML mode and point to `dashboard/main.yaml`.
3. Restart Home Assistant or reload automations/helpers as needed.

---

*Config maintained for home use with Home Assistant.*
