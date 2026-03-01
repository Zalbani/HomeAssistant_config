# Home Assistant Config — CLAUDE.md

## Project Structure

```
_HomeAssistant_Config/
├── automations/          # YAML automations organized by category
│   ├── Heaters/          # 10 heating automations
│   ├── Shutters/         # 4 shutter automations
│   ├── Lights/           # 2 light automations
│   ├── Remotes/          # 6 remote control automations
│   ├── Sensors/          # Sensors (window → shutter)
│   └── index.yaml        # !include_dir_merge_list of all subdirectories
├── dashboard/
│   ├── main.yaml         # Dashboard entry point (includes templates/ + views/)
│   ├── templates/        # Reusable button-card templates
│   │   └── overview_card_templates.yaml   # room_card, room_card_action
│   ├── views/            # One file per dashboard page
│   └── rooms/            # Per-room cards, included from views
│       ├── config.yaml   # Centralized room metadata (name, icon, color) keyed by room_id
│       └── <room>/
│           ├── overview_card.yaml  # Summary card used in home.yaml
│           └── controls.yaml       # Bubble cards (lights, shutters, PC)
├── mappings/
│   ├── heaters/          # Profiles, thermostats, valves, window sensors
│   ├── lights/           # Remote → light entities mapping
│   └── shutters/         # Remote → shutter entities mapping
├── scripts.yaml
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

| room_id      | Name        | Icon                     | Color     |
|--------------|-------------|--------------------------|-----------|
| living_room  | Living Room | mdi:sofa-outline         | #4CAF50   |
| office       | Office      | mdi:microsoft-office     | #C9A96E   |
| bedroom      | Bedroom     | mdi:bed-outline          | #B55233   |
| kitchen      | Kitchen     | mdi:coffee-maker-outline | #E6B800   |
| bathroom     | Bathroom    | mdi:bathtub-outline      | #45B5C4   |
| restroom     | Restroom    | mdi:paper-roll-outline   | #D4759E   |
| balcony      | Balcony     | mdi:balcony              | #8BC34A   |
| entry        | Entry       | mdi:door-open            | #78909C   |

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

## Remote Control Mappings

Remotes have numbered buttons (3, 4, 5, 6, 9):
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
| `switch.living_room_ceiling_lamp_switch` | Ceiling lamp switch |
| `light.living_room_ceiling_lamp_bulb` | Ceiling lamp bulb (smart, color temp) |
| `switch.living_room_outlet_switch` | Outlet switch (floor lamp) |
| `light.living_room_streetlight_bulb` | Streetlight bulb (smart, color temp) |
| `light.living_room_mushroom_lamp_bulb` | Mushroom lamp (smart, color temp) |
| `cover.living_room_shutter` | Window shutter |
| `cover.living_room_sunshade` | Sunshade / BSO |
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
| `switch.office_ceiling_lamp_switch` | Ceiling lamp switch |
| `light.office_ceiling_lamp_bulb` | Ceiling lamp bulb (smart, color temp) |
| `cover.office_sunshade` | Sunshade / BSO |
| `climate.office_thermostatic_valve_thermostat` | Thermostat |
| `sensor.office_thermometer_temperature` | Temperature sensor |
| `sensor.office_thermometer_humidity` | Humidity sensor |
| `sensor.office_thermostatic_valve_hvac_action` | HVAC action |
| `input_boolean.heating_manual_office` | Manual heating override |
| `event.office_remote_button_3/4/5/6/9` | Remote (lights / shutters / PC) |

### Bedroom
| Entity | Description |
|--------|-------------|
| `light.bedroom_ceiling_lamp_bulb` | Ceiling lamp bulb (smart, color temp) |
| `light.bedroom_bed_led_strip` | Bed LED strip |
| `cover.bedroom_sunshade` | Sunshade / BSO |
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

### Restroom
| Entity | Description |
|--------|-------------|
| `switch.restroom_ceiling_lamp_switch` | Ceiling lamp switch |

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

## Icon Mapping (Light entities)

| Entity type              | MDI icon                      |
|--------------------------|-------------------------------|
| Ceiling lamp (all rooms) | `mdi:ceiling-light-outline`   |
| Streetlight (living room)| `mdi:floor-lamp-torchiere`    |
| Balcony outside lamp     | `mdi:coach-lamp-variant`      |
| Under cabinet (kitchen)  | `mdi:dome-light`              |

## Conventions
- Automations use numeric IDs (`id: "1740500000001"`)
- YAML anchors (`&alias` / `*alias`) are used in mappings to avoid duplication
- Button-card templates use inline JavaScript in `custom_fields`
- Dashboard navigation path: `/overview-dashboard/<path>`
