# Home Assistant Config — CLAUDE.md

## Project Structure

```
_HomeAssistant_Config/
├── automations/          # YAML automations organized by category
│   ├── Heaters/          # Heating automations
│   ├── Shutters/         # Shutter automations
│   ├── Lights/           # Light automations
│   ├── Remotes/          # Remote control automations
│   ├── Sensors/          # Sensor-driven automations (window → shutter)
│   ├── Awtrix/           # Awtrix clock automations
│   ├── Notifications/    # Notification automations — named <subject>.yaml (e.g. heating_profile.yaml, window_open.yaml)
│   ├── Vacuum/           # Vacuum automations
│   └── index.yaml        # Flat !include list (HA does not auto-merge subdirs here)
├── dashboard/
│   ├── main.yaml         # Dashboard entry point (includes templates/ + views/)
│   ├── templates/        # Reusable button-card templates (auto-merged via !include_dir_merge_named)
│   │   ├── overview_card_templates.yaml   # room_card, room_card_action
│   │   ├── vacuum_card_templates.yaml     # vacuum_room_card, vacuum_btn
│   │   └── awtrix_card_templates.yaml     # awtrix_card, awtrix_btn
│   ├── views/            # One file per dashboard page
│   └── rooms/            # Per-room cards, included from views
│       ├── config.yaml   # Centralized room metadata (name, icon, color, segment_id) keyed by room_id
│       └── <room>/
│           ├── overview_card.yaml  # Summary card used in home.yaml
│           └── controls.yaml       # Bubble cards (lights, shutters, vacuum…)
├── mappings/
│   ├── heaters/          # Profiles, thermostats, valves, window sensors
│   ├── lights/           # Light/switch mappings (remote toggles, bypass switches)
│   └── shutters/         # Remote → shutter entities mapping
├── scripts/              # Scripts organized by domain (auto-merged via !include_dir_merge_named)
│   ├── vacuum.yaml       # Roborock segment cleaning scripts
│   ├── heating.yaml      # Heating profile helpers
│   └── pc.yaml           # PC toggle
└── scenes.yaml
```

## Dashboard

### Available Views
- `home` — Home: weather, heating profile, summary cards for all rooms
- `living_room`, `office`, `bedroom`, `kitchen`, `bathroom`, `restroom`, `balcony` — Per-room views
- `heating` — Centralized heating control
- `maintenance` — Batteries, PC, calendar

### Adding a New Room
1. Create `dashboard/rooms/<room_id>/overview_card.yaml` using the `room_card` template
2. Add the entry to `dashboard/rooms/config.yaml` (name, icon, color)
3. Include the card in `dashboard/views/home.yaml` (Rooms section)
4. Optional: create a dedicated view in `dashboard/views/<room>.yaml`

### `room_card` Template (overview_card.yaml)
```yaml
type: custom:button-card
template: room_card
name: <Name>
variables:
  name: <Name>
  room_id: <room_id>           # Must match the key in config.yaml
  light_entity: <entity_id>   # Main entity (determines card on/off state)
  sub_state: ""                # Secondary text (Jinja/JS)
  actions_columns:
    - - entity: <entity_id>
        icon: mdi:<icon>
```

## Existing Rooms

Source of truth: `dashboard/rooms/config.yaml`. The icon and color defined here must be used consistently in `overview_card.yaml`, view headers (`dashboard/views/<room>.yaml`), and any card referencing that room.

| room_id      | Name        | Icon                     | Color     | segment_id |
|--------------|-------------|--------------------------|-----------|------------|
| living_room  | Living Room | mdi:sofa-outline         | #4CAF50   | 6          |
| office       | Office      | mdi:microsoft-office     | #C9A96E   | 2          |
| bedroom      | Bedroom     | mdi:bed-outline          | #B55233   | 7          |
| kitchen      | Kitchen     | mdi:coffee-maker-outline | #E6B800   | 5          |
| bathroom     | Bathroom    | mdi:bathtub-outline      | #45B5C4   | 4          |
| restroom     | Restroom    | mdi:paper-roll-outline   | #D4759E   | 3          |
| balcony      | Balcony     | mdi:balcony              | #8BC34A   | —          |
| entry        | Entry       | mdi:door-open            | #78909C   | 1          |

## Heating System

### Profiles (`input_select.heating_profile`)
`comfort` / `eco` / `night` / `boost` / `off`

### Automatic Profile Resolution
Priority order (automation `heating_profile_resolver`):
1. Collective heating OFF → `off`
2. Person away OR outdoor temp > threshold → `eco`
3. Nighttime hours → `night`
4. Default → `comfort`

