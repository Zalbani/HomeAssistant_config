# IKEA MYGGSPRAY — Motion Sensor

## References

| Field | Value |
|-------|-------|
| Model | IKEA MYGGSPRAY |
| Protocol | Zigbee |
| Power | _(battery type — to be filled)_ |
| Price | ~8 € |
| Purchase link | [ikea.com](https://www.ikea.com/fr/fr/p/myggspray-detecteur-de-mouvement-sans-fil-connecte-70604186/) |
| Manual | [ikea_myggspray_manual.pdf](ikea_myggspray_manual.pdf) |
| Setup guide | [ikea_myggspray_setup.pdf](ikea_myggspray_setup.pdf) |

---

## Installed Units

| Room | HA Entity | Location | Automations |
|------|-----------|----------|-------------|
| not yet installed | not yet installed | not yet installed | not yet installed |

---

## Troubleshooting

### Sensor not triggering

1. Check battery level in HA (Maintenance view)
2. Check detection zone — PIR sensors have a limited angle and range
3. Check if there is a cooldown period between detections (some sensors have a 1–3 min lockout)
4. In ZHA / Z2M, check the last received event timestamp

### Sensor shows as `unavailable`

1. Check battery level
2. Check Zigbee network coverage at the sensor location

---

## Reset & Reconnection

### Factory reset

_(To be filled for MYGGSPRAY)_

### Zigbee pairing

1. In HA: open ZHA / Z2M → enable pairing mode
2. On the sensor: _(pairing procedure for MYGGSPRAY)_
3. Wait for detection in HA (30–60 s)
4. Rename entity and link to automations

### Battery replacement

- Type: _(to be filled)_
- Pairing is retained after battery swap
