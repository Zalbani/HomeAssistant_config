# IKEA TIMMERFLOTTE — Temperature & Humidity Sensor

## References

| Field | Value |
|-------|-------|
| Model | IKEA TIMMERFLOTTE |
| Protocol | Zigbee |
| Power | 2× AAA (HR03) — 1.5V |
| Price | ~8 € |
| Purchase link | [ikea.com](https://www.ikea.com/fr/fr/p/timmerflotte-capteur-dhumidite-temperature-connecte-30597606/) |
| Manual | [ikea_timmerflotte_manual.pdf](ikea_timmerflotte_manual.pdf) |
| Setup guide | [ikea_timmerflotte_setup.pdf](ikea_timmerflotte_setup.pdf) |

---

## Installed Units

| Room | Temp entity | Humidity entity | Location in room |
|------|-------------|-----------------|------------------|
| Living Room | `sensor.living_room_thermometer_temperature` | `sensor.living_room_thermometer_humidity` | wall-mounted, away from radiator and direct sunlight |
| Office | `sensor.office_thermometer_temperature` | `sensor.office_thermometer_humidity` | wall-mounted, away from radiator and direct sunlight |
| Bedroom | `sensor.bedroom_thermometer_temperature` | `sensor.bedroom_thermometer_humidity` | wall-mounted, away from radiator and direct sunlight |
| Bathroom | `sensor.bathroom_thermometer_temperature` | `sensor.bathroom_thermometer_humidity` | wall-mounted, away from radiator and direct sunlight |

---

## Role in HA

Each thermometer feeds its reading into the associated thermostatic valve via the `heat_sync` automation (`mappings/heaters/mapping.yaml`). If the sensor becomes unavailable, the sync automation automatically disables the external sensor on the valve so it falls back to its internal sensor.

---

## Troubleshooting

### Sensor shows as `unavailable`

1. Check battery level in HA (Maintenance view)
2. Check in ZHA / Z2M whether the device is reachable
3. If unavailable → the associated valve automatically falls back to its internal sensor (expected behavior)

### Temperature reading seems wrong

1. Check the sensor is not placed near a heat source or in direct sunlight
2. Compare with another source
3. If the offset is consistent → calibrate via ZHA / Z2M offset setting

---

## Reset & Reconnection

### Factory reset

_(To be filled)_

### Zigbee pairing

1. In HA: open ZHA / Z2M → enable pairing mode
2. On the sensor: _(pairing procedure for TIMMERFLOTTE)_
3. Wait for detection in HA (30–60 s)
4. Rename entity to match existing convention

### Battery replacement

- Type: **2× AAA (HR03)** — 1.5V
- Pairing is retained after battery swap
