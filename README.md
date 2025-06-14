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

<img src="Images/STM32xMCP2515.jpg" alt="Setup" width="400"/>

---

### 📠 SPI Interface (STM32 → MCP2515)

| MCP2515 Pin | STM32 Pin             | Function                     |
|-------------|-------------------    |------------------------------|
| `SCK`       | `PA5 (SPI1_SCK)`      | SPI Clock                    |
| `MISO`      | `PA6 (SPI1_MISO)`     | SPI Master In Slave Out      |
| `MOSI`      | `PA7 (SPI1_MOSI)`     | SPI Master Out Slave In      |
| `CS`        | `PB6` (user-defined)  | Chip Select (active low)     |
| `INT`       | `PA0` (optional)      | Interrupt from MCP2515       |
<img src="Images/stm32_mcp.jpg" alt="Setup" width="400"/>

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
- Create a **New STM32 Project**
- Select board: `NUCLEO-F446RE`
- Name your project (e.g., `MCP2515_CAN_DTI`)

---

### ⚙️ 2. System Configuration

#### SYS

- **Debug Interface**: `Serial Wire`

<img src="Images/sys.png" alt="Setup" width="900"/>

#### RCC

- **High-Speed Clock (HSE)**: `Crystal/Ceramic Resonator`
<img src="Images/rcc.png" alt="Setup" width="900"/>

---

### ⏰ 3. Clock Configuration

- Open the **Clock Configuration** tab
- Set the system clock (`HCLK`) to **180 MHz**
- Let STM32CubeIDE auto-calculate PLL settings
<img src="Images/clockconfig.png" alt="Setup" width="900"/>

### ⚡ Why Set STM32F446RE System Clock to 180 MHz?

Setting the STM32F446RE to **180 MHz** (its maximum frequency) improves performance across the entire project:

| ✅ Benefit                  | 🚀 Why It Matters                                     |
|----------------------------|--------------------------------------------------------|
| **SPI Clock Headroom**     | Enables faster communication with MCP2515 (up to 10 MHz) |
| **Faster CAN Frame Handling** | Ensures real-time updates from DTI HV 550 Motor Controller |
| **Efficient HAL Operations** | More accurate delays, better timing for CAN & display |
| **Better UI Performance**  | Quicker screen updates on Nextion display              |
---

### 🧩 4. Peripheral Pinout & Configuration

#### 🔄 SPI1 Configuration

Enable SPI1 under **Connectivity**:

- Mode: `Full-Duplex Master`

### 🛠️ Why Not Use Higher SPI Speeds?

| Reason                  | Explanation                                |
|-------------------------|--------------------------------------------|
| 🧱 **MCP2515 Limit**     | Absolute maximum SPI speed = **10 MHz**    |
| 🧪 **Stability over Speed** | Lower SPI clock = fewer communication errors |
| ⚙️ **CubeIDE Configuration** | Prescaler selected to stay **safely below 10 MHz** |

> Recommended SPI Clock: **≤ 10 MHz**  
> In this project: **5.625 MHz** (Prescaler = 16)

**Pin Assignments:**

| SPI Signal | STM32 Pin |
|------------|------------|
| SCK        | `PA5`      |
| MISO       | `PA6`      |
| MOSI       | `PA7`      |

<img src="Images/SPI.png" alt="Setup" width="900"/>

---


#### 🧷 GPIO Configuration

| Function       | Pin   | Mode         |
|----------------|--------|--------------|
| `CAN_CS`       | `PA4` | GPIO_Output  |
<img src="Images/GPIO.png" alt="Setup" width="400"/>

---

#### 🛰️ UART2 (Optional for Debug)

- Enable USART2
- Baud Rate: 115200

| Signal | Pin   |
|--------|--------|
| TX     | `PA2` |
| RX     | `PA3` |

<img src="Images/usart2.png" alt="Setup" width="900"/>

---

### 🛣️ CAN1 (Optional - STM32 Native CAN)

If using STM32 CAN features (`CAN_TxHeaderTypeDef`, `CAN_RxHeaderTypeDef`), enable `CAN1` in CubeMX.

#### ✅ Pin Assignments (STM32F446RE)

| Function | STM32 Pin | Alternate Function |
|----------|------------|--------------------|
| CAN_TX   | `PB9`      | `AF9`              |
| CAN_RX   | `PB8`      | `AF9`              |

> 💡 These are required only if you're using STM32’s internal CAN controller and HAL CAN APIs like `HAL_CAN_AddTxMessage()`.

<img src="Images/can.png" alt="Setup" width="900"/>

---

### 🛠️ 5. Generate Code

- Go to **Project > Generate Code**
- Select toolchain: `STM32CubeIDE`

This generates:

| File        | Purpose                             |
|-------------|-------------------------------------|
| `main.c`    | Main application logic              |
| `spi.c`     | SPI setup for MCP2515               |
| `gpio.c`    | GPIOs including CS and INT          |
| `usart.c`   | UART debug output (optional)        |

<img src="Images/code_gen.png" alt="Setup" width="900"/>

---


