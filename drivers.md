# 🛠 Sensor Drivers in a Common Style

## 📋 Available Drivers

- 🧭 [**ak09940a**](https://github.com/reznikmm/ak09940a) — Magnetic sensor  
- 🌡️ [**bme280**](https://github.com/reznikmm/bme280) — Humidity, barometric pressure, and ambient temperature sensor  
- 🧭 [**bmm150**](https://github.com/reznikmm/bmm150) — Magnetic sensor  
- 🧭 [**hmc5883**](https://github.com/reznikmm/hmc5883) — Magnetic sensor  
- 🎯 [**icm20602**](https://github.com/reznikmm/icm20602) — 3-axis gyroscope, 3-axis accelerometer  
- 💡 [**neo_pixel**](https://github.com/reznikmm/neo_pixel) — RGB LED serial driver (WS2812, SK6812, etc.)  
- 🧭 [**qmc5883**](https://github.com/reznikmm/qmc5883) — Magnetic sensor  
- 💾 [**w25x**](https://github.com/reznikmm/w25x) — SPI flash memory (W25Q16 and similar, VERY DRAFT)

---

These repositories contains **drivers for various sensors and devices, following a common style**.  
Drivers are available in two variants:

1. **HAL-based** — ready to use with STM32 HAL or similar environments.
2. **HAL-independent** — portable, hardware-agnostic, suitable for bare-metal or custom setups.

---

## 1️⃣ HAL-Based Drivers

These drivers integrate with the (Ada Drivers Library) HAL (or compatible) library and are ready for use in embedded projects that already rely on HAL.

**Usage models:**
- **Generic package** — convenient for working with a single sensor instance.
- **Tagged type** — enables creating objects for multiple sensors and handling them uniformly.

This flexibility allows both quick prototyping and building complex systems with many sensors.

```
   ┌───────────────────────┐
   │  Application Code     │
   │  (Your Project)       │
   └───────────┬───────────┘
               │ Calls
               ▼
   ┌───────────────────────┐
   │  HAL-Based Driver     │  <-- Uses ADL HAL for I/O
   │  (register encoding)  │
   └───────────┬───────────┘
               │ Calls
               ▼
   ┌───────────────────────┐
   │  HAL.I2C, HAL.SPI     │  <-- I2C, SPI HAL drivers
   └───────────┬───────────┘
               │ Implemented by Ada Drivers Library
               ▼
   ┌───────────────────────┐
   │  Hardware Access      │
   │  (SPI/I²C, etc.)      │
   └───────────────────────┘
```

---

## 2️⃣ HAL-Independent Drivers

### Low-Level Interface: `Driver.Raw`

The `<Driver>.Raw` package provides a **low-level interface** for interacting with a sensor.
It focuses on **encoding and decoding** sensor register values, while leaving the actual read/write operations to the user.

**Key properties:**
- No HAL dependency — can be used with DMA, bit-banging, or any other I/O method.
- Communication is done by reading/writing one or more bytes to/from I2C/SPI devices.
  Users may perform these read/write operations synchronously or asynchronously,
  using any method that fits their hardware setup.

---

### Purpose of `Driver.Raw`

The package defines **array subtypes** where:
- The **index** represents the register number.
- The **value** represents the register's data.

Functions in this package help prepare and interpret register values:
- `Set_*` — create values for writing to registers.
- `Get_*` — decode values read from registers.
- `Is_*` — handle boolean logic checks (e.g., whether the sensor is currently measuring or updating).

---

### User Responsibilities

You must implement:
- Reading from I2C, SPI devices.
- Writing to I2C, SPI devices.

This design keeps the driver **portable** and adaptable to various hardware platforms.

```
   ┌───────────────────────┐
   │  Application Code     │
   │  (Your Project)       │
   └───────────┬───────────┘
               │ Calls
               ▼
   ┌───────────────────────┐
   │  Raw Driver API       │  <-- Encodes/decodes register data
   │  (Driver.Raw)         │
   └───────────┬───────────┘
               │ Returns
               ▼
   ┌───────────────────────┐
   │  Data Buffers         │  <-- Prepared register read/write data
   └───────────┬───────────┘
               │ User implements
               ▼
   ┌───────────────────────┐
   │  Hardware I/O         │  <-- SPI, I2C, DMA, Bit-banging etc.
   │  (Any method)         │
   └───────────────────────┘

```

---