### Heating Entities per Room
- Thermostat: `climate.<room>_thermostatic_valve_thermostat`
- Temp sensor: `sensor.<room>_thermometer_temperature`
- Humidity sensor: `sensor.<room>_thermometer_humidity`
- HVAC action: `sensor.<room>_thermostatic_valve_hvac_action`
- Manual override: `input_boolean.heating_manual_<room>`

### Heated Rooms: `bathroom`, `bedroom`, `office`, `living_room`

### Global Helpers
- `input_number.heating_global_min` (default 16°C)
- `input_number.heating_global_max` (default 23°C)
- `input_number.heating_outdoor_threshold` (default 22°C)
- `input_boolean.collective_heating`
- `timer.heating_boost`

## Adaptive Lighting

Integration: `custom_components/adaptive_lighting` (v1.30.1)
Configured via UI (Settings → Devices & Services), not YAML.

### Instances

| Instance | Lights | Min/Max brightness | Min/Max color temp |
|----------|--------|--------------------|--------------------|
| Bedroom | `light.bedroom_ceiling_lamp_bulb`, `light.bedroom_bed_led_strip` | 1% / 80% | 2000K / 4000K |
| Office | `light.office_ceiling_lamp_bulb` | 10% / 100% | 2000K / 5500K |
| Living Room | `light.living_room_ceiling_lamp_bulb`, `light.living_room_streetlight_bulb`, `light.living_room_mushroom_lamp_bulb` | 1% / 100% | 2000K / 5500K |

Common settings on all instances: `initial_transition: 3s`, `take_over_control: true`, `adapt_only_on_bare_turn_on: true`, `sunrise_offset: +3600s`, `sunset_offset: -3600s`

Bedroom sleep settings: `sleep_brightness: 1%`, `sleep_color_temp: 1000K`

### Sleep Mode

Single source of truth: `input_boolean.sleep_mode` (defined in `configuration.yaml`)

**Detection ON** (`automations/Lights/sleep_mode_detection_on.yaml`):
All lights off for 30 min + all shutters closed + person home → `input_boolean.sleep_mode` ON

**Detection OFF — Primary** (`automations/Shutters/wake_up_time_bedroom.yaml`, `wake_up_phone_global.yaml`):
Wake-up automations turn off `input_boolean.sleep_mode` directly.

**Detection OFF — Backoff** (`automations/Lights/sleep_mode_detection_off.yaml`):
Bedroom sunshade opens manually while sleep mode is still ON → `input_boolean.sleep_mode` OFF

**Apply** (`automations/Lights/sleep_mode_apply.yaml`):
Reacts to `input_boolean.sleep_mode` state change:
- ON → activates sleep mode on all 3 AL instances + turns off Awtrix
- OFF → deactivates sleep mode on all 3 AL instances + turns on Awtrix (`script.awtrix_apply_time_brightness`)

**Dashboard**: chip `dashboard/chips/sleep_mode.yaml` (visible on home page when active), toggle button in `maintenance/misc.yaml`

## Remote Control Mappings

Remotes have numbered buttons (1, 2, 3, 4, 5, 6, 9):
- Buttons 1/2 → brightness scroll wheel (`mappings/lights/scroll_wheel_mapping.yaml`) — bypass rooms only (bedroom, living room, office)
- Button 3 → toggle lights (`mappings/lights/toggle_mapping.yaml`)
- Buttons 4/5 → scroll shutters (`mappings/shutters/mapping.yaml`)
- Button 6 → toggle/hold shutters
- Button 9 (office) → toggle PC

Triggers are defined in `*_triggers.yaml` files in the same folder.

## Remotes per Room
- `event.office_remote_button_*`
- `event.living_room_remote_button_*`
- `event.bedroom_remote_button_*`

## Devices by Room

### Living Room
| Entity | Description |
|--------|-------------|
| `switch.living_room_ceiling_lamp_switch` | Ceiling lamp switch (SONOFF, Shelly bypass in fixture) |
| `light.living_room_ceiling_lamp_bulb` | Ceiling lamp bulb (smart, color temp) — always powered, controlled via bypass automation |
| `switch.living_room_outlet_switch` | Outlet switch (floor lamp) |
| `light.living_room_streetlight_bulb` | Streetlight bulb (smart, color temp) |
| `light.living_room_mushroom_lamp_bulb` | Mushroom lamp (smart, color temp) |
| `cover.living_room_shutter` | Window shutter |
| `cover.living_room_sunshade` | Sunshade |
| `binary_sensor.living_room_french_window` | French window sensor |
| `climate.living_room_thermostatic_valve_thermostat` | Thermostat |
| `sensor.living_room_thermometer_temperature` | Temperature sensor |
| `sensor.living_room_thermometer_humidity` | Humidity sensor |
| `sensor.living_room_thermostatic_valve_hvac_action` | HVAC action |
| `input_boolean.heating_manual_living_room` | Manual heating override |
| `event.living_room_remote_button_3/4/5/6` | Remote (lights / shutters) |

