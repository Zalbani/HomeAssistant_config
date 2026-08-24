# Connected Device Catalog

---

## Thermostatic Valves

| Room | HA Entity | Model | Reference | Protocol |
|------|-----------|-------|-----------|----------|
| Living Room | `climate.living_room_thermostatic_valve_thermostat` | Sonoff TRVZB | `TRVZB` | Zigbee |
| Office | `climate.office_thermostatic_valve_thermostat` | Sonoff TRVZB | `TRVZB` | Zigbee |
| Bedroom | `climate.bedroom_thermostatic_valve_thermostat` | Sonoff TRVZB | `TRVZB` | Zigbee |
| Bathroom | `climate.bathroom_thermostatic_valve_thermostat` | Sonoff TRVZB | `TRVZB` | Zigbee |

**Sonoff TRVZB specs:**
- Temperature range: 4 °C – 35 °C
- Built-in open window detection
- External temperature sensor control supported
- Power: 2× AA batteries

---

## Temperature / Humidity Sensors

| Room | HA Entity (temp.) | HA Entity (hum.) | Model | Protocol |
|------|-------------------|-------------------|-------|----------|
| Living Room | `sensor.living_room_thermometer_temperature` | `sensor.living_room_thermometer_humidity` | _(to be filled)_ | Zigbee |
| Office | `sensor.office_thermometer_temperature` | `sensor.office_thermometer_humidity` | _(to be filled)_ | Zigbee |
| Bedroom | `sensor.bedroom_thermometer_temperature` | `sensor.bedroom_thermometer_humidity` | _(to be filled)_ | Zigbee |
| Bathroom | `sensor.bathroom_thermometer_temperature` | `sensor.bathroom_thermometer_humidity` | _(to be filled)_ | Zigbee |

---

## Remotes

| Room | HA Entity | Model | Reference | Protocol |
|------|-----------|-------|-----------|----------|
| Living Room | `event.living_room_remote_button_*` | _(to be filled)_ | _(to be filled)_ | Zigbee |
| Office | `event.office_remote_button_*` | _(to be filled)_ | _(to be filled)_ | Zigbee |
| Bedroom | `event.bedroom_remote_button_*` | _(to be filled)_ | _(to be filled)_ | Zigbee |

**Button mapping:**
| Button | Action |
|--------|--------|
| 1 | Brightness − (scroll wheel) |
| 2 | Brightness + (scroll wheel) |
| 3 | Toggle lights |
| 4 | Shutter down |
| 5 | Shutter up |
| 6 | Toggle / hold shutters |
| 9 | Toggle PC (office only) |

---

## Smart Bulbs

| Room | HA Entity | Model | Protocol | Socket | Features |
|------|-----------|-------|----------|--------|----------|
| Living Room — ceiling | `light.living_room_ceiling_lamp_bulb` | _(to be filled)_ | Zigbee | _(E27/GU10)_ | Color temperature |
| Living Room — streetlight | `light.living_room_streetlight_bulb` | _(to be filled)_ | Zigbee | _(E27/GU10)_ | Color temperature |
| Living Room — mushroom | `light.living_room_mushroom_lamp_bulb` | _(to be filled)_ | Zigbee | _(E27/GU10)_ | Color temperature |
| Office — ceiling | `light.office_ceiling_lamp_bulb` | _(to be filled)_ | Zigbee | _(E27/GU10)_ | Color temperature |
| Bedroom — ceiling | `light.bedroom_ceiling_lamp_bulb` | _(to be filled)_ | Zigbee | _(E27/GU10)_ | Color temperature |
| Bedroom — LED strip | `light.bedroom_bed_led_strip` | _(to be filled)_ | Zigbee | — | _(color / white)_ |
| Entry — ceiling flat LED | `light.entry_ceiling_lamp_flat_led` | _(to be filled)_ | Zigbee | Integrated LED | Color temperature (no bypass — depends on `light.entry_telecontrol_switch`) |

---

## Switches / Outlets

| Room | HA Entity | Model | Protocol | Type |
|------|-----------|-------|----------|------|
| Living Room — ceiling | `switch.living_room_ceiling_lamp_switch` | _(to be filled)_ | Zigbee | Wall switch |
| Living Room — outlet | `switch.living_room_outlet_switch` | _(to be filled)_ | Zigbee | Smart plug |
| Office — ceiling | `switch.office_ceiling_lamp_switch` | _(to be filled)_ | Zigbee | Wall switch |
| Kitchen — ceiling | `switch.kitchen_ceiling_lamp_switch` | _(to be filled)_ | Zigbee | Wall switch |
| Kitchen — under cabinet | `switch.kitchen_under_cabinet_lighting_switch` | _(to be filled)_ | Zigbee | Wall switch |
| Bathroom — ceiling | `switch.bathroom_ceiling_lamp_switch` | _(to be filled)_ | Zigbee | Wall switch |
| Bathroom — mirror | `switch.bathroom_mirror_switch` | _(to be filled)_ | Zigbee | Wall switch |
| Restroom | `switch.restroom_ceiling_lamp_switch` | _(to be filled)_ | Zigbee | Wall switch |
| Restroom — washing machine | `switch.restroom_washing_machine_outlet` | Schneider Wiser SOCKET/OUTLET/1 | Zigbee | Smart outlet with energy monitoring |
| Office — 3D printer | `switch.office_3d_printer_outlet` | Schneider Wiser SOCKET/OUTLET/1 | Zigbee | Smart outlet with energy monitoring |
| Balcony | `switch.balcony_outside_lamp` | _(to be filled)_ | Zigbee | Wall switch |
| Entry — telecontrol (both ceiling fixtures) | `light.entry_telecontrol_switch` | NodOn 4.1.20 | Zigbee | DIN impulse relay (télérupteur) in electrical panel |
| Desktop PC | `switch.alban_pc_fixe` + `button.hass_pc_fixe_shutdown` | Wake-on-LAN | Network | — |

---

## Shutters / Sunshades

| Room | HA Entity | Model | Protocol |
|------|-----------|-------|----------|
| Living Room — shutter | `cover.living_room_shutter` | _(to be filled)_ | _(Zigbee / Z-Wave / other)_ |
| Living Room — sunshade | `cover.living_room_sunshade` | _(to be filled)_ | _(to be filled)_ |
| Office — sunshade | `cover.office_sunshade` | _(to be filled)_ | _(to be filled)_ |
| Bedroom — sunshade | `cover.bedroom_sunshade` | _(to be filled)_ | _(to be filled)_ |
| Kitchen — shutter | `cover.kitchen_shutter` | _(to be filled)_ | _(to be filled)_ |

---

## Window / Door Sensors

| Room | HA Entity | Model | Protocol |
|------|-----------|-------|----------|
| Living Room — French window | `binary_sensor.living_room_french_window` | _(to be filled)_ | Zigbee |

---

## Desktop PC

| Field | Value |
|-------|-------|
| Power entity | `switch.alban_pc_fixe` |
| Shutdown entity | `button.hass_pc_fixe_shutdown` |
| CPU entity | `sensor.hass_pc_fixe_cpuload` |
| Power-on method | Wake-on-LAN |
| MAC address | _(to be filled)_ |
| Static IP | _(to be filled)_ |
| HA agent installed | Yes (Home Assistant Agent) |
