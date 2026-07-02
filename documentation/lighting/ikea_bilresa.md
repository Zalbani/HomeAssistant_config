# IKEA BILRESA — Remote (Scroll Wheel)

## References

| Field | Value |
|-------|-------|
| Model | IKEA BILRESA |
| Protocol | Zigbee |
| Power | 2× AAA (HR03) — 1.5V alkaline or 1.2V NiMH (e.g. IKEA LADDA) |
| Price | ~20 € |
| Note | Not recommended by vendor |
| Purchase link | [ikea.com](https://www.ikea.com/fr/fr/p/bilresa-kit-de-reglage-a-distance-molette-50604173/) |
| Manual | [ikea_bilresa_manual.pdf](ikea_bilresa_manual.pdf) |
| Setup guide | [ikea_bilresa_setup.pdf](ikea_bilresa_setup.pdf) |

---

## Installed Units

| Room | HA Entity prefix | Location (mounted on) |
|------|------------------|----------------------|
| Living Room | `event.living_room_remote_button_*` | wall-mounted near room entrance |
| Office | `event.office_remote_button_*` | wall-mounted near room entrance |
| Bedroom | `event.bedroom_remote_button_*` | wall-mounted near room entrance |

---

## Button Mapping

| Button | Action | Automation |
|--------|--------|------------|
| 1 | Brightness − | Button 1&2 – Light scroll wheel |
| 2 | Brightness + | Button 1&2 – Light scroll wheel |
| 3 (single) | Toggle room lights | Button 3 – Light toggle |
| 3 (double) | Turn off all lights | Button 3 – Light toggle |
| 3 (hold) | Ramp brightness up/down (yoyo between adaptive_lighting min/max, ~5 s full range). Stops on release. | Button 3 – Light brightness yoyo on hold |
| 4 | Shutter down | Buttons 4&5 – Shutter scroll wheel |
| 5 | Shutter up | Buttons 4&5 – Shutter scroll wheel |
| 6 (single) | Toggle shutters (stop if moving / close if open / open if closed) | Button 6 – Shutter toggle |
| 6 (double) | Open all shutters of the room (skips any cover whose window sensor is open) | Button 6 – Shutter toggle |
| 6 (hold) | Move shutters (up if all closed, down otherwise) | Button 6 – Shutter hold |
| 9 (double) | Toggle PC on/off — **office only** | Button 9 – Computer toggle |

---

## Troubleshooting

### Remote not responding

1. Check battery level in HA (Maintenance view)
2. Press a button → check for events: Developer Tools → Events → listen to `zha_event`
3. If no event fires → remote is no longer paired → see Reset & Reconnection below

### Double press not triggering (e.g. PC toggle, all lights off)

1. Press faster — the window is typically 500 ms
2. Check the corresponding automation is enabled in HA

---

## Reset & Reconnection

### Factory reset

_(To be filled based on BILRESA model)_

### Zigbee pairing

1. In HA: open ZHA / Z2M → enable pairing mode
2. On the remote: _(pairing procedure for BILRESA)_
3. Wait for detection in HA
4. Verify events fire for each button: Developer Tools → Events → `zha_event`

### Battery replacement

- Type: **2× AAA (HR03)** — 1.5V alkaline or 1.2V NiMH rechargeable (e.g. IKEA LADDA)
- Pairing is retained after battery swap
