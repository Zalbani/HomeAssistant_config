# SONOFF ZBMINIL2 — In-Wall Switch Module (1-channel)

## References

| Field | Value |
|-------|-------|
| Model | SONOFF ZBMINIL2 |
| Channels | 1 |
| Neutral required | No |
| Protocol | Zigbee 3.0 |
| Price | ~11 € |
| Purchase link | [domadoo.fr](https://www.domadoo.fr/en/devices/6619-sonoff-zigbee-30-neutral-free-intelligent-switch-zbminil2-6920075778298.html) |
| Manual | [sonoff_zbminil2_manual.pdf](sonoff_zbminil2_manual.pdf) |

Installed inside wall electrical boxes (EUROHM D67) behind existing switches.

**Two-way switch wiring (va-et-vient):** supported — S1/S2 terminals connect to the two switch wires.

---

## Installed Units

| Room | HA Entity | Controls |
|------|-----------|----------|
| Living Room — ceiling | `switch.living_room_ceiling_lamp_switch` | Ceiling lamp bulb |
| Living Room — outlet | `switch.living_room_outlet_switch` | Floor lamp |
| Office — ceiling | `switch.office_ceiling_lamp_switch` | Ceiling lamp bulb |
| Restroom | `switch.restroom_ceiling_lamp_switch` | Ceiling lamp |
| Balcony | `switch.balcony_outside_lamp` | Outdoor lamp |

---

## Troubleshooting

### Switch not responding / `unavailable`

1. Check physical power (circuit breaker for the room)
2. Check ZHA / Z2M — is the module reachable?
3. Toggle from HA UI — if no response → Zigbee issue → see Reset & Reconnection

### Physical switch no longer works but HA still controls the light

- The module has lost sync with the wall switch input → check S1/S2 wiring connections

### Switch state out of sync with actual light state

1. Developer Tools → States → find the entity → force a state refresh
2. Toggle via HA to re-sync

---

## Reset & Reconnection

### Factory reset

1. Press and hold the button on the module for **5 seconds**
2. LED blinks rapidly → reset complete

### Zigbee pairing

1. In HA: open ZHA / Z2M → enable pairing mode
2. Press the button on the module **once** briefly (after reset)
3. Wait for detection in HA (30–60 s)
4. Rename entity to match existing convention
