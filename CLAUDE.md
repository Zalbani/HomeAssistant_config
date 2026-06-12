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
├── binary_sensors/       # REST/TCP binary sensors (auto-merged via !include_dir_merge_list)
│   ├── arr_stack.yaml    # Seerr, Prowlarr, Sonarr, Radarr, Bazarr — authenticated REST health checks
│   └── arr_updates.yaml  # Prowlarr, Sonarr, Radarr, Seerr — update detection (device_class: update, 24h poll)
├── sensors/              # REST sensors (auto-merged via !include_dir_merge_list)
│   ├── arr_stack.yaml    # VPN IP + VPN Status (Gluetun, basic auth)
│   ├── arr_versions.yaml # Prowlarr/Sonarr/Radarr/Seerr/Bazarr versions (24h poll)
│   └── seerr.yaml        # Seerr request counts with breakdown attributes (movie/tv/pending/processing…)
├── dashboard/
│   ├── main.yaml         # Dashboard entry point (includes templates/ + views/)
│   ├── templates/        # Reusable button-card templates (auto-merged via !include_dir_merge_named)
│   │   ├── overview_card_templates.yaml   # room_card, room_card_action
│   │   ├── vacuum_card_templates.yaml     # vacuum_room_card, vacuum_btn
│   │   └── awtrix_card_templates.yaml     # awtrix_card, awtrix_btn
│   ├── views/            # One file per dashboard page
│   │   └── maintenance/  # Maintenance subview sections (each file = one section)
│   └── rooms/            # Per-room cards, included from views
│       ├── config.yaml   # Centralized room metadata (name, icon, color, segment_id) keyed by room_id
│       └── <room>/
│           ├── overview_card.yaml  # Summary card used in home.yaml
│           └── controls.yaml       # Bubble cards (lights, shutters, vacuum…)
├── helpers/              # All helpers defined in YAML (loaded via configuration.yaml)
│   ├── input_boolean.yaml   # Booleans: sleep_mode, guest_mode, wind, heating, vacuum selects
│   ├── input_number.yaml    # Numbers: heating global min/max, outdoor threshold, boost duration
│   ├── input_select.yaml    # Selects: heating_profile
│   ├── input_datetime.yaml  # Datetimes: collective heating season start/end
│   └── timer.yaml           # Timers: heating_boost
├── mappings/
│   ├── helpers.yaml      # Central index of all helper entity IDs (importable via !include)
│   ├── heaters/          # Profiles, thermostats, valves, window sensors
│   ├── lights/           # Light/switch mappings (remote toggles, bypass switches)
│   └── shutters/         # Remote → shutter entities mapping
├── templates/            # Template sensors/binary_sensors (auto-merged via !include_dir_merge_list)
│   ├── system.yaml       # binary_sensor: PC Online, TV Media Active
│   ├── scale.yaml        # sensor: body composition (Segal formula — male, 28 y/o, 173 cm)
│   └── sun_exposure.yaml # binary_sensor: West Facade Sun Exposure (sun azimuth/elevation + weather + outdoor temp)
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
- `maintenance` — Batteries, PC, NAS, Livebox, AdGuard, Stack Arr (VPN + services), Seerr, qBittorrent, BambuLab

### Standalone Cards (`dashboard/cards/`)
Reusable single-card definitions (start with `type:`). Included as items inside a section's `cards:` list, or as the sole card of a dedicated section.

| File | Used in | Description |
|------|---------|-------------|
| `scale_card.yaml` | `views/bathroom.yaml` (dedicated section) | Scale + body composition — `type: vertical-stack` |
| `heating_overview_card.yaml` | `views/home.yaml` | Heating profile + room temps |
| `bambulab_card.yaml` | `views/maintenance/` | BambuLab printer status |
| `nas_card.yaml` | `views/maintenance/system.yaml` | NAS status |
| `pc_power_card.yaml` | `views/maintenance/system.yaml` | PC power control |
| `weather_card.yaml` | `views/home.yaml` | Weather summary |
| `balcony_weather_card.yaml` | `views/balcony.yaml` | Balcony weather |

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
| Entry | `light.entry_ceiling_lamp_flat_led` | 10% / 100% | 2000K / 5500K |

