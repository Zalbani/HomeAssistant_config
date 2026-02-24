# 🏠 HomeAssistant_Config

Custom **Home Assistant** config: heating, shutters, lights, wake-up and PC control.  
This folder is meant to be loaded from `configuration.yaml` (automations, scripts, scenes).

---

## 📁 Structure

```
HomeAssistant_Config/
├── automations/
│   ├── index.yaml          # Automation list
│   ├── Heaters/            # Heating & valves
│   ├── Shutters/           # Shutters & Sunshade
│   ├── Lights/             # Lights
│   ├── Remotes/            # Remotes / PC
│   └── Sensors/            # Sensor-triggered automations
├── mappings/               # Mappings by domain
│   ├── heaters/           # Heating (valves, sensors)
│   ├── shutters/          # Remote shutter control
│   └── lights/            # Remote light control
├── dashboard/
│   └── overview.yaml      # Lovelace dashboard (YAML)
├── scripts.yaml
├── scenes.yaml
└── README.md
```

---

## ⚙️ Features

### 🔥 Heating (Heaters)

| Automation | Role |
|------------|------|
| **Heat sync - Global** | Syncs valves with thermometers (bathroom, bedroom, office, living room); handles external sensor mode and safety when sensor is unavailable. |
| **Collective Heating Master Switch** | Manages heating season: turns collective heating and valves on/off by date. |
| **Climate - Range limiter** | Limits setpoint (min/max) to avoid extremes. |
| **Climate - Hardware range limiter Sync** | Updates min/max limits on thermostat hardware. |
| **Heat - Sync Living room & general thermostat** | Two-way sync between living room valve and general thermostat. |
| **Window open cuts valve** | When a window opens, turns off the associated valve. When closed, turns it back on. Mapping in `mappings/heaters/window_valve_mapping.yaml`. |

### 🪟 Shutters

| Automation | Role |
|------------|------|
| **Close - Global** | Global close for shutters and Sunshade. |
| **Wake UP - Time (Bedroom)** | Opens bedroom shutters at wake-up time (alarm > 5 h). |
| **Wake UP - Sun (Office / Living / Kitchen)** | Opens office, living room and kitchen shutters based on sun. |
| **Wake UP - Phone (Global)** | Opens based on phone alarm (within 2 minutes). |

### 💡 Lights

| Automation | Role |
|------------|------|
| **Light sync - Bedroom - Switch toggle** | Switch toggles bulb + LED strip. Bulb off immediately, LED strip after 1 min (cancelled if switch pressed again). |
| **Balcony outside lamp (french window)** | Turns on when french window opens (or is already open) 30 min after sunset in winter. Turns off when window closes. |

### 📡 Sensors

| Automation | Role |
|------------|------|
| **Living Room Sunshade - Window control** | Manages sunshade position based on french window open/close. Heating and balcony light in separate automations. |

### 🖥️ Remotes / PC

| Automation | Role |
|------------|------|
| **Remotes - Office - Button 9 - Computer toggle** | Double press = toggle PC on/off (Wake-on-LAN or shutdown). |
| **Remotes - Global - Button 3 - Light toggle** | Single press = toggle lights on/off. Double press = turn off all. |
| **Remotes - Global - Buttons 1 & 2 - Light scroll wheel brightness** | Decrease/increase brightness (8 presses = 90% range, min 10%). |
| **Remotes - Global - Button 6 - Shutter toggle** | Toggle: stop → close all → open all. |
| **Remotes - Global - Button 6 - Shutter hold** | Hold = move, release = stop. |
| **Remotes - Global - Buttons 4 & 5 - Shutter scroll wheel** | Down = lower, up = raise (8 presses = 100%). |

---

## 📋 Scripts

- **Toggle ON/OFF PC Fix**: turns PC on (WoL) when off, otherwise triggers shutdown.

---

## 🚀 Usage

1. Include this folder in your Home Assistant config, e.g.:
   ```yaml
   automation: !include _HomeAssistant_Config/automations/index.yaml
   script: !include _HomeAssistant_Config/scripts.yaml
   scene: !include _HomeAssistant_Config/scenes.yaml
   ```
2. Restart Home Assistant or reload automations.

---

*Config maintained for home use with Home Assistant.*
