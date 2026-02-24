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
| **Bedroom - ON** | Turns on bed LED strip when bedroom ceiling light turns on. |
| **Bedroom - OFF** | Turns off bed LED strip when bedroom ceiling light turns off. |

### 📡 Sensors

| Automation | Role |
|------------|------|
| **French Window Living Room** | When french window opens: turn off heating, raise sunshade to 70% if closed, turn on balcony light if night. When closing: revert all. |

### 🖥️ Remotes / PC

| Automation | Role |
|------------|------|
| **Toggle computer** | Wakes desktop PC (Wake-on-LAN) or triggers shutdown. |
| **Scroll wheel - Light brightness (global)** | Bilresa remote buttons 1 & 2: decrease/increase light brightness (8 presses = 90% range, min 10%). |
| **Scroll wheel - Shutter incremental control (global)** | Bilresa remote scroll wheel: down = lower shutters, up = raise (8 presses = 100%). |

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