### Office
| Entity | Description |
|--------|-------------|
| `switch.office_ceiling_lamp_switch` | Ceiling lamp switch (SONOFF, Shelly bypass in fixture) |
| `light.office_ceiling_lamp_bulb` | Ceiling lamp bulb (smart, color temp) — always powered, controlled via bypass automation |
| `cover.office_sunshade` | Sunshade |
| `climate.office_thermostatic_valve_thermostat` | Thermostat |
| `sensor.office_thermometer_temperature` | Temperature sensor |
| `sensor.office_thermometer_humidity` | Humidity sensor |
| `sensor.office_thermostatic_valve_hvac_action` | HVAC action |
| `input_boolean.heating_manual_office` | Manual heating override |
| `event.office_remote_button_3/4/5/6/9` | Remote (lights / shutters / PC) |

### Bedroom
| Entity | Description |
|--------|-------------|
| `switch.bedroom_ceiling_lamp_switch` | Ceiling lamp switch (SONOFF, Shelly bypass in fixture) |
| `light.bedroom_ceiling_lamp_bulb` | Ceiling lamp bulb (smart, color temp) — always powered, controlled via bypass automation |
| `light.bedroom_bed_led_strip` | Bed LED strip |
| `cover.bedroom_sunshade` | Sunshade |
| `climate.bedroom_thermostatic_valve_thermostat` | Thermostat |
| `sensor.bedroom_thermometer_temperature` | Temperature sensor |
| `sensor.bedroom_thermometer_humidity` | Humidity sensor |
| `sensor.bedroom_thermostatic_valve_hvac_action` | HVAC action |
| `input_boolean.heating_manual_bedroom` | Manual heating override |
| `event.bedroom_remote_button_3/4/5/6` | Remote (lights / shutters) |

### Kitchen
| Entity | Description |
|--------|-------------|
| `switch.kitchen_ceiling_lamp_switch` | Ceiling lamp switch |
| `switch.kitchen_under_cabinet_lighting_switch` | Under cabinet lighting |
| `cover.kitchen_shutter` | Window shutter |
| `binary_sensor.kitchen_water_leak_sensor_water_leak` | Water leak sensor |

### Bathroom
| Entity | Description |
|--------|-------------|
| `switch.bathroom_ceiling_lamp_switch` | Ceiling lamp switch |
| `switch.bathroom_mirror_switch` | Mirror light switch |
| `climate.bathroom_thermostatic_valve_thermostat` | Thermostat |
| `sensor.bathroom_thermometer_temperature` | Temperature sensor |
| `sensor.bathroom_thermometer_humidity` | Humidity sensor |
| `sensor.bathroom_thermostatic_valve_hvac_action` | HVAC action |
| `input_boolean.heating_manual_bathroom` | Manual heating override |
| `binary_sensor.bathroom_water_leak_sensor_water_leak` | Water leak sensor |

### Restroom
| Entity | Description |
|--------|-------------|
| `switch.restroom_ceiling_lamp_switch` | Ceiling lamp switch |
| `binary_sensor.restroom_water_leak_sensor_water_leak` | Water leak sensor |

### Balcony
| Entity | Description |
|--------|-------------|
| `switch.balcony_outside_lamp` | Outside lamp |

### Entry
| Entity | Description |
|--------|-------------|
| `light.entry_telecontrol_switch` | Entry light (smart) |

## System & Global Entities

### Heating
| Entity | Description |
|--------|-------------|
| `climate.general_thermostat` | General thermostat |
| `input_select.heating_profile` | Active profile (comfort/eco/night/boost/off) |
| `input_boolean.collective_heating` | Collective heating on/off |
| `timer.heating_boost` | Boost timer |
| `input_number.heating_global_min` | Global min temperature (default 16°C) |
| `input_number.heating_global_max` | Global max temperature (default 23°C) |
| `input_number.heating_outdoor_threshold` | Outdoor threshold for eco (default 22°C) |
| `input_number.heating_boost_duration` | Boost duration in minutes |
| `input_datetime.collective_heating_start_date` | Collective heating season start |
| `input_datetime.collective_heating_end_date` | Collective heating season end |

