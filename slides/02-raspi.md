## Raspberry Pi Hardware {.smaller}

::: {.columns}

::: {.column width="35%" .left-col}
::: {.nonincremental}
<div class="fragment code-block" data-fragment-index="2">
<ol class="hwlist no-number" start="1">
  <li><br></li>
  <li>microSD card slot</li>
  <li>DSI display port</li>
  <li>USB 3.0 power port</li>
  <li>Dual 4k HDMI ports</li>
  <li>CSI camera port</li>
  <li>4-pole stereo audio</li>
  <li>Dual USB 2.0 ports</li>
</ol>
</div>
:::
:::

::: {.column width="30%" .middle-col}
::: {.fragment data-fragment-index="1"}
![](/media/raspi-hardware-final.webp)
<figcaption>[8GB Raspberry Pi 4]()</figcaption>
:::
:::

::: {.column width="35%" .right-col}
::: {.nonincremental}
<div class="fragment code-block" data-fragment-index="2">
<ol class="hwlist no-number" start="11" reversed>
<li><br></li>
  <li>WiFi & Bluetooth 5.0 radio</li>
  <li><span class="fragment highlight-red" data-fragment-index="3">20x2 GPIO header</span></li>
  <li><br></li>
  <li>Gigabit ethernet port</li>
  <li>Dual USB 3.0 ports</li>
</ol>
</div>
:::
:::

:::

::: {.fragment data-fragment-index="4"}
  GPIO (General Purpose Input & Output): Programmable **digital pins** on a microcontroller
  
  - Input for **monitor**: Reads digital voltage, HIGH (≈ 3.3V) / LOW (≈ 0V)
  - Ouput for **control**: Drives digital voltage
:::

::: {.fragment data-fragment-index="5"}

:::

::: {.notes}
One powerful feature of the Raspberry Pi is the row of 20x2 GPIO pins along the top edge of the board
GPIO stands for General-Purpose Input/Output. 
These pins are a physical interface between the Raspberry Pi and the outside world

How Sensors Connect to an MCU? Microcontroller ⟷ Peripheral devices
:::

---


## Translate Analog to Digital {.smaller}

- Raspberry Pi GPIO is digital only
- Cannot measure **continuous voltage**


::: {.fragment}
ADC (Analog-to-Digital Converter): 

- Continuous voltage → Discrete digital value
- **Resolution** --- precision in amplitude<span class="fragment">, e.g., 10-bit ADC → 1024 discrete levels</span>
- **Sampling rate** --- precision in time

:::

::: {.notes}
So far, everything we've discussed — GPIO, UART, I²C, SPI —
assumes the signal is already digital.

But many physical sensors are not digital. But many environmental sensors produce analog voltage.

A gas sensor, for example, may output a voltage proportional to concentration.

That voltage is continuous. It's not just 0 or 1.

The Raspberry Pi cannot directly measure that value.
It can only detect whether a voltage crosses a threshold.

So how do we convert that continuous voltage into something the processor understands?

---

An ADC samples the input voltage and converts it into a binary number.

If it’s a 10-bit ADC, it maps the voltage range into 1024 discrete levels.

This is how an analog quantity becomes usable in software.

Resolution: How precise each reading is
Sampling Rate: How often readings are taken

Think of it like taking photos:
	•	Resolution = image sharpness (pixel density)
	•	Sampling rate = frames per second

If resolution is low → blurry values.
If sampling rate is low → you miss fast events.

---

The Raspberry Pi does not have a built-in ADC.
So we connect an external ADC chip.

That ADC performs the analog measurement.

Then it sends the digital result to the Raspberry Pi —
typically over SPI.
:::

---

## A Closer Look at the GPIO Pins {auto-animate=true .smaller} 

::: {}

