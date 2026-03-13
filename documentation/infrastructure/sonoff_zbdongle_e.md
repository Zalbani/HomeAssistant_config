# SONOFF ZBDONGLE-E — Zigbee Coordinator

## References

| Field | Value |
|-------|-------|
| Model | SONOFF Zigbee 3.0 USB Dongle Plus V2 |
| Reference | ZBDONGLE-E |
| Protocol | Zigbee 3.0 |
| Antenna | External |
| Price | ~15 € |
| Purchase link | [domadoo.fr](https://www.domadoo.fr/en/zigbee-dongle/6315-sonoff-zigbee-30-usb-dongle-external-antenna-v2-zbdongle-e-compatible-with-home-assistant-and-zigbee2mqtt-6920075777659.html) |
| Manual | [sonoff_zbdongle_e_manual.pdf](sonoff_zbdongle_e_manual.pdf) |

---

## Current Setup

| Field | Value |
|-------|-------|
| USB port | _(e.g. `/dev/ttyUSB0` or `/dev/serial/by-id/usb-ITead_...`)_ |
| Zigbee channel | _(e.g. 15)_ |
| HA integration | ZHA / Zigbee2MQTT _(to be filled)_ |
| Location | _(USB extension cable? — to be filled)_ |

> **Tip:** Use a USB extension cable to move the dongle away from the Pi — the Pi's USB 3.0 ports emit RF noise in the 2.4 GHz band. Prefer USB 2.0 ports.

> **Tip:** Use the persistent `/dev/serial/by-id/` path instead of `/dev/ttyUSB0` — stable across reboots.

---

## Troubleshooting

### Multiple Zigbee devices unavailable simultaneously

1. Check the dongle is detected: `ls /dev/ttyUSB* /dev/ttyACM*` via SSH
2. Check ZHA / Z2M integration status in HA
3. Try unplugging and replugging the dongle
4. Restart the Zigbee integration in HA

### Dongle changed USB port after reboot

1. SSH into server: `ls -la /dev/serial/by-id/`
2. Update the integration config to use the by-id path
3. This path is stable across reboots

### Poor Zigbee range / interference

1. Move the dongle away from the Pi using a USB extension cable
2. Check channel conflicts with Wi-Fi (Zigbee channels 15, 20, 25 minimize overlap)

---

## Replacing the Coordinator (Network Migration)

> **Warning:** A coordinator swap may require re-pairing all devices if the network cannot be migrated.

### With ZHA

1. Export backup: ZHA → ⋮ → Download network settings
2. Plug in the new coordinator
3. Restore the backup during new coordinator setup

### With Zigbee2MQTT

1. Save `coordinator_backup.json` from Z2M data folder
2. Configure the new coordinator and import the backup
