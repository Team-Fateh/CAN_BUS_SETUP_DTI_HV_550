## 🔧 Hardware Requirements

This project sets up CAN communication between a **DTI HV 550 Motor Controller** and an **STM32 Nucleo F446RE** board using an **MCP2515 CAN module**. The **DTI CAN Tool** is used for monitoring, debugging, and verifying communication on the CAN network.

### 📦 Components List

| Component                           | Description / Notes                                              |
|-------------------------------      |-------------------------------------------------------------------|
| **DTI HV 550 Motor Controller**     | High-voltage EV motor controller with CAN interface              |
| **STM32 Nucleo F446RE**             | ARM Cortex-M4 development board, acts as CAN master/slave        |
| **MCP2515 CAN Module**              | SPI-based CAN controller + transceiver (e.g., TJA1050)            |
| **DTI CAN Tool**                    | CAN bus interface and software for message analysis & debugging  |
| **Termination Resistors (2x 120Ω)** | Placed at each end of the CAN bus line  
