This project sets up CAN communication between a **DTI HV 550 Motor Controller** and an **STM32 Nucleo F446RE** board using an **MCP2515 CAN module**. The **DTI CAN Tool** is used for monitoring, debugging, and verifying communication on the CAN network.

# 🔧 Hardware Requirements

| Component                           | Description                                                        |
|-------------------------------      |------------------------------------------------------------------  |
| **DTI HV 550 Motor Controller**     | High-voltage EV motor controller with CAN interface                |
| **STM32 Nucleo F446RE**             | ARM Cortex-M4 development board                                    |
| **MCP2515 CAN Module**              | SPI-based CAN controller + transceiver                             |
| **DTI CAN Tool**                    | For set-up and tuning                                              |
| **Termination Resistors (2x 120Ω)** | Placed at each end of the CAN bus line                             |
| **Connecting Wires**                | For SPI and power connections between STM32, MCP2515 and DTI HV 550|
| **External Power Supply**           | For powering components.                                           |

# HARDWARE SETUP

## 🔌 STM32F446RE to MCP2515 CAN Module Wiring

This section details the pin connections between the **STM32 Nucleo F446RE** and the **MCP2515 CAN module** using the SPI interface. The CAN bus is then connected to the **DTI HV 550 Motor Controller**.

<img src="Images/STM32xMCP2515.png" alt="Setup" width="400"/>

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
## 🔌 MCP2515 CAN MODULE

<img src="Images/MCP2515.jpg" alt="MCP2515" width="300"/>

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

## 🔌 CAN WIRING DTI COM 
<table>
  <tr>
    <td><img src="Images/Canwiring.png" alt="DTICOM" width="500"/></td>
    <td><img src="Images/DTICOM.png" alt="DTICOM" width="500"/></td>
  </tr>
</table>

## HARNESS CONNECTOR PINOUT (H) - INVERTER SIDE 
<table>
  <tr>
    <td><img src="Images/CanConnPinouts.png" alt="MCP2515"/></td>
    <td><img src="Images/TECONNEC.png" alt="TE" width="500"/></td>
  </tr>
</table>

# The Setup 
<img src="Images/theSetup.jpg" alt="TE" width="1000"/>


# 💻 Software Requirements

This section lists the necessary tools, libraries, and software configurations needed to build, flash, and test the CAN communication setup.

### 🧰 Development Tools

| Tool/Software                  | Description                                                                 |
|------------------------        |-----------------------------------------------------------------------------|
| **STM32CubeIDE**               | All-in-one IDE for STM32 development (code editing, build, flash, debug)    |
| **STM32CubeMX**                | Optional GUI tool for configuring peripherals, generating HAL boilerplate   |
| **DTI CAN Tool**               | Desktop tool provided by DTI to send/receive CAN messages and debug         |
| **Serial Terminal (optional)** | e.g. PuTTY or Tera Term, for UART-based debugging                           |

---

## 🧑‍💻 STM32CubeIDE Project Configuration

### ✅ 1. Create Project

- Open **STM32CubeIDE**
- New STM32 Project → Select `NUCLEO-F446RE`
- Name your project (e.g., `MCP2515_CAN_DTI`)

---

### ⚙️ 2. System Configuration

#### SYS

- **Debug**: `Serial Wire`

#### RCC

- **High-Speed Clock (HSE)**: `Crystal/Ceramic Resonator`

---

### ⏰ 3. Clock Configuration

- Open **Clock Configuration** tab
- Set `HCLK` = **180 MHz** (max for F446RE)
- Let Cube auto-configure PLLs

---

### 🧩 4. Peripheral Pinout & Configuration

#### SPI1 Configuration

- Enable `SPI1` under Connectivity
- Mode: `Full-Duplex Master`
- Prescaler: `32` → SPI Clock = **5.625 MHz** (safe for MCP2515)
- Pins used:
  - SCK → `PA5`
  - MISO → `PA6`
  - MOSI → `PA7`

#### GPIO Configuration

- `PA4`: GPIO_Output (Label as `CAN_CS`)
- `PA0`: GPIO_Input (Optional MCP2515 INT)

#### UART (Optional for Serial Debug)

- Enable `USART2`
  - TX: `PA2`
  - RX: `PA3`
  - Baud Rate: 115200

---

### 🛠️ 5. Generate Code

- Click **Project > Generate Code**
- Use **STM32CubeIDE** toolchain
- This generates:
  - `main.c` - user application logic
  - `spi.c` - SPI setup
  - `gpio.c` - GPIO (including CS line)
  - `usart.c` - UART debug (optional)

---
