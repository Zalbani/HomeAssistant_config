# Sonoff TRVZB — Thermostatic Valve

## References

| Field | Value |
|-------|-------|
| Model | Sonoff TRVZB |
| Reference | `TRVZB` |
| Protocol | Zigbee 3.0 |
| Power | 2× AA batteries |
| Temperature range | 4 °C – 35 °C |
| Price | ~21 € |
| Purchase link | [domadoo.fr](https://www.domadoo.fr/en/smart-heating/6776-sonoff-zigbee-30-thermostatic-head-trvzb-zigbee2mqtt-compatible.html) |
| Manual | [sonoff_trvzb_manual.pdf](sonoff_trvzb_manual.pdf) |

---

## Installed Units

| Room | HA Entity | Radiator location |
|------|-----------|-------------------|
| Living Room | `climate.living_room_thermostatic_valve_thermostat` | main radiator |
| Office | `climate.office_thermostatic_valve_thermostat` | main radiator |
| Bedroom | `climate.bedroom_thermostatic_valve_thermostat` | main radiator |
| Bathroom | `climate.bathroom_thermostatic_valve_thermostat` | main radiator |

**Related entities per room:**

| Entity | Description |
|--------|-------------|
| `sensor.<room>_thermostatic_valve_hvac_action` | Current HVAC action (heating / idle / off) |
| `input_boolean.heating_manual_<room>` | Manual override flag |

---

## Features Used in HA

- **External temperature sensor**: each valve receives the room thermometer reading via the `heat_sync` automation
- **Open window detection**: native valve feature + HA automation (`window_valve_mapping.yaml`)
- **Hardware min/max limits**: pushed by automation `climate_hardware_range_limiter_sync`

---

## Troubleshooting

### Valve shows as `unavailable`

**Possible causes:** dead batteries / out of Zigbee range / coordinator restarted

1. Check battery level (blinking LED on the valve, or HA battery indicator)
2. Check in ZHA / Z2M whether nearby router devices are active
3. Verify the Zigbee coordinator is detected (`/dev/ttyUSB0` or `/dev/ttyACM0`)

**HA behavior when unavailable:**
- `heat_sync` disables the external sensor automatically
- `heating_profile_applier` skips the valve

---

### Valve is heating when it should not (or vice versa)

1. Check `input_boolean.heating_manual_<room>` — if ON, disable it manually
2. Check `sensor.<room>_thermostatic_valve_hvac_action`
3. Force profile re-application: change then restore `input_select.heating_profile`

---

### Displayed temperature is incorrect

1. Check `sensor.<room>_thermometer_temperature` — is it available?
2. In Zigbee integration, verify the "external temperature sensor" option is active on the valve
3. If the thermometer is offline → valve falls back to its internal sensor (expected behavior)

---

## Reset & Reconnection

### Factory reset

1. Press and hold the center button for **5 seconds** → screen shows `rSt`
2. Release → the valve restarts and blinks → reset complete

### Zigbee pairing

1. In HA: open ZHA / Z2M → enable pairing mode ("Permit join")
2. On the valve: press and hold the button for **3 seconds** → Zigbee icon blinks
3. Wait for HA to detect the device (30–60 s)
4. Rename the entity to match the existing convention

> **After re-pairing:** verify that the external temperature sensor (thermometer) is correctly linked to the valve in Zigbee settings.

### Battery replacement

- Type: **2× AA**
- Slide off the back cover to access the compartment
- Pairing is retained after battery swap
- Entity returns to `available` within a few minutes
