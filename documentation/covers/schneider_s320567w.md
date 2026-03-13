# Shutters & Sunshades

## References

| Field | Value |
|-------|-------|
| Model | Schneider Wiser Ovalis — Roller shutter switch |
| Reference | S320567W |
| Protocol | Zigbee |
| Max load | 4A |
| Color | White |
| Format | Ovalis wall switch (replaces standard wall switch) |
| Purchase link | [123elec.com](https://www.123elec.com/schneider-wiser-ovalis-interrupteur-volet-roulant-4a-zigbee-blanc-s320567w.html) |
| Manual | [schneider_s320567w_manual.pdf](schneider_s320567w_manual.pdf) |

This is a wall-mounted switch module (Ovalis format) that replaces the existing roller shutter wall switch.

---

## Installed Units

| Room | HA Entity | Type | Window |
|------|-----------|------|--------|
| Living Room | `cover.living_room_shutter` | Shutter | main window |
| Living Room | `cover.living_room_sunshade` | Sunshade | French window |
| Office | `cover.office_sunshade` | Sunshade | main window |
| Bedroom | `cover.bedroom_sunshade` | Sunshade | main window |
| Kitchen | `cover.kitchen_shutter` | Shutter | main window |

---

## Automations Controlling Covers

| Automation | Trigger | Behavior |
|------------|---------|----------|
| Close – Global | 30 min after sunset | Closes all shutters (skips if associated window sensor is open) |
| Wake UP – Time (Bedroom) | Sunrise +30 min (weekdays) / 11:00 (weekends) | Opens bedroom shutter if phone alarm is > 5 h away |
| Wake UP – Sun (Office / Living / Kitchen) | Sunrise +30 min | Opens office, living room, kitchen shutters |
| Wake UP – Phone (Global) | 2 min before phone alarm | Opens all shutters |
| French window sensor | Window opens/closes | Raises living room sunshade to 70 % when window opens; lowers at night when it closes |

---

## Troubleshooting

### Cover not responding to remote or HA

1. Check the entity in HA — `unavailable` or `unknown`?
2. Test directly from HA UI (open/close button)
3. If HA sends the command but shutter doesn't move → power/motor issue (check circuit breaker)
4. If entity is `unavailable` → Zigbee issue → see Reset & Reconnection

### Cover closes at unexpected times

- Check the "Close – Global" automation (triggers 30 min after sunset)
- Check whether the French window sensor triggered the sunshade automation

### Cover position is wrong after a power cut

- The motor may have lost its calibration → recalibrate end stops (see below)

---

## Reset & Reconnection

### Schneider Wiser S320567W — Factory reset

_(To be filled — check Schneider documentation)_

### Zigbee pairing

1. In HA: open ZHA / Z2M → enable pairing mode
2. On the switch: _(pairing procedure for S320567W)_
3. Wait for detection in HA (30–60 s)
4. Rename entity to match existing convention

### Position recalibration (end stop setup)

After a reset or motor replacement:
1. Send a full **close** command → wait for full completion
2. Send a full **open** command → wait for full completion
3. The module should now know its full travel range
