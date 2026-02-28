# 🏠 HomeAssistant_Config

Custom **Home Assistant** config: heating with profile management, shutters, lights, remotes and dashboard.  
This folder is meant to be loaded from `configuration.yaml` (automations, scripts, dashboard).

---

## 📁 Structure

```
HomeAssistant_Config/
├── automations/
│   ├── index.yaml              # Automation list
│   ├── Heaters/                # Heating, profiles, valves, safety
│   ├── Shutters/               # Shutters & sunshade
│   ├── Lights/                 # Lights
│   ├── Remotes/                # Remote controls & PC
│   └── Sensors/                # Sensor-triggered automations
├── mappings/                   # Data-driven mappings by domain
│   ├── heaters/                # Profiles, thermostats, valves, windows
│   ├── shutters/               # Covers, triggers, window sensors
│   └── lights/                 # Toggle & scroll wheel mappings
├── dashboard/
│   ├── main.yaml               # Dashboard definition (views list)
│   ├── views/                  # Per-room & feature views
│   ├── cards/                  # Shared dashboard cards (weather, heating overview)
│   ├── rooms/                  # Per-room cards (overview, controls, heating, climate)
│   │   └── config.yaml         # Room metadata (name, icon, color)
│   └── templates/              # Reusable button-card templates
├── scripts.yaml
├── scenes.yaml
└── README.md
```

---

## ⚙️ Features

### 🔥 Heating (Heaters)

#### Profile management

The heating system is driven by a **profile** (`input_select.heating_profile`) resolved automatically with a priority chain, then applied to all thermostats. Manual overrides are detected and respected.

| Automation | Role |
|------------|------|
| **Heating profile resolver** | Resolves active profile via priority: collective off → `off`, away or outdoor temp > threshold → `eco`, night (23:00–sunrise+30 min) → `night`, default → `comfort`. Never overrides `boost`. |
| **Heating profile applier** | Applies profile temperatures to all thermostats (triggers on profile change + every 30 min). Skips unavailable thermostats and manually overridden rooms. |
| **Heating boost manager** | Starts a countdown timer on boost activation; reverts to `comfort` when timer ends. Cancels timer if boost is deactivated manually. |
| **Manual override detector** | Detects physical thermostat adjustments (delta > 0.1 °C from expected) and flags the room so the applier won't overwrite it. |

#### Hardware sync & safety

| Automation | Role |
|------------|------|
| **Heat sync – Global** | Syncs external temperature sensors to valves. Disables external sensor if sensor goes offline/invalid. Uses `mapping.yaml`. |
| **Heat sync – Living room & general thermostat** | Bidirectional sync between general thermostat and living room valve. Bumps general thermostat to max active target when living room is satisfied but other rooms still need heat. |
| **Collective heating master switch** | Checks daily if current date is within the configured heating season; turns `input_boolean.collective_heating` and all valves on/off accordingly. |
| **Climate range limiter** | Clamps thermostat setpoints to global min/max helpers to prevent unsafe temperatures. |
| **Climate hardware range limiter sync** | Pushes helper min/max limits to physical valve hardware when helpers change or collective heating turns on. |
| **Window open → valve off** | Turns off the associated valve when a window opens; restores temperature when closed. Handles SONOFF TRVZB open-window detection. Uses `window_valve_mapping.yaml`. |

#### Heater mappings (`mappings/heaters/`)

| File | Content |
|------|---------|
| `profiles.yaml` | Room ↔ thermostat mapping, per-room manual override booleans, temperature targets per profile (comfort, eco, night, boost, off). |
| `mapping.yaml` | Temperature sensor → valve external-sensor-control mapping. |
| `thermostats.yaml` | List of climate entities. |
| `valves.yaml` | List of valve switch entities. |
| `window_valve_mapping.yaml` | Window sensor → valve mapping (with optional open-window switch & room name). |
| `window_sensors.yaml` | List of window sensors. |
| `manual_overrides.yaml` | List of per-room manual override booleans. |

---

### 🪟 Shutters

