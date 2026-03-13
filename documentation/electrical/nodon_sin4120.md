# NodOn SIN-4-1-20 — Dry Contact / Switch Module

## References

| Field | Value |
|-------|-------|
| Model | NodOn SIN-4-1-20 |
| Type | 1-channel switch + dry contact, 16A |
| Protocol | Zigbee |
| Use cases | Telerupteur, gate, VMC, boiler |
| Price | ~39 € |
| Purchase link | [domadoo.fr](https://www.domadoo.fr/fr/peripheriques/5688-nodon-module-commutateur-contact-sec-zigbee-16a-portail-vmc-chaudiere-sin-4-1-20-3700313925188.html) |
| Manual | [nodon_sin4120_manual.pdf](nodon_sin4120_manual.pdf) |

### DIN Rail Accessory

| Model | Price | Link |
|-------|-------|------|
| NodOn DIN rail box "Standard" (pack of 5) | ~10 € | [amazon.fr](https://www.amazon.fr/NodOn-Standard-accessoire-%C3%A9lectrique-compatible/dp/B0FY6NLPVR) |

---

## Installed Units

| Location | HA Entity | Controls | Wiring mode |
|----------|-----------|----------|-------------|
| not yet installed | not yet installed | not yet installed | not yet installed |

---

## Troubleshooting

### Module not responding / `unavailable`

1. Check power to the module (circuit breaker / DIN rail)
2. Check ZHA / Z2M — is the module reachable?
3. Verify wiring — the SIN-4-1-20 requires a neutral wire

### Controlled device (telerupteur, VMC) not toggling

1. Confirm dry contact mode is configured correctly in ZHA / Z2M device settings
2. Check the load wiring on the output terminals

---

## Reset & Reconnection

### Factory reset

1. Press and hold the module button for **10 seconds** until the LED changes behavior
2. Release → reset complete

### Zigbee pairing

1. In HA: open ZHA / Z2M → enable pairing mode
2. Press the module button **3 times** quickly
3. Wait for detection in HA (30–60 s)
4. Rename entity and configure as switch or dry contact in device settings
