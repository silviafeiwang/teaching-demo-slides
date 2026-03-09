## 3 Main Steps

::: {.columns}

::: {.column width="40%"}
![](/media/pi.jpg){width=90%}
:::

::: {.column width="60%"}
![](/media/bme280.jpg){width=50%}
:::

:::

::: {.columns}

::: {.column width="60%"}
- Access the Interface
- Configure Communication Parameters
- Transfer Data
:::

::: {.column width="40%"}
::: {.fragment}
**When our code runs, what actually happens on the wires?**
:::
:::

:::





---

## An SPI Transaction

<!-- ::: {.columns}

::: {.column width="50%"}
![](/media/spi-multi-cs.png){width=100%}
<figcaption>[Source: https://learn.sparkfun.com/tutorials/serial-peripheral-interface-spi/chip-select-cs.](https://learn.sparkfun.com/tutorials/serial-peripheral-interface-spi/chip-select-cs)</figcaption>
::: -->

<!-- ::: {.column width="50%"} -->
- Pulls **CS low** to select device
- Generates **clock pulses** on SCLK 
- On each clock edge:
	- One bit **shifts out** on PICO
	- One bit **shifts in** on POCI
- Releases **CS high**
<!-- ::: -->

<!-- ::: -->

---
