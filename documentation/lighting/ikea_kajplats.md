# Smart Bulbs

## References

| Field | Value |
|-------|-------|
| Model | IKEA KAJPLATS E27 (white spectrum) |
| Protocol | Zigbee |
| Socket | E27 |
| Lumens | 1521 lm |
| Price | ~13 € |
| Purchase link | [ikea.com](https://www.ikea.com/fr/fr/p/kajplats-ampoule-led-e27-1521-lumen-connecte-spectre-blanc-globe-blanc-opaque-10611302/) |
| Manual | [ikea_kajplats_manual.pdf](ikea_kajplats_manual.pdf) |

### Accessories

| Model | Purpose | Price | Link |
|-------|---------|-------|------|
| Shelly Bypass | Fixes flickering when no neutral wire is available | ~3 € | [shelly.com](https://www.shelly.com/fr/products/shelly-bypass) |

---

## Installed Units

| Room | HA Entity | Model | Location |
|------|-----------|-------|----------|
| Living Room — ceiling | `light.living_room_ceiling_lamp_bulb` | IKEA KAJPLATS E27 | Ceiling fixture |
| Living Room — streetlight | `light.living_room_streetlight_bulb` | IKEA KAJPLATS E27 | Floor lamp |
| Living Room — mushroom | `light.living_room_mushroom_lamp_bulb` | IKEA KAJPLATS E27 | Table lamp |
| Office — ceiling | `light.office_ceiling_lamp_bulb` | IKEA KAJPLATS E27 | Ceiling fixture |
| Bedroom — ceiling | `light.bedroom_ceiling_lamp_bulb` | IKEA KAJPLATS E27 | Ceiling fixture |
| Bedroom — LED strip | `light.bedroom_bed_led_strip` | _(to be filled)_ | _(under bed / behind headboard)_ |
| Entry | `light.entry_telecontrol_switch` | IKEA KAJPLATS E27 | ceiling fixture |

**Rooms with Shelly bypass** (bulb always powered — wall switch safe to toggle):

| Room | Switch entity | Notes |
|------|--------------|-------|
| Living Room — ceiling | `switch.living_room_ceiling_lamp_switch` | Bypass installed in fixture |
| Office — ceiling | `switch.office_ceiling_lamp_switch` | Bypass installed in fixture |
| Bedroom — ceiling | `switch.bedroom_ceiling_lamp_switch` | Bypass installed in fixture |

The bypass automation (`Lights/switch_light_bypass_global.yaml`) intercepts the switch state change and controls the bulb via Zigbee, so the bulb always has power.

---

## Important — Wall Switches

For bulbs **without** a Shelly bypass (Entry, streetlight, mushroom): if the wall switch is turned **off physically**, the bulb loses power and **cannot be controlled from HA**. Always keep those switches in the ON position.

---

## Troubleshooting

### Bulb not responding / `unavailable`

1. **Check the associated wall switch** — most common cause
2. If the switch is ON → bulb dropped off Zigbee network → see Reset & Reconnection
3. Check ZHA / Z2M to confirm the device is reachable

### Bulb flickers

- If no neutral wire is present in the box, install a **Shelly Bypass** in parallel with the bulb

### Unexpected color temperature or brightness

1. Check if an automation or scene is overriding the state
2. Check brightness minimum (automations enforce ≥ 10 %)

---

## Reset & Reconnection

### Factory reset (on/off method)

Using the physical wall switch (or cut power at breaker for bypass rooms):

1. Turn the bulb on
2. Turn it off after **2 seconds**
3. Repeat **6 times** (IKEA KAJPLATS)
4. The bulb blinks several times → reset complete

### Zigbee pairing

1. In HA: open ZHA / Z2M → enable pairing mode
2. Power the bulb on — it enters pairing mode immediately after reset
3. Wait for detection in HA (30–60 s)
4. Rename the entity to match the existing convention
