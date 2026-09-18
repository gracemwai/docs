#  Hardware & State of Health

Probe uses a physical battery-testing system to collect electrical and environmental measurements from used Lithium-Ion batteries.

Battery condition is determined from real sensor measurements and a **hardcoded State of Health (SoH) calculation**, rather than a trained AI or machine-learning model.

---

##  Hardware

### Battery Sensors

Sensors are connected to the Lithium-Ion battery during testing and capture measurements in real time.

The system measures:

* **Temperature**
* **Voltage**
* **Current**

These measurements provide the raw data used to evaluate the battery's condition.

### ESP32

The **ESP32** acts as the microcontroller for the testing hardware.

It:

1. Reads measurements from the connected sensors.
2. Packages the sensor readings.
3. Publishes the raw telemetry to the **🐝HiveMQ MQTT broker**.

```text
Lithium-Ion Battery
        ↓
     Sensors
        ↓
      ESP32
        ↓
      HiveMQ
```

---

##  Data Pipeline

The hardware data pipeline connects the physical battery to the Probe backend and recycler dashboard.

```text
┌──────────────────────┐
│   Lithium-Ion       │
│      Battery        │
└──────────┬───────────┘
           ↓
┌──────────────────────┐
│   Temperature        │
│   Voltage            │
│   Current Sensors    │
└──────────┬───────────┘
           ↓
┌──────────────────────┐
│        ESP32         │
│  Reads & publishes   │
│     telemetry        │
└──────────┬───────────┘
           ↓
┌──────────────────────┐
│       HiveMQ         │
│     MQTT Broker      │
└──────────┬───────────┘
           ↓
┌──────────────────────┐
│   SoH Calculation    │
│       Module         │
└──────────┬───────────┘
           ↓
      Tested Battery
          Data
        ↙     ↘
       ↓       ↓
   FastAPI    Recycler
   Backend    Dashboard
       ↓
   PostgreSQL
```

### Pipeline Flow

**1. Sensor Measurement**

The battery sensors capture temperature, voltage, and current while the battery is being tested.

**2. Telemetry Transmission**

The ESP32 receives the measurements and publishes the raw readings to HiveMQ using MQTT.

**3. State of Health Calculation**

The SoH Module subscribes to the raw telemetry from HiveMQ and processes the measurements using a **hardcoded formula**.

The calculation produces tested battery information, including the battery's State of Health and classification.

**4. Data Distribution**

The resulting tested battery data is:

* Published for the recycler dashboard.
* Sent to the FastAPI backend.
* Verified and stored in PostgreSQL.

---

##  State of Health (SoH)

Probe does not use an AI model to determine battery condition.

Instead, the system uses a predefined **hardcoded formula** based on the measurements collected from the physical testing hardware.

```text
Sensor Measurements
       ↓
Temperature
Voltage
Current
       ↓
Hardcoded SoH Formula
       ↓
State of Health
       ↓
Battery Classification
```

The calculated result is then associated with the tested battery and made available to the rest of the Probe platform.

---

##  Hardware-to-Platform Flow

The complete hardware-to-platform flow can be summarized as:

```text
Battery
   ↓
Sensors
   ↓
ESP32
   ↓
HiveMQ
   ↓
SoH Module
   ↓
FastAPI
   ↓
PostgreSQL
   ↓
Recycler Dashboard
```

This architecture allows Probe to connect **physical battery testing with digital inventory and reuse workflows**, giving recyclers access to battery condition data and enabling suitable batteries to move toward reuse.
