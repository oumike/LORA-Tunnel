# Seeed Wio Tracker L2 target

PlatformIO environment `wio-tracker-l2` (`-D LORA_TUNNEL_WIO_TRACKER_L2`) builds LORA Tunnel for the **Seeed Wio Tracker L2**. It has an ESP32-S3 with 16 MB flash and 8 MB PSRAM, an SX1262, an L76K GNSS, a 320 × 240 NV3031B QSPI touchscreen and a microSD slot. Board constants are in the `LORA_TUNNEL_WIO_TRACKER_L2` branch of `include/board_config.h`. The hardware layer is `src/hardware_wio_tracker_l2.cpp`.

The pin map and bring-up sequence come from the Seeed board support used by the wadamesh and Camillia-MT Wio Tracker L2 variants. **This target compiles, but it has not been tested on physical hardware.**

```sh
python -m platformio run -e wio-tracker-l2
```

The web flasher and release packaging do not include this target yet.

## Board configuration

| Function | Configuration |
| --- | --- |
| Shared I2C | SDA 47, SCL 48, 100 kHz: PCA9555 expander 0x21, GT911 touch 0x5D/0x14, LP5814 backlight 0x2C, ADS1115 0x48, ES8311 0x18 |
| PCA9555 bits | 0 wake button, 2 SD detect, 4/5/6 LCD control/power/reset, 8 touch reset, 9 GNSS reset, 12 audio power, 13 GNSS power, 14 SD power, 15 battery sense |
| Display | NV3031B, QSPI SCK 42, IO0–IO3 41/40/39/38, CS 46; reset through the expander |
| SD | SD_MMC 1-bit: CLK 2, CMD 3, D0 1; power on expander bit 14 |
| GNSS | L76K, ESP32 RX 18 / TX 17, 9600 baud; power and reset through the expander; no PPS |
| LoRa | SX1262, SCK 4, MISO 5, MOSI 6, CS 21, RESET 7, BUSY 8, DIO1 9; 1.8 V TCXO; no external FEM control |
| Audio | ES8311 DAC + amplifier: MCLK 10, BCLK 11, WS 12, DOUT 16. Recon alert tones are 16 kHz I2S sine bursts |
| Buttons | USER GPIO0 (short press selects, long press goes back); WAKE on expander bit 0 (next) |
| Battery | ADS1115 channel 0, ×2 divider while expander bit 15 is high |

Differences from the Heltec target:
- The display, SD card and LoRa radio are on three independent buses.
- SD uses `SD_MMC` (see `include/sd_card.h`).
- Recon tones go through the codec, not a piezo.

## Display orientation

**Settings → Display** switches between landscape (the default) and portrait. The choice is saved in NVS. Landscape scales the 240 × 320 touch layout onto the 320 × 240 panel, and touches are mapped back. Host UI checks cover both orientations (`tests/ui_host/heltec_mesh_ui.cpp`, `landscape-*` previews).

## Hardware acceptance still required

None of the following has been checked on a physical unit:
- Display colour, offset and the portrait rotation direction.
- GT911 coordinate mapping in both orientations, all four corners.
- Both buttons and sleep/wake.
- SD mount, writes and power cycling.
- GNSS bytes, fix and time.
- LoRa reception.
- Alert tone volume.
- Battery voltage calibration.
- PSRAM size.

A build pass is not evidence for any of these.
