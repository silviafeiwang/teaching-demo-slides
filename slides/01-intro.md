## Title

::: {.notes}
Title similar to: Bridging the Digital and Physical: Exploring how microcontrollers connect computation with real-world devices

a hook! might be a quote?
:::

---

## What is a Microcontroller?

- Small, cost-effective computer on a chip
- Components (image)

- Applications: robotics, home automatic, automotive, consumer electronics

---

## General Purpose Input/Output (GPIO) Basics

- GPIO pins allow for:
  - Input: read signals to sense the env
  - Ouput: control devices to change the env 
- GPIO direction and values are set using registers in the microcontroller

(image) how it looks like


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


## Microcontrollers and Its Applications

---

## Why Do We Care About External Devices?

- Sensors are everywhere
- Embedded systems must interact with the physical world
- Software alone cannot observe analog signals

. . .

Hardware interfaces bridge programs and reality

::: {.notes}
Speaker notes go here.
:::
