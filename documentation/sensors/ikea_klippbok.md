# IKEA KLIPPBOK — Water Leak Sensor

## References

| Field | Value |
|-------|-------|
| Model | IKEA KLIPPBOK |
| Protocol | Zigbee |
| Power | 1× AAA (HR03) — 1.2V NiMH rechargeable (e.g. IKEA LADDA) |
| Price | ~8 € |
| Purchase link | [ikea.com](https://www.ikea.com/fr/fr/p/klippbok-detecteur-de-fuites-deau-connecte-30604193/) |
| Manual | [ikea_klippbok_manual.pdf](ikea_klippbok_manual.pdf) |
| Setup guide | [ikea_klippbok_setup.pdf](ikea_klippbok_setup.pdf) |

---

## Installed Units

| Location | HA Entity | Automations / Alerts |
|----------|-----------|----------------------|
| not yet installed | not yet installed | not yet installed |

---

## Troubleshooting

### Sensor not triggering on water contact

1. Check that the sensor probes are in contact with the floor / surface (not elevated)
2. Check battery level in HA (Maintenance view)
3. Test by briefly touching the probes with a damp cloth

### Sensor shows as `unavailable`

1. Check battery level
2. Check Zigbee network coverage at the sensor location

---

## Reset & Reconnection

### Factory reset

_(To be filled for KLIPPBOK)_

### Zigbee pairing

1. In HA: open ZHA / Z2M → enable pairing mode
2. On the sensor: _(pairing procedure for KLIPPBOK)_
3. Wait for detection in HA (30–60 s)
4. Rename entity and set up alert automations

### Battery replacement

- Type: **1× AAA (HR03)** — 1.2V NiMH rechargeable (e.g. IKEA LADDA)
- Pairing is retained after battery swap
