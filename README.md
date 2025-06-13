This project sets up CAN communication between a **DTI HV 550 Motor Controller** and an **STM32 Nucleo F446RE** board using an **MCP2515 CAN module**. The **DTI CAN Tool** is used for monitoring, debugging, and verifying communication on the CAN network.

## 🔧 Hardware Requirements

| Component                           | Description                                                        |
|-------------------------------      |------------------------------------------------------------------  |
| **DTI HV 550 Motor Controller**     | High-voltage EV motor controller with CAN interface                |
| **STM32 Nucleo F446RE**             | ARM Cortex-M4 development board                                    |
| **MCP2515 CAN Module**              | SPI-based CAN controller + transceiver                             |
| **DTI CAN Tool**                    | For set-up and tuning                                              |
| **Termination Resistors (2x 120Ω)** | Placed at each end of the CAN bus line                             |
| **Connecting Wires**                | For SPI and power connections between STM32, MCP2515 and DTI HV 550|
| **External Power Supply**           | For powering components.                                           |


## 🔌 STM32F446RE to MCP2515 CAN Module Wiring

This section details the pin connections between the **STM32 Nucleo F446RE** and the **MCP2515 CAN module** using the SPI interface. The CAN bus is then connected to the **DTI HV 550 Motor Controller**.

---

### 📠 SPI Interface (STM32 → MCP2515)

| MCP2515 Pin | STM32 Pin             | Function                     |
|-------------|-------------------    |------------------------------|
| `SCK`       | `PA5 (SPI1_SCK)`      | SPI Clock                    |
| `MISO`      | `PA6 (SPI1_MISO)`     | SPI Master In Slave Out      |
| `MOSI`      | `PA7 (SPI1_MOSI)`     | SPI Master Out Slave In      |
| `CS`        | `PB6` (user-defined)  | Chip Select (active low)     |
| `INT`       | `PA0` (optional)      | Interrupt from MCP2515       |

---

### ⚡ Power and Ground

| MCP2515 Pin | STM32 / Power Supply | Notes                                  |
|-------------|----------------------|----------------------------------------|
| `VCC`       | `5V` or `3.3V`       | Confirm logic compatibility            |
| `GND`       | `GND`                | Common ground between all devices      |

---

### 🧷 CAN Bus Wiring (MCP2515 ↔ DTI HV 550)

| MCP2515 Pin | Connects To         | Description                 |
|-------------|---------------------|-----------------------------|
| `CANH`      | DTI HV 550 `CANH`   | CAN High line               |
| `CANL`      | DTI HV 550 `CANL`   | CAN Low line                |

- Use **120Ω termination resistors** at **both ends** of the CAN bus.
- Connect the **DTI CAN Tool** in parallel to monitor traffic on the same CAN lines.

---
