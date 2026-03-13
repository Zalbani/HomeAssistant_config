# SONOFF MINI DUO-L — In-Wall Switch Module (2-channel)

## References

| Field | Value |
|-------|-------|
| Model | SONOFF MINI DUO-L |
| Channels | 2 |
| Neutral required | No |
| Protocol | Zigbee 3.0 |
| Price | ~19 € |
| Purchase link | [domadoo.fr](https://www.domadoo.fr/en/zigbee-devices/8682-sonoff-2-channel-zigbee-switch-without-neutral-mini-duo-l.html) |
| Manual | [sonoff_miniduo_manual.pdf](sonoff_miniduo_manual.pdf) |

Installed inside wall electrical boxes (EUROHM D67). One module controls two independent circuits.

---

## Installed Units

| Room | HA Entity | Channel | Controls |
|------|-----------|---------|----------|
| Kitchen | `switch.kitchen_ceiling_lamp_switch` | 1 | Ceiling lamp |
| Kitchen | `switch.kitchen_under_cabinet_lighting_switch` | 2 | Under-cabinet lighting |
| Bathroom | `switch.bathroom_ceiling_lamp_switch` | 1 | Ceiling lamp |
| Bathroom | `switch.bathroom_mirror_switch` | 2 | Mirror light |

---

## Troubleshooting

### One channel not responding but the other works

- The module is reachable but one output is stuck → toggle from HA UI to re-sync
- Check wiring on the affected output terminal

### Module not responding / `unavailable`

1. Check physical power (circuit breaker for the room)
2. Check ZHA / Z2M — is the module reachable?
3. Toggle from HA UI — if no response → Zigbee issue → see Reset & Reconnection

### Switch state out of sync with actual light state

1. Developer Tools → States → find the entity → force a state refresh
2. Toggle via HA to re-sync

---

## Reset & Reconnection

### Factory reset

1. Press and hold the button for **5 seconds**
2. LED blinks → reset complete

### Zigbee pairing

1. In HA: open ZHA / Z2M → enable pairing mode
2. Press the button on the module **once** briefly (after reset)
3. Wait for detection in HA (30–60 s)
4. Rename both channel entities to match existing convention
