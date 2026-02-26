## How Sensors Connect to an MCU?

::: {.table-wrap}

| Interface | Device Type          | Specific Model         |
|:---------|:----------------------|:-----------------------|
| I²C      | Light Sensor          | BH1750                 |
| SPI      | RFID Reader           | MFRC522                |
| UART     | Bluetooth Module      | HC-05                  |
| Analog   | Temperature Sensor    | LM35                   |
| PWM DAC  | DAC Emulation         | STM32 PWM + RC Filter  |

:::

---

## SPI — Serial Peripheral Interface

![](/media/spi.svg)

Four primary signals:

- SCLK (Clock)  
- MOSI (Master → Slave)  
- MISO (Slave → Master)  
- CS (Chip Select)

---

## SPI Core Idea

- Master drives the clock  
- Bits shift on clock edges  
- Data flows in both directions simultaneously  

. . .

Clock ticks → Bits shift → Data captured