![](/media/raspi-gpio.png){style="max-width:450px;"}
![](/media/raspi-gpio-legend.png){style="max-width:200px;"}
<figcaption>[https://pinout.xyz/](https://pinout.xyz/)</figcaption>

:::

## A Closer Look at the GPIO Pins {auto-animate=true .smaller}


::: {}
::: {.columns}

::: {.column width="30%"}
![](/media/raspi-gpio.png){width=100%}  
![](/media/raspi-gpio-legend.png){width=50%}                  
<figcaption>[https://pinout.xyz/](https://pinout.xyz/)</figcaption>
:::

::: {.column width="70%"}
Main serial communication protocols besides raw GPIO:<span class="fragment" data-fragment-index="9">![](/media/led.png){style="max-width:30px;"}</span>

| <span class="fragment" data-fragment-index="1">Protocol</span> | <span class="fragment" data-fragment-index="1">Pins</span> | <span class="fragment" data-fragment-index="1">Clocked?</span>          | <span class="fragment" data-fragment-index="1">Speed</span>      | <span class="fragment" data-fragment-index="1">Multi-Device?</span>           | <span class="fragment" data-fragment-index="1">Best For</span>                                                                                                                                                                                       |
| -------------------------------------------------------------- | ---------------------------------------------------------- | ----------------------------------------------------------------------- | ---------------------------------------------------------------- | ----------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| <span class="fragment" data-fragment-index="2">**UART**</span> | <span class="fragment" data-fragment-index="2">2</span>    | <span class="fragment" data-fragment-index="2">No (Asynchronous)</span> | <span class="fragment" data-fragment-index="2">Low</span>        | <span class="fragment" data-fragment-index="2">No (Point-to-point)</span>     | <span class="fragment" data-fragment-index="2">Long-distance communication</span> <span class="fragment" data-fragment-index="6">![](/media/satellite.png){style="max-width:30px;"}</span>                                                           |
| <span class="fragment" data-fragment-index="3">**I²C**</span>  | <span class="fragment" data-fragment-index="3">2</span>    | <span class="fragment" data-fragment-index="3">Yes</span>               | <span class="fragment" data-fragment-index="3">Low–Medium</span> | <span class="fragment" data-fragment-index="3">Yes (Addressed bus)</span>     | <span class="fragment" data-fragment-index="3">Many simple sensors</span>  <span class="fragment" data-fragment-index="7">![](/media/temperature-sensor.png){style="max-width:30px;"}![](/media/humidity-sensor.png){style="max-width:30px;"}</span> |
| <span class="fragment" data-fragment-index="4">**SPI**</span>  | <span class="fragment" data-fragment-index="4">4+</span>   | <span class="fragment" data-fragment-index="4">Yes</span>               | <span class="fragment" data-fragment-index="4">High</span>       | <span class="fragment" data-fragment-index="4">Yes (Chip-select lines)</span> | <span class="fragment" data-fragment-index="4">High-speed sensors/memory</span>  <span class="fragment" data-fragment-index="8">![](/media/oled.png){style="max-width:30px;"}</span>                                                                 |
|                                                                |


::: {.fragment data-fragment-index="5"}
<!-- <span class="fragment">![](/media/satellite.png){style="max-width:30px;"}</span> -->
![](/media/satellite.png){style="max-width:30px;"}&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
![](/media/temperature-sensor.png){style="max-width:30px;"}
![](/media/humidity-sensor.png){style="max-width:30px;"}&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
![](/media/oled.png){style="max-width:30px;"}&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
![](/media/led.png){style="max-width:30px;"}
:::
:::

:::
:::




::: {.notes}
There's a lot of things going on at this part of the board with 40 pins.

Point to the legend and say:
	•	Some pins are power
	•	Some are ground
	•	Some are general digital
	•	Some are specialized

While GPIO gives us raw digital control, Raspberry Pi provides three main specialized protocols that organize multiple GPIO lines into different ways of communication with external devices such as ....



UART uses two pins named as TX (transmit) and RX (receive) for asynchronous communication
I²C uses two pins named SDA (serial data line) for data exchange and SCL (serial clock line) which functions as a clock 
SPI uses four pins for fast, synchronous data exchange.

I²C is like a shared bus with addresses.
SPI is like private direct lines with speed.
:::

---


## 4-Wire SPI Model {.smaller}

::: {.columns}

::: {.column width="50%"}
![](/media/spi-one-demo.png){fig-align="center"}

::: aside
<figcaption>
[https://learn.sparkfun.com/tutorials/serial-peripheral-interface-spi/chip-select-cs (CC BY-SA 4.0).](https://learn.sparkfun.com/tutorials/serial-peripheral-interface-spi/chip-select-cs)
</figcaption>
:::
:::

::: {.column width="50%"}
A typical SPI transaction:

1. Pulls **CS low** to select device
2. Generates clock pulses on SCLK 
3. On each clock edge:
	- One bit shifts out on PICO
	- One bit shifts in on POCI
4. Releases **CS high**

:::

::: {.fragment}
Clock ticks <span class="fragment">→ Bits shift</span> <span class="fragment">→ Data sampled</span>
:::

::: {.fragment}
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; ↪ in both directions at the same time
:::


:::

::: {.notes}
So instead of manually toggling GPIO pins, we let the SPI hardware controller manage timing and data shifting for us.

“Let’s look at the four wires. Each one has a very specific job.”
	•	SCLK is generated only by the controller.
	•	MOSI carries bits from controller to peripheral.
	•	MISO carries bits back.
	•	CS enables exactly one peripheral at a time.

“Once CS goes low, the controller starts generating clock pulses.”

Now point at the waveform:
	•	On every clock edge, a bit shifts out.
	•	At the same time, another bit shifts in.
	•	After 8 clock pulses, one byte is exchanged.
    
Synchronous → Clock defines timing
Full-duplex → Send and receive simultaneously
Bit-shift register based → Data moves one bit per clock

Even if we only care about receiving data, we still must send dummy bits to generate clock pulses.

::: 

---
