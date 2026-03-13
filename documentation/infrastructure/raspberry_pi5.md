# Raspberry Pi — Home Assistant Server

## Installed Configuration

| Component | Model | Price | Link |
|-----------|-------|-------|------|
| Board | Raspberry Pi 5 — 8 GB | ~134 € | [kubii.com](https://www.kubii.com/en/nano-computers/4106-1832-raspberry-pi-5-3272496315938.html#/ram-8_gb) |
| Power supply | Raspberry Pi 27W USB-C | ~12 € | [kubii.com](https://www.kubii.com/en/power-supplies/4107-1890-power-supply-raspberry-pi-27w-usb-c-3272496315761.html) |
| Case | Official case for Raspberry Pi 5 with fan | ~10 € | [kubii.com](https://www.kubii.com/en/ventilated-smart-cases/4108-1903-official-case-for-raspberry-pi-5-with-fan-3272496316638.html) |
| Storage | _(to be filled — SD card or NVMe SSD)_ | — | — |

## Alternative / Previous Configuration

| Component | Model | Price | Link |
|-----------|-------|-------|------|
| Board | Raspberry Pi 4 Model B — 4 GB | ~81 € | [kubii.com](https://www.kubii.com/en/nano-computers/2772-raspberry-pi-4-model-b-4gb-5056561800349.html) |
| Power supply | Official 15.3W USB-C | ~10 € | [kubii.com](https://www.kubii.com/en/power-supplies/2678-1049-official-power-supply-for-raspberry-pi-4-153w-usb-c-3272496300002.html) |
| Case | Official case | ~4 € | [kubii.com](https://www.kubii.com/en/cases-mountings/2727-1079-case-for-raspberry-pi4-3272496299290.html) |
| Storage | SanDisk 64 GB Extreme PRO microSDXC | ~27 € | [amazon.fr](https://www.amazon.fr/gp/aw/d/B09X7BYSFG) |

---

## Network

| Field | Value |
|-------|-------|
| Static IP | `192.168.x.x` _(to be filled)_ |
| Hostname | _(to be filled)_ |
| SSH access | `ssh root@<IP>` |
| HA web UI | `http://<IP>:8123` |
| Storage | _(SD card / SSD — to be filled)_ |

---

## Zigbee Dongle

The Zigbee coordinator is plugged into a USB port of this server. See [zigbee_coordinator.md](zigbee_coordinator.md).

---

## Troubleshooting

### HA unreachable (web UI not loading)

1. Check the server is powered on (LED on board)
2. Check network cable / Wi-Fi connection
3. SSH in: `ssh root@<IP>`
4. Check HA status: `ha core info`
5. Restart HA: `ha core restart`

### HA slow or unresponsive

1. Check CPU/memory: `htop` via SSH
2. Check storage usage: `df -h`
3. Check HA logs for errors: `ha core logs`

### System not booting

1. Check power supply (use the official 27W USB-C — insufficient power causes instability)
2. Check SD card / SSD integrity — try reflashing if corrupted
3. Check for red/green LED patterns on the Pi for error codes

---

## Maintenance

### Backup

Settings → System → Backups → Create backup
Schedule automatic backups (recommended: daily, keep last 7)

### Update Home Assistant

Settings → System → Updates

### SSH access

```bash
ssh root@<IP>
ha core restart       # Restart HA Core
ha core logs          # View logs
ha core check         # Check YAML configuration
ha os update          # Update HA OS
```