### System
| Entity | Description |
|--------|-------------|
| `person.alban_pierson` | Presence detection |
| `weather.forecast_home` | Weather |
| `sun.sun` | Sunrise / sunset |
| `sensor.pixel_9_pro_next_alarm` | Phone alarm |
| `switch.alban_pc_fixe` | Desktop PC power |
| `button.hass_pc_fixe_shutdown` | Desktop PC shutdown |
| `sensor.hass_pc_fixe_cpuload` | Desktop PC CPU load |
| `button.clock_awtrix_dismiss_notification` | Awtrix clock — dismiss notification (command) |
| `button.clock_awtrix_next_app` | Awtrix clock — next app (command) |
| `button.clock_awtrix_previous_app` | Awtrix clock — previous app (command) |
| `binary_sensor.clock_awtrix_button_select` | Awtrix clock — center button physical press |
| `binary_sensor.clock_awtrix_button_left` | Awtrix clock — left button physical press |
| `binary_sensor.clock_awtrix_button_right` | Awtrix clock — right button physical press |
| `sensor.clock_awtrix_current_app` | Awtrix clock — currently displayed app |
| `light.clock_awtrix_matrix` | Awtrix clock — matrix power (on/off) |
| `light.clock_awtrix_indicator_1` | Awtrix clock — top-right indicator dot |
| `light.clock_awtrix_indicator_2` | Awtrix clock — right indicator dot |
| `light.clock_awtrix_indicator_3` | Awtrix clock — bottom-right indicator dot |
| `select.clock_awtrix_brightness_mode` | Awtrix clock — brightness mode (Manual/Auto) |
| `sensor.clock_awtrix_illuminance` | Awtrix clock — ambient light sensor (lx) |
| `sensor.clock_awtrix_temperature` | Awtrix clock — internal temperature (°C) |
| `sensor.clock_awtrix_humidity` | Awtrix clock — internal humidity (%) |

### Awtrix Clock
- MQTT prefix: `awtrix_6f0891c`
- Notification topic: `awtrix_6f0891c/notify`
- Power control: `POST http://192.168.1.25/api/power` with `{"power": true/false}` or MQTT topic `awtrix_6f0891c/power`
- Payload format: `{"text": "...", "icon": "<id>", "color": "#RRGGBB", "duration": <seconds>}`
- Custom app topic: `awtrix_6f0891c/custom/<app_name>`

## Icon Mapping (Light entities)

| Entity type              | MDI icon                      |
|--------------------------|-------------------------------|
| Ceiling lamp (all rooms) | `mdi:ceiling-light-outline`   |
| Streetlight (living room)| `mdi:floor-lamp-torchiere`    |
| Balcony outside lamp     | `mdi:coach-lamp-variant`      |
| Under cabinet (kitchen)  | `mdi:dome-light`              |

## Vacuum (Roborock Qrevo Edge Series)

### Segment IDs
Stored in `dashboard/rooms/config.yaml` under `segment_id`. See Existing Rooms table above.

### Scripts (`scripts/vacuum.yaml`)
Each script takes `segment_id` (int) as a field:
- `vacuum_clean_segment` — sets mop off, vacuums segment
- `vacuum_mop_segment` — sets fan off + mop on, mops segment
- `vacuum_clean_mop_segment` — sets fan max + mop on, cleans & mops segment

### `vacuum_room_card` Template
Defined in `dashboard/templates/vacuum_card_templates.yaml`. Reads `segment_id` from `room_configs[room_id]`.

Usage in `controls.yaml`:
```yaml
- type: custom:button-card
  template: vacuum_room_card
  variables:
    room_id: office   # Must match key in rooms/config.yaml
```

To add vacuum to a new room: add `segment_id` in `rooms/config.yaml` and add the card above to the room's `controls.yaml`.

### Key Entities
- `vacuum.roborock_qrevo_edge_series`
- `sensor.roborock_qrevo_edge_series_status`
- `sensor.roborock_qrevo_edge_series_current_room` (options: Entry, Office, Restroom, Bathroom, Kitchen, Living room, Bedroom)
- `sensor.roborock_qrevo_edge_series_vacuum_error`
- `select.roborock_qrevo_edge_series_mop_intensity`

## Scripts

Scripts are split by domain in `scripts/` and auto-loaded via `!include_dir_merge_named`. To add a new group, create a new file — no index needed.

## Conventions
- Automations use numeric IDs (`id: "1740500000001"`)
- YAML anchors (`&alias` / `*alias`) are used in mappings to avoid duplication
- Button-card templates use inline JavaScript in `custom_fields`
- Dashboard navigation path: `/overview-dashboard/<path>`
- Button-card templates are split by feature in `dashboard/templates/` (auto-merged)
- Scripts are split by domain in `scripts/` (auto-merged via `!include_dir_merge_named`)
