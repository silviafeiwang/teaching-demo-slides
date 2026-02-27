::: {.table-wrap}

| Interface | Device Type        | Specific Model        |
| :-------- | :----------------- | :-------------------- |
| I²C       | Light Sensor       | BH1750                |
| SPI       | RFID Reader        | MFRC522               |
| UART      | Bluetooth Module   | HC-05                 |
| Analog    | Temperature Sensor | LM35                  |
| PWM DAC   | DAC Emulation      | STM32 PWM + RC Filter |

:::



## 4-Wire SPI Model  {auto-animate=true}

::: {}
::: {.columns}

::: {.column width="50%"}
![](/media/spi-one-demo.png){fig-align="center"}

 <div style="
      position:absolute;
      top:180px;      /* adjust */
      left:25px;     /* adjust */
      width:500px;    /* adjust */
      height:350px;   /* adjust */
      background:#F7F8F6;
  "></div>

::: aside
<figcaption>
[https://learn.sparkfun.com/tutorials/serial-peripheral-interface-spi/chip-select-cs (CC BY-SA 4.0).](https://learn.sparkfun.com/tutorials/serial-peripheral-interface-spi/chip-select-cs)
</figcaption>
:::
:::

::: {.column width="50%"}

:::
:::
:::

## 4-Wire SPI Model  {transition="none"}

::: {}
::: {.columns}

::: {.column width="50%"}
![](/media/spi-one-demo.png){fig-align="center"}

 <div style="
      position:absolute;
      top:180px;      /* adjust */
      left:125px;     /* adjust */
      width:420px;    /* adjust */
      height:350px;   /* adjust */
      background:#F7F8F6;
  "></div>

::: aside
<figcaption>
[https://learn.sparkfun.com/tutorials/serial-peripheral-interface-spi/chip-select-cs (CC BY-SA 4.0).](https://learn.sparkfun.com/tutorials/serial-peripheral-interface-spi/chip-select-cs)
</figcaption>
:::
:::

::: {.column width="50%"}

:::
:::
:::

## 4-Wire SPI Model  {auto-animate=true}

::: {}
::: {.columns}

::: {.column width="50%"}
![](/media/spi-one.png){fig-align="right"}
![](/media/spi-demo.png)
<figcaption>
[https://learn.sparkfun.com/tutorials/serial-peripheral-interface-spi/chip-select-cs (CC BY-SA 4.0).](https://learn.sparkfun.com/tutorials/serial-peripheral-interface-spi/chip-select-cs)
</figcaption>
:::




---

---

## Code Example: Blink an LED

::: {.columns}

::: {.column width="50%"}
a demo image or video 

- input or output something? output
- digital or analog? digital

:::

::: {.column width="50%"}
code snippet
:::

:::


---

## Analog Interfacing with ADC {.smaller}

::: {.columns}

::: {.column width="50%"}
- Real-world signals are often analog (e.g., temperature sensors)
- An ADC is used to convert these signals to digital values
- Key items:
   - Sampling rate: How frequently the signal is sampled
   - Resolution: The number of bits the ADC uses (e.g., 10 bit -> 1024 values)
:::

::: {.column width="50%"}
example figures + interaction
:::

:::

---

## Code Example -- Read Temp Sensor

::: {.columns}

::: {.column width="50%"}
a demo image or video 

- input or output something? input
- digital or analog? analog

:::

::: {.column width="50%"}
code snippet
:::

:::

::: {.notes}
Can somebody help me figure out ....?
:::


| <!--     | Protocol                                                 | <span class="fragment" data-fragment-index="2">Pins</span>                     | <span class="fragment" data-fragment-index="3">Communication Type</span>                         | <span class="fragment" data-fragment-index="4">Typical Uses</span> |
| -------- | -------------------------------------------------------- | ------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------ |
| **UART** | <span class="fragment" data-fragment-index="2">2</span>  | <span class="fragment" data-fragment-index="3">Asynchronous</span>             | <span class="fragment" data-fragment-index="4">Debugging console, GPS modules, Bluetooth</span>  |
| **I²C**  | <span class="fragment" data-fragment-index="2">2</span>  | <span class="fragment" data-fragment-index="3">Synchronous, half-duplex</span> | <span class="fragment" data-fragment-index="4">Sensors, EEPROM, Real-Time Clocks</span>          |
| **SPI**  | <span class="fragment" data-fragment-index="2">4+</span> | <span class="fragment" data-fragment-index="3">Synchronous, full-duplex</span> | <span class="fragment" data-fragment-index="4">Displays, SD cards, high-speed peripherals</span> | -->                                                                |


## Step 3 — Transfer bytes {.smaller}

- Instantiate structure `spi_ioc_transfer` from `<linux/spi/spidev.h>` to let the kernel know how to perform the SPI transaction with per-transfer setting


- Full-duplex access using `SPI_IOC_MESSAGE(N)` from `<linux/spi/spidev.h>`
  - `N` indicates how many `spi_ioc_transfer` structures we are passing


```c
uint8_t tx[3] = {0};
uint8_t rx[3] = {0};

struct spi_ioc_transfer tr = {
    .tx_buf = (unsigned long)tx,
    .rx_buf = (unsigned long)rx,
    .len = sizeof(tx),
    .delay_usecs = 0,
    .speed_hz = speed,
    .bits_per_word = bits,
};
```



```{.c code-line-numbers="13"}
uint8_t tx[3] = {0};
uint8_t rx[3] = {0};

struct spi_ioc_transfer tr = {
    .tx_buf = (unsigned long)tx,
    .rx_buf = (unsigned long)rx,
    .len = sizeof(tx),
    .delay_usecs = 0,
    .speed_hz = speed,    
    .bits_per_word = bits,
};

int ret = ioctl(f_dev, SPI_IOC_MESSAGE(1), &tr);
if (ret < 1) {
    perror("SPI_IOC_MESSAGE");
}
```


::: {.fragment}
Even when reading, we must send bytes to generate clock pulses.
:::