Common settings on all instances: `initial_transition: 3s`, `take_over_control: true`, `adapt_only_on_bare_turn_on: true`, `sunrise_offset: +3600s`, `sunset_offset: -3600s`

Bedroom sleep settings: `sleep_brightness: 1%`, `sleep_color_temp: 1000K`

### Sleep Mode

Single source of truth: `input_boolean.sleep_mode` (defined in `helpers/input_boolean.yaml`)

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

## Helpers

All helpers are defined in YAML under `helpers/` and loaded in `configuration.yaml`. **Never create helpers via the UI** — add them to the appropriate file and reload HA.

| File | Integration key | Reload via |
|------|----------------|------------|
| `helpers/input_boolean.yaml` | `input_boolean` | Developer Tools → YAML → Input Boolean |
| `helpers/input_number.yaml` | `input_number` | Developer Tools → YAML → Input Number |
| `helpers/input_select.yaml` | `input_select` | Developer Tools → YAML → Input Select |
| `helpers/input_datetime.yaml` | `input_datetime` | Developer Tools → YAML → Input Datetime |
| `helpers/timer.yaml` | `timer` | Developer Tools → YAML → Timer |

### Complete helper inventory

| Entity | File | Purpose |
|--------|------|---------|
| `input_boolean.sleep_mode` | `input_boolean.yaml` | Sleep mode active |
| `input_boolean.guest_mode` | `input_boolean.yaml` | Guest mode active (Awtrix, heating, shutters) |
| `input_boolean.wind_protection_active` | `input_boolean.yaml` | Wind protection lock on shutters |
| `input_boolean.collective_heating` | `input_boolean.yaml` | Season master switch |
| `input_boolean.heating_manual_bathroom` | `input_boolean.yaml` | Manual override — bathroom |
| `input_boolean.heating_manual_bedroom` | `input_boolean.yaml` | Manual override — bedroom |
| `input_boolean.heating_manual_living_room` | `input_boolean.yaml` | Manual override — living room |
| `input_boolean.heating_manual_office` | `input_boolean.yaml` | Manual override — office |
| `input_boolean.vacuum_select_*` | `input_boolean.yaml` | Per-room vacuum selection (7 rooms) |
| `input_number.heating_global_min` | `input_number.yaml` | Global heating min (°C) |
| `input_number.heating_global_max` | `input_number.yaml` | Global heating max (°C) |
| `input_number.heating_outdoor_threshold` | `input_number.yaml` | Outdoor temp → eco profile (°C) |
| `input_number.heating_boost_duration` | `input_number.yaml` | Boost duration (min) |
| `input_number.sun_protection_outdoor_threshold` | `input_number.yaml` | Outdoor temp above which west-facade sun protection triggers (°C) |
| `input_select.heating_profile` | `input_select.yaml` | Active profile: comfort/eco/night/boost/off |
| `input_datetime.collective_heating_start_date` | `input_datetime.yaml` | Season start date |
| `input_datetime.collective_heating_end_date` | `input_datetime.yaml` | Season end date |
| `timer.heating_boost` | `timer.yaml` | Boost countdown timer |

### Using helpers in automations and scripts

Prefer direct entity references for simple single-entity use:
```yaml
condition:
  - condition: state
    entity_id: input_boolean.sleep_mode
    state: "on"
```

For bulk operations or dynamic room-keyed lookups, use `mappings/helpers.yaml` via `!include`:
```yaml
variables:
  helpers: !include ../../mappings/helpers.yaml   # from automations/
  helpers: !include ../mappings/helpers.yaml      # from scripts/

# Then in Jinja:
# {{ helpers.heating.profile }}
# {{ helpers.heating.manual_overrides[room] }}
# {{ helpers.vacuum.room_selects[room_id] }}
```

