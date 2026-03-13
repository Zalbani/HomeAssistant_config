# NABU CASA ZBT-2 — Thread / Matter Coordinator

## References

| Field | Value |
|-------|-------|
| Model | NABU CASA Home Assistant Connect ZBT-2 |
| Reference | ZBT-2 |
| Protocol | Zigbee 3.0 + Thread / Matter |
| Price | ~45 € |
| Purchase link | [domadoo.fr](https://www.domadoo.fr/en/zigbee-dongle/8542-nabu-casa-home-assistant-connect-zigbee-usb-adapter-zbt-2-0860011789741.html) |

---

## Current Setup

| Field | Value |
|-------|-------|
| USB port | _(e.g. `/dev/ttyACM0` or `/dev/serial/by-id/usb-Nabu_Casa_...`)_ |
| Role | Thread border router |
| HA integration | Thread (via Home Assistant OS) |
| Location | _(to be filled)_ |

> **Tip:** Use a USB extension cable to move the dongle away from the Pi — the Pi's USB 3.0 ports emit RF noise in the 2.4 GHz band. Prefer USB 2.0 ports.

---

## Troubleshooting

### Thread / Matter devices not discovered

1. Check the ZBT-2 is detected: `ls /dev/ttyUSB* /dev/ttyACM*` via SSH
2. Check the Thread integration status in HA (Settings → System → Hardware)
3. Try unplugging and replugging the dongle
4. Restart the Thread integration in HA

### Thread border router not visible to Matter devices

1. Verify the ZBT-2 is configured as a border router in HA
2. Ensure the HA server and Matter devices are on the same network segment
3. Check that IPv6 is enabled on the local network (Thread requires IPv6)

---

## Replacing the Coordinator

1. Plug in the new Thread-compatible dongle
2. In HA: Settings → System → Hardware → configure the new device as Thread border router
3. Matter devices should automatically reconnect via the new border router
