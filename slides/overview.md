## Demo

Raspberry Pi + BME280 Sensor

---

## Main Steps

- Establish communication
- The setup
- The data transfer

::: {.fragment}
> How bits travel in the wires?
:::

---

# Serial Communication

- One bit at a time
- Sequentially

::: {.fragment}
**Protocol**

UART, I²C, CAN, <span class="fragment highlight-red"> SPI </span>
:::

---

# The 4 Wires

- (CS) Chip Select
- (SCLK) Serial clock
- (PICO) Peripheral in  & controller out
- (POCI) Peripheral out & controller in

---

# 4 Modes for Timing Patterns

::: {.fragment data-fragment-index="1"}
- (CPOL) Clock Polarity <span class="fragment" data-fragment-index="2"> -> idle state </span>
- (CPHA) Clock phase <span class="fragment" data-fragment-index="3" > -> 1st/2nd edge </span>
:::

::: {.fragment data-fragment-index="10"}

> 2 binary configuration -> 4 modes

:::

---

# Recap

- The 4 wires
- The 4 modes

---

# Back to the code
