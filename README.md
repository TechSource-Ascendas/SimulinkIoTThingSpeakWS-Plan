<div align="center">
<img src="workshop instruction/images/techsource_logo.png" alt="TechSource Systems" width="400">
</div>

[![Open in MATLAB Online](https://www.mathworks.com/images/responsive/global/open-in-matlab-online.svg)]([https://matlab.mathworks.com/](https://matlab.mathworks.com/open/github/v1?repo=TechSource-Ascendas/SimulinkIoTThingSpeakWS-Plan))

# Hands-On Workshop — IoT with Simulink, ThingSpeak & ESP32

> 🇮🇩 Bahasa Indonesia? Buka [README_id.md](README_id.md)

## Model — Simulink IoT ThingSpeak with ESP32 (EN)

This repository holds the complete participant + instructor material for a **hands-on workshop** where undergraduate engineering students build a Simulink® model that acquires sensor data on an **ESP32**, publishes it live to a **ThingSpeak** channel over MQTT, and reads its own channel back (a full IoT loop). The workshop is delivered bilingual (EN/ID).

| | |
|---|---|
| **Duration** | 120 minutes (30 IoT setup + 40 model build + 30 deploy & live data + 20 challenge) |
| **Audience** | Undergraduate, engineering background |
| **Products** | MATLAB R2026a, Simulink, Embedded Coder *(the products reported by the MATLAB Dependency Analyzer)* |
| **ESP32 support (add-on)** | Simulink Support Package for Arduino Hardware (ESP32-WROOM) — a support package, not a toolbox (`ver` will not list it) |
| **Platform** | ThingSpeak (MathWorks IoT analytics) + ESP32 Wi-Fi / MQTT |
| **Hardware** | ESP32-WROOM (Arduino-compatible) development board — 1 board per group |

---

## Folder Structure

```
SimulinkIoTThingSpeakWS-Plan/
├─ SimulinkIoTThingSpeak.prj             # MATLAB project (path + configuration)
├─ ComponentList_V1.0.xlsx               # Hardware & component list (draft)
├─ .gitignore                            # MATLAB code-generation ignore rules
├─ models/
│  ├─ SimulinkIoTThingSpeak_starter.slx  # ⭐ START HERE (simulation scaffolding, no IoT blocks)
│  └─ SimulinkIoTThingSpeak_complete.slx # Completed reference model (answer key)
├─ requirements/
│  └─ (empty)                            # TODO: requirements CSV
├─ tests/
│  └─ (empty)                            # TODO: validation test-case CSV
├─ data/
│  └─ TunableParameter.m                 # tunable params (SensorKnob, ThresholdKnob)
├─ resources/
│  └─ project/                           # MATLAB project metadata (auto-generated)
├─ work/                                 # Build output, git-ignored (<model>_ert_rtw/, *.bin/.elf/.map, slprj/)
└─ workshop instruction/
   ├─ images/                            # genuine MATLAB + ThingSpeak screenshots
   │  ├─ screenshot_starter_model.png
   │  ├─ screenshot_complete_model.png
   │  ├─ screenshot_thingspeak_home.png
   │  ├─ screenshot_thingspeak_channel_settings.png
   │  ├─ screenshot_thingspeak_api_keys.png
   │  ├─ screenshot_thingspeak_channel_live.png
   │  └─ techsource_logo.png
   ├─ WS1 - Simulink IoT with ThingSpeak and ESP32_en.md      # step-by-step (EN)
   ├─ WS1 - Simulink IoT with ThingSpeak and ESP32_id.md      # step-by-step (ID)
   ├─ WS1 - Simulink IoT with ThingSpeak and ESP32_en.docx    # DOCX (EN)
   └─ WS1 - Simulink IoT with ThingSpeak and ESP32_id.docx    # DOCX (ID)
```

---

## Quick Start

1. Open MATLAB and the project:
   ```matlab
   >> openProject('SimulinkIoTThingSpeak.prj')
   ```
2. Define the model's **tunable parameters** (the completed model and the knobs need them):
   ```matlab
   >> run('data/TunableParameter.m')   % creates SensorKnob & ThresholdKnob (ExportedGlobal)
   ```
3. Open the starter model and follow the guide:
   ```matlab
   >> open_system('models/SimulinkIoTThingSpeak_starter.slx')
   ```
4. Verify the hardware support package (Step 0 in the guide):
   ```matlab
   >> ver('simulink'), ver('ecoder')
   >> supportPackageInstaller   % confirm "Simulink Support Package for Arduino Hardware" (ESP32)
   >> arduinolist               % confirm board + COM port
   ```
5. Need the complete model? Open the answer key:
   ```matlab
   >> open_system('models/SimulinkIoTThingSpeak_complete.slx')
   ```

---

## The Model

The **starter** model (R2026a) ships with the simulation scaffolding already wired — dashboard knobs (12-bit, 0–4095), a Sim ↔ Hardware source selector, threshold constant, GoTo/From signal routing, and displays. Participants then build the **completed** model, which targets the **ESP32-WROOM (Arduino Compatible)** board with Embedded Coder (`ert.tlc`) in **external (Monitor & Tune / XCP on Serial)** mode:

- **Analog inputs** (ADC pins 4/5) → scaling → **DAC1/DAC2** loop-back;
- **Compose String** `field1=%d&field2=%d&field3=%d` → **String to ASCII** → **WiFi MQTT Publish** (ThingSpeak, topic `channels/<ID>/publish`, **60 s**);
- **Relational Operator** `TemperatureData > ThresholdData` → `FanControl` → **Digital Output D2**;
- **WiFi ThingSpeak Read** (15 s) returns the channel's own fields to the displays.
- **Tunable parameters** — `SensorKnob` / `ThresholdKnob` (`Simulink.Parameter`, **`ExportedGlobal`**) drive the two sensor-input constants and can be tuned live in Monitor & Tune; defined in **`data/TunableParameter.m`** (`SensorKnob = 3072`, `ThresholdKnob = 2048`).

Field mapping (matches the ThingSpeak channel): **Field 1 = TemperatureData, Field 2 = ThresholdData, Field 3 = FanControl**.

> ⚠️ **Credentials:** the shipped models contain **placeholders only** — Wi-Fi (`YOUR_WIFI_SSID` / `YOUR_WIFI_PASSWORD`), MQTT (`YOUR_MQTT_USERNAME` / `YOUR_MQTT_PASSWORD`), and the ThingSpeak read key (`YOUR_READ_API_KEY`). Each group enters its **own** values before running (the guide's Step 3 shows where); the demo channel ID (`3478187`) is kept only as a reference. Never commit real keys.

---

## Content Status

- ✅ `models/SimulinkIoTThingSpeak_starter.slx` + `models/SimulinkIoTThingSpeak_complete.slx`
- ✅ `workshop instruction/` — WS1 guide MD + DOCX (EN & ID)
- ✅ `workshop instruction/images/` — genuine MATLAB & ThingSpeak screenshots
- 🔜 `requirements/` CSV — derive from the model signals (REQ list)
- 🔜 `tests/` CSV — validation cases (field mapping, publish/read-back)
- ✅ `data/TunableParameter.m` — tunable parameter definitions (`SensorKnob`, `ThresholdKnob`, ExportedGlobal)
- 🔜 `ComponentList_V1.0.xlsx` — draft present; complete per-group hardware (2 rows so far)

---

## Prerequisites

- MATLAB R2026a with **Simulink** and **Embedded Coder** (the products the Dependency Analyzer reports as required), plus the **Simulink Support Package for Arduino Hardware** installed as an add-on (ESP32 support).
- A free **ThingSpeak** account and a private channel (Write / Read / MQTT keys) per group.
- 1 ESP32-WROOM development board per group + potentiometer(s), breadboard, and an LED/buzzer on D2 for the FanControl demo.

---

*Created for Hands-On Workshop · TechSource Systems*
