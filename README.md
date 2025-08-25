# Wireless Buzzer with ESP32-C6 + Home Assistant

This project is a battery-powered, Wi-Fi-connected buzzer built using an ESP32-C6 and controlled via Home Assistant. I originally made it as a prank, I hid it in my sister’s room and triggered random buzzes from my phone.

Despite being a bit of fun, this device shows off practical ESPHome integrations like battery monitoring, voltage dividers, and remote triggering via Wi-Fi.

> ⚠️ Zigbee is supported on this board, and I plan to switch once my Zigbee coordinator arrives, that’ll reduce power consumption and eliminate the need for Wi-Fi entirely.

---

## Features

- ESP32-C6 powered (Seeed Studio XIAO)
- Active buzzer triggered remotely via Home Assistant
- Battery monitoring using a 200kΩ/200kΩ voltage divider
- Voltage-to-percentage lookup for Li-ion battery estimation
- Fully wireless with optional 3D-printed enclosure
- ESPHome YAML configuration using ESP-IDF framework

---

## Hardware Used

- [XIAO ESP32-C6 Dev Board](https://s.click.aliexpress.com/e/_ol3B2OP)
- [Active Buzzer (3.3V or 5V)](https://s.click.aliexpress.com/e/_oCSsUbv)
- [2 × 200kΩ resistors (for voltage divider)](https://s.click.aliexpress.com/e/_ooGuPLW)
- [Soldering Iron](https://s.click.aliexpress.com/e/_ol3ivQL)
- 3.7V Li-ion Battery
- Optional: 3D-printed case (STL included)

---

## Voltage Divider Logic

The voltage divider halves the battery voltage to ensure it's within safe range for the ESP32’s ADC (~3.3V max).

**Formula**: Vout = Vin × R2 / (R1 + R2)

With both resistors at 200kΩ: Vout = Vin × 0.5

We compensate in ESPHome by multiplying the reading by `2.0` to restore the full battery voltage.

---

## Battery Percentage Estimation

The ESP reads the voltage and uses a **piecewise linear lookup table** to estimate the battery’s charge level.

| Voltage (V) | Percentage (%) |
|-------------|----------------|
| 4.20        | 100            |
| 4.00        | 85             |
| 3.85        | 70             |
| 3.75        | 55             |
| 3.65        | 40             |
| 3.55        | 25             |
| 3.40        | 10             |
| 3.20        | 0              |

This is implemented in the `lambda` using a simple interpolation algorithm in ESPHome.

---

## ESPHome YAML Highlights

- Reads battery voltage on boot
- Updates voltage and percentage every 5 minutes
- Buzzer is controlled via a template switch
- Uses external ADC component (`adc_oneshot`)

---

## Home Assistant Integration

When added to ESPHome, the device exposes:

- `switch.buzzer` – for remote control
- `sensor.buzzer_battery_voltage` – raw battery voltage (in volts)
- `sensor.buzzer_battery_level` – calculated battery level (in %)

You can automate it, display it on your dashboard, or manually trigger buzzes from the Home Assistant UI.

---

## Files in this Repo

- `buzzer.yaml` – ESPHome config
- `Buzzer Housing 3D Print.stl` and `Buzzer Lid 3D Print.stl` – Optional 3D-printed case 
- `Buzzer Schematics.png` – Basic wiring overview

---

## Future Improvements

- Switch from Wi-Fi to Zigbee for lower power draw
- Add deep sleep when using Zigbee
- Tune the battery lookup table using real test data

---

## License

MIT - feel free to fork, modify, and share.