| Automation | Role |
|------------|------|
| **Close – Global** | Closes all shutters 30 min after sunset, excluding covers whose window sensor is open. |
| **Wake UP – Time (Bedroom)** | Opens bedroom shutter at sunrise + 30 min (weekdays) or 11:00 (weekends), only if phone alarm > 5 h away. |
| **Wake UP – Sun (Office / Living / Kitchen)** | Opens office, living room and kitchen shutters 30 min after sunrise. |
| **Wake UP – Phone (Global)** | Opens all shutters 2 min before phone alarm. |

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
| **Bedroom switch – Light toggle** | Switch toggles ceiling lamp + LED strip. On bulb off: LED strip turns off after 60 s (cancelled if switch pressed again). |
| **Balcony lamp – French window** | Turns on when French window opens after sunset in winter (Nov–Mar); turns off when window closes. |

#### Light mappings (`mappings/lights/`)

| File | Content |
|------|---------|
| `toggle_mapping.yaml` | Button 3 → light/switch entities (per room). |
| `toggle_triggers.yaml` | Button 3 trigger entities. |
| `scroll_wheel_mapping.yaml` | Buttons 1 & 2 → dimmable lights. |
| `scroll_wheel_triggers.yaml` | Buttons 1 & 2 trigger entities. |

---

### 📡 Sensors

| Automation | Role |
|------------|------|
| **French window sensor** | Raises living room sunshade to 70 % when French window opens (if currently closed); lowers it when window closes at night. |

---

### 🖥️ Remotes

| Automation | Role |
|------------|------|
| **Button 3 – Light toggle (Global)** | Single press = toggle room lights. Double press = turn off all. |
| **Buttons 1 & 2 – Light scroll wheel (Global)** | Adjust brightness (8 presses = 90 % range, min 10 %). |
| **Button 6 – Shutter toggle (Global)** | If moving → stop; if any open → close all; if all closed → open all. |
| **Button 6 – Shutter hold (Global)** | Hold = move (up if all closed, down otherwise), release = stop. |
| **Buttons 4 & 5 – Shutter scroll wheel (Global)** | Adjust position (8 presses = 100 %, 12.5 % per press). |
| **Button 9 – Computer toggle (Office)** | Double press = toggle PC on/off (Wake-on-LAN or shutdown). |

---

## 🖥️ Dashboard

The dashboard is defined in `dashboard/main.yaml` and uses reusable **button-card templates**.

### Views

| View | Content |
|------|---------|
| **Home** | Weather overview, heating status (room temps, active profile, boost timer, collective heating), room overview cards. |
| **Living room / Office / Bedroom / Kitchen / Bathroom / Restroom / Balcony / Entry** | Per-room view with climate, controls (lights, covers, switches) and heating sections where applicable. |
| **Heating** | Profile selector, boost timer, outdoor threshold, collective heating status, seasonal dates, global limits, per-room temperature cards with manual override indicators. |
| **Maintenance** | Battery levels (auto-entities < 100 %), desktop PC control, calendar, person tracking. |

### Shared cards (`dashboard/cards/`)

| Card | Purpose |
|------|---------|
| `weather_card.yaml` | Weather overview for the Home view. |
| `heating_overview_card.yaml` | Heating status: room temps, active profile, boost timer, collective heating. |

### Templates (`dashboard/templates/`)

| Template | Purpose |
|----------|---------|
| `overview_card_templates.yaml` | Room card: temperature, door status, action buttons, heating & door indicators. |
| `thermostat_card_templates.yaml` | Thermostat control: current/target temp, +/− buttons, heating state indicator. |
| `climate_card_templates.yaml` | Climate display: temperature, humidity, 24 h graph placeholder. |

### Room config (`dashboard/rooms/config.yaml`)

Defines metadata for 8 rooms: **living_room**, **office**, **bedroom**, **kitchen**, **bathroom**, **restroom**, **balcony**, **entry** — each with name, icon and color.

---

## 📋 Scripts

| Script | Role |
|--------|------|
| **Toggle ON/OFF PC Fix** | Turns PC on (WoL) when off, otherwise triggers shutdown. |
| **Toggle Heating Boost** | Activates boost profile or reverts to comfort if already boosted. |

---

## 🚀 Usage

1. Include this folder in your Home Assistant config, e.g.:
   ```yaml
   automation: !include _HomeAssistant_Config/automations/index.yaml
   script: !include _HomeAssistant_Config/scripts.yaml
   ```
2. For the dashboard, use YAML mode and point to `dashboard/main.yaml`.
3. Restart Home Assistant or reload automations.

---

*Config maintained for home use with Home Assistant.*