### Adding a new helper

1. Add the entry to the relevant file in `helpers/`
2. Add it to `mappings/helpers.yaml` under the appropriate group
3. Reload via Developer Tools (no full restart needed)

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
| `switch.living_room_wall_outlet_switch_2` | Wall outlet switch — IKEA donut lamp |
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
| `sensor.scale_weight` | Scale — body weight (kg) |
| `sensor.scale_impedance` | Scale — body impedance (Ω) |
| `sensor.scale_impedance_low` | Scale — body impedance low frequency (Ω) |
| `sensor.scale_heart_rate` | Scale — heart rate (bpm) |
| `sensor.scale_body_fat` | Scale — body fat % (computed, `templates/scale.yaml`) |
| `sensor.scale_fat_mass` | Scale — fat mass kg (computed) |
| `sensor.scale_lean_mass` | Scale — lean mass kg (computed) |
| `sensor.scale_body_water` | Scale — body water % (computed) |
| `sensor.scale_bmi` | Scale — BMI kg/m² (computed) |
| `sensor.scale_bmr` | Scale — basal metabolic rate kcal (computed) |
| `sensor.scale_bone_mass` | Scale — bone mass kg (computed) |

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
| `light.entry_telecontrol_switch` | NodOn 4.1.20 impulse relay (télérupteur, DIN module in electrical panel) — master switch for both ceiling fixtures |
| `light.entry_ceiling_lamp_flat_led` | Ceiling flat LED lamp (Zigbee, color temp) — **no bypass**: goes `unavailable` when telecontrol is off |

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
| `button.pc_shutdown` | Desktop PC shutdown |
| `sensor.pc_cpuload` | Desktop PC CPU load |
| `sensor.pc_gpuload` | Desktop PC GPU load |
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
| Mushroom lamp (living room) | `mdi:mushroom-outline`     |
| Donut lamp (living room) | `mdi:circle-double`           |
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

## Template Sensors

Template sensors and binary sensors are defined in `templates/` and loaded via:
```yaml
# configuration.yaml
template: !include_dir_merge_list _HomeAssistant_Config/templates/
```

Each file is a **list of template blocks**. Format:
```yaml
- sensor:
    - name: "My Sensor"
      unique_id: my_sensor
      unit_of_measurement: "kg"
      state_class: measurement
      availability: "{{ states('sensor.source') | is_number }}"
      state: >
        {% set val = states('sensor.source') | float %}
        {{ val | round(1) }}

- binary_sensor:
    - name: "My Binary"
      unique_id: my_binary
      state: "{{ ... }}"
```

To add a new template sensor: add to the appropriate file in `templates/` (or create a new file), then reload via **Developer Tools → YAML → Template Entities**.

### Computed scale sensors (`templates/scale.yaml`)

Segal formula pre-computed for male, 28 y/o, 173 cm — update constants if profile changes.

| Constant | Value | Origin |
|----------|-------|--------|
| `25.730` | `0.00066360×173² - 0.12380×28 + 9.33285` | Height + age offset |
| `0.02117` | Impedance coefficient | Segal 1988 |
| `0.62854` | Weight coefficient | Segal 1988 |

Formula: `FFM = 25.730 - 0.02117×Z + 0.62854×W`

## Scripts

Scripts are split by domain in `scripts/` and auto-loaded via `!include_dir_merge_named`. To add a new group, create a new file — no index needed.

## Conventions
- Automations use numeric IDs (`id: "1740500000001"`)
- YAML anchors (`&alias` / `*alias`) are used in mappings to avoid duplication
- Button-card templates use inline JavaScript in `custom_fields`
- Dashboard navigation path: `/overview-dashboard/<path>`
- Button-card templates are split by feature in `dashboard/templates/` (auto-merged)
- Scripts are split by domain in `scripts/` (auto-merged via `!include_dir_merge_named`)
