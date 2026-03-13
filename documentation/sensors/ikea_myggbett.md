# IKEA MYGGBETT — Window & Door Sensor

## References

| Field | Value |
|-------|-------|
| Model | IKEA MYGGBETT |
| Protocol | Zigbee |
| Power | 1× AAA (HR03) — 1.2V NiMH rechargeable (e.g. IKEA LADDA) |
| Price | ~8 € |
| Purchase link | [ikea.com](https://www.ikea.com/fr/fr/p/myggbett-capteur-fenetre-porte-connecte-00603864/) |
| Manual | [ikea_myggbett_manual.pdf](ikea_myggbett_manual.pdf) |
| Setup guide | [ikea_myggbett_setup.pdf](ikea_myggbett_setup.pdf) |

---

## Installed Units

| Room | HA Entity | Mounted on | Linked cover / valve |
|------|-----------|------------|----------------------|
| Living Room | `binary_sensor.living_room_french_window` | French window | `cover.living_room_sunshade` + valve |

---

## Role in HA

| Automation | Behavior |
|------------|----------|
| French window sensor | Raises `cover.living_room_sunshade` to 70 % when window opens (if closed); lowers it when window closes at night |
| Window open → valve off | Turns off the thermostatic valve when window opens; restores setpoint when closed (`window_valve_mapping.yaml`) |
| Close – Global | Excludes covers from auto-close when their associated window sensor is open |

---

## Troubleshooting

### Sensor stuck on `open` or `closed`

1. Check physical magnet alignment — the two parts must be within a few mm when the window is closed
2. Check battery level in HA (Maintenance view)
3. In ZHA / Z2M, check the last received event timestamp

### Sensor shows as `unavailable`

1. Check battery level
2. Check Zigbee network coverage near the window

---

## Reset & Reconnection

### Factory reset

_(To be filled for MYGGBETT)_

### Zigbee pairing

1. In HA: open ZHA / Z2M → enable pairing mode
2. On the sensor: _(pairing procedure for MYGGBETT)_
3. Wait for detection in HA (30–60 s)
4. Rename entity to match existing convention

### Battery replacement

- Type: **1× AAA (HR03)** — 1.2V NiMH rechargeable (e.g. IKEA LADDA)
- Pairing is retained after battery swap
