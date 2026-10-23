<div align="center">
<img src="workshop instruction/images/techsource_logo.png" alt="TechSource Systems" width="400">
</div>

[![Buka di MATLAB Online](https://www.mathworks.com/images/responsive/global/open-in-matlab-online.svg)](https://matlab.mathworks.com/open/github/v1?repo=TechSource-Ascendas/SimulinkIoTThingSpeakWS-Plan)

# Hands-On Workshop — IoT dengan Simulink, ThingSpeak & ESP32

> 🇬🇧 English version? Open [README.md](README.md)

## Model — Simulink IoT ThingSpeak dengan ESP32 (ID)

Repositori ini berisi seluruh materi peserta & instruktur untuk **workshop hands-on** di mana mahasiswa teknik membangun model Simulink® yang membaca data sensor pada **ESP32**, mempublikasikannya secara live ke kanal **ThingSpeak** melalui MQTT, dan membaca kembali kanalnya sendiri (loop IoT yang lengkap). Workshop disampaikan dwibahasa (ID/EN).

| | |
|---|---|
| **Durasi** | 120 menit (30 menit setup IoT + 40 menit membangun model + 30 menit deploy & data live + 20 menit tantangan) |
| **Peserta** | Mahasiswa S1, latar belakang teknik |
| **Produk** | MATLAB R2026a, Simulink, Embedded Coder *(produk yang dilaporkan Dependency Analyzer MATLAB)* |
| **Dukungan ESP32 (add-on)** | Simulink Support Package for Arduino Hardware (ESP32-WROOM) — support package, bukan toolbox (`ver` tidak akan mendaftarnya) |
| **Platform** | ThingSpeak (analytics IoT MathWorks) + Wi-Fi / MQTT ESP32 |
| **Hardware** | ESP32-WROOM DevKit V1 (30-pin) — 1 board per kelompok, plus 2 kabel jumper Dupont untuk loop-back DAC → ADC |

---

## Hardware & Wiring

![Referensi pinout ESP32-WROOM-32 DevKit](workshop%20instruction/images/screenshot_wiring.png)

*Pinout ESP32-WROOM-32 DevKit — temukan input analog (pin 4 dan pin 5), output DAC (DAC1/DAC2), dan output digital (pin 2, `D2`) pada header fisik. Angka putih kecil di tepi board adalah nomor posisi header, bukan nomor GPIO.*

---

## Struktur Folder

```
SimulinkIoTThingSpeakWS-Plan/
├─ SimulinkIoTThingSpeak.prj             # Project MATLAB (atur path + konfigurasi)
├─ ComponentList_V1.0.xlsx               # Daftar komponen hardware (draf)
├─ .gitignore                            # Aturan ignore hasil code-generation MATLAB
├─ .gitattributes                        # menandai .mlx/.slx/.docx/.xlsx sebagai biner (tanpa konversi line-ending)
├─ models/
│  ├─ SimulinkIoTThingSpeak_starter.slx  # ⭐ MULAI DI SINI (kerangka simulasi, tanpa blok IoT)
│  └─ SimulinkIoTThingSpeak_complete.slx # Model referensi lengkap (kunci jawaban)
├─ requirements/
│  └─ (kosong)                           # TODO: CSV requirements
├─ tests/
│  └─ (kosong)                           # TODO: CSV kasus uji
├─ data/
│  └─ TunableParameter.m                 # parameter tunable (SensorKnob, ThresholdKnob)
├─ resources/
│  └─ project/                           # Metadata project MATLAB (auto-generated)
├─ work/                                 # Output build, di-ignore git (<model>_ert_rtw/, *.bin/.elf/.map, slprj/)
└─ workshop instruction/
   ├─ images/                            # screenshot asli MATLAB & ThingSpeak + pinout ESP32
   │  ├─ screenshot_starter_model.png
   │  ├─ screenshot_complete_model.png
   │  ├─ screenshot_analog_input_dialog.png
   │  ├─ screenshot_compose_string_dialog.png
   │  ├─ screenshot_relational_operator_dialog.png
   │  ├─ screenshot_string_to_ascii_dialog.png
   │  ├─ screenshot_mqtt_publish_dialog.png
   │  ├─ screenshot_digital_output_dialog.png
   │  ├─ screenshot_thingspeak_read_dialog.png
   │  ├─ screenshot_hardware_settings.png
   │  ├─ screenshot_wiring.png
   │  ├─ screenshot_monitor_tune.png
   │  ├─ screenshot_thingspeak_home.png
   │  ├─ screenshot_thingspeak_channel_settings.png
   │  ├─ screenshot_thingspeak_api_keys.png
   │  ├─ screenshot_thingspeak_channel_live.png
   │  └─ techsource_logo.png
   ├─ WS1 - Simulink IoT with ThingSpeak and ESP32_id.md      # panduan langkah (ID)
   ├─ WS1 - Simulink IoT with ThingSpeak and ESP32_id.docx    # DOCX (ID)
   ├─ WS1 - Simulink IoT with ThingSpeak and ESP32_id.mlx     # Live Script (ID)
   ├─ WS1 - Simulink IoT with ThingSpeak and ESP32_en.md      # panduan langkah (EN)
   ├─ WS1 - Simulink IoT with ThingSpeak and ESP32_en.docx    # DOCX (EN)
   └─ WS1 - Simulink IoT with ThingSpeak and ESP32_en.mlx     # Live Script (EN)
```

---

## Mulai Cepat

1. Buka MATLAB dan project:
   ```matlab
   >> openProject('SimulinkIoTThingSpeak.prj')
   ```
2. Definisikan **parameter tunable** model (model lengkap dan knob membutuhkannya):
   ```matlab
   >> run('data/TunableParameter.m')   % membuat SensorKnob & ThresholdKnob (ExportedGlobal)
   ```
3. Buka model starter dan ikuti panduan:
   ```matlab
   >> open_system('models/SimulinkIoTThingSpeak_starter.slx')
   ```
4. Verifikasi support package hardware (Langkah 1 di panduan):
   ```matlab
   >> ver('simulink'), ver('ecoder')
   >> supportPackageInstaller   % pastikan "Simulink Support Package for Arduino Hardware" (ESP32)
   >> arduinolist               % pastikan board + port COM
   ```
5. Butuh model lengkap? Buka kunci jawaban:
   ```matlab
   >> open_system('models/SimulinkIoTThingSpeak_complete.slx')
   ```

---

## Model

Model **starter** (R2026a) sudah berisi kerangka simulasi — knob dashboard (12-bit, 0–4095), pemilih sumber Sim ↔ Hardware, konstanta threshold, routing GoTo/From, dan display. Peserta kemudian membangun model **lengkap**, yang menargetkan board **ESP32-WROOM (Arduino Compatible)** dengan Embedded Coder (`ert.tlc`) dalam **external (Monitor & Tune / XCP on Serial)** mode:

- **Analog input** (pin ADC 4/5) → scaling → **loop-back DAC1/DAC2**;
- **Compose String** `field1=%d&field2=%d&field3=%d` → **String to ASCII** → **WiFi MQTT Publish** (ThingSpeak, topic `channels/<ID>/publish`, **60 dtk**);
- **Relational Operator** `TemperatureData > ThresholdData` → `FanControl` → **Digital Output D2**;
- **WiFi ThingSpeak Read** (15 dtk) mengembalikan field kanal sendiri ke display.
- **Parameter tunable** — `SensorKnob` / `ThresholdKnob` (`Simulink.Parameter`, **`ExportedGlobal`**) menggerakkan dua konstanta input sensor dan dapat diubah live pada Monitor & Tune; didefinisikan di **`data/TunableParameter.m`** (`SensorKnob = 3072`, `ThresholdKnob = 2048`).

Pemetaan field (sesuai kanal ThingSpeak): **Field 1 = TemperatureData, Field 2 = ThresholdData, Field 3 = FanControl**.

> ⚠️ **Kredensial:** model yang dikirim hanya berisi **placeholder** — Wi-Fi (`YOUR_WIFI_SSID` / `YOUR_WIFI_PASSWORD`), MQTT (`YOUR_MQTT_USERNAME` / `YOUR_MQTT_PASSWORD`), dan read key ThingSpeak (`YOUR_READ_API_KEY`). Setiap kelompok memasukkan nilai **miliknya sendiri** sebelum menjalankan (Langkah 4 panduan menunjukkan lokasinya); Channel ID demo (`3478187`) disimpan hanya sebagai referensi. Jangan pernah men-commit kunci asli.

---

## Status Konten

- ✅ `models/SimulinkIoTThingSpeak_starter.slx` + `models/SimulinkIoTThingSpeak_complete.slx`
- ✅ `workshop instruction/` — panduan WS1 dalam **MD + DOCX + MLX** (ID & EN)
- ✅ `workshop instruction/images/` — screenshot asli MATLAB & ThingSpeak, plus referensi pinout ESP32-WROOM-32
- 🔜 `requirements/` CSV — turunkan dari sinyal model (daftar REQ)
- 🔜 `tests/` CSV — kasus uji (pemetaan field, publish/read-back)
- ✅ `data/TunableParameter.m` — definisi parameter tunable (`SensorKnob`, `ThresholdKnob`, ExportedGlobal)
- 🔜 `ComponentList_V1.0.xlsx` — draf sudah ada; lengkapi sesuai hardware per kelompok (baru 2 baris)

---

## Prasyarat

- MATLAB R2026a dengan **Simulink** dan **Embedded Coder** (produk yang dilaporkan Dependency Analyzer sebagai kebutuhan), ditambah **Simulink Support Package for Arduino Hardware** yang dipasang sebagai add-on (dukungan ESP32).
- Akun **ThingSpeak** gratis dan satu kanal privat (kunci Write / Read / MQTT) per kelompok.
- 1 **ESP32-WROOM DevKit V1 (30-pin)** per kelompok, beserta kabel micro-USB (daya + external mode XCP).
- **2 kabel jumper Dupont female-to-female** — satu-satunya wiring eksternal yang dibutuhkan: **DAC1 (GPIO25) → input analog 4 (GPIO32)** dan **DAC2 (GPIO26) → input analog 5 (GPIO33)**, membentuk loop-back DAC → ADC.
- **Tidak diperlukan sensor, potensiometer, breadboard, maupun LED eksternal.** Model membaca kembali output DAC-nya sendiri melalui ADC, dan `FanControl` menggerakkan **LED onboard pada GPIO2 (`D2`)**.

---

*Dibuat untuk Workshop Hands-On · TechSource Systems*
