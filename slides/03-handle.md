
## Estabilish Communication

- Access SPI device file via `/dev/spidevX.Y` from `<linux/spi/spidev.h>`
  ```{.bash code-line-numbers="1|2"}
    $ ls /dev/*spi*
    /dev/spidev0.0  /dev/spidev0.1
  ```
- Open file via `open()` from `<fcntl.h>`

::: {.fragment}
```{.c code-line-numbers="|5"}
#include <stdio.h>
#include <fcntl.h>
#include <unistd.h>

int fd = open("/dev/spidev0.0", O_RDWR);
if (fd < 0) {
    perror("open");
    return 1;
}
```
:::

::: {.notes}
On Linux systems like Raspberry Pi
SPI devices appear as files under /dev.

For example: /dev/spidev0.0

The first number is the bus.
The second is the chip select.

We open it like a file.

From the user-space perspective, SPI communication begins by opening this device file.

This header from linux provides an interface to define the SPI communication structure.
:::


---

## Device-Level Configuration

- Use `ioctl()` from `<sys/ioctl.h>` to read or override the current settings for data transfer parameters
  - `SPI_IOC_RD_MODE`, `SPI_IOC_WR_MODE`: Choose among constants `SPI_MODE_0`..`SPI_MODE_3`
  - `SPI_IOC_RD_BITS_PER_WORD`, `SPI_IOC_WR_BITS_PER_WORD`
  - `SPI_IOC_RD_MAX_SPEED_HZ`, `SPI_IOC_WR_MAX_SPEED_HZ`
  <!-- - `SPI_IOC_RD_LSB_FIRST`, `SPI_IOC_WR_LSB_FIRST`: The bit justification used to transfer SPI words -->


## Device-Level Configuration (Cont.) {auto-animate=true}

```{.c code-line-numbers="|5|8"}
#include <linux/spi/spidev.h>
#include <sys/ioctl.h>
#include <stdint.h>

uint8_t mode = SPI_MODE_0;   // CPOL=0, CPHA=0

/* Set SPI transfer mode*/
if (ioctl(fd, SPI_IOC_WR_MODE, &mode) == -1)
    perror("SPI_IOC_WR_MODE");
```

## Device-Level Configuration (Cont.) {auto-animate=true}

```{.c code-line-numbers="6,13"}
#include <linux/spi/spidev.h>
#include <sys/ioctl.h>
#include <stdint.h>

uint8_t mode = SPI_MODE_0;   // CPOL=0, CPHA=0
uint8_t bits = 8;

/* Set SPI transfer mode*/
if (ioctl(fd, SPI_IOC_WR_MODE, &mode) == -1)
    perror("SPI_IOC_WR_MODE");

/* Set bits per word*/
if (ioctl(fd, SPI_IOC_WR_BITS_PER_WORD, &bits) == -1)
    perror("SPI_IOC_WR_BITS_PER_WORD");
```

## Device-Level Configuration (Cont.) {auto-animate=true}

```c
#include <linux/spi/spidev.h>
#include <sys/ioctl.h>
#include <stdint.h>

uint8_t mode = SPI_MODE_0;   // CPOL=0, CPHA=0
uint8_t bits = 8;
uint32_t speed = 1000000;    // 1 MHz

/* Set SPI transfer mode*/
if (ioctl(fd, SPI_IOC_WR_MODE, &mode) == -1)
    perror("SPI_IOC_WR_MODE");

/* Set bits per word*/
if (ioctl(fd, SPI_IOC_WR_BITS_PER_WORD, &bits) == -1)
    perror("SPI_IOC_WR_BITS_PER_WORD");

/* Set SPI speed*/
if (ioctl(fd, SPI_IOC_WR_MAX_SPEED_HZ, &speed) == -1)
    perror("SPI_IOC_WR_MAX_SPEED_HZ");
```

::: {.notes}
Before transferring data,
we must configure SPI parameters.

Mode:
Defines clock polarity and phase.
These must match the device datasheet.

Bits per word:
Typically 8 bits.

Speed:
Maximum clock frequency.

These settings define how electrical signaling occurs.
If mismatched, communication fails silently.
:::

---


## Transfer Bytes {.smaller}

- Instantiate structure `spi_ioc_transfer` from `<linux/spi/spidev.h>` to let the kernel know how to perform the SPI transaction with per-transfer setting


- Full-duplex access using `SPI_IOC_MESSAGE(N)` from `<linux/spi/spidev.h>`
  - `N` indicates how many `spi_ioc_transfer` structures we are passing


::: {.fragment}
```{.c code-line-numbers="|4-11|13"}
static int spi_transfer(int fd, uint8_t *tx, uint8_t *rx, size_t n,
                        uint32_t speed, uint8_t bits) {

  struct spi_ioc_transfer tr = {
    .tx_buf = (unsigned long)tx,
    .rx_buf = (unsigned long)rx,
    .len = n,
    .delay_usecs = 0,
    .speed_hz = speed,
    .bits_per_word = bits,
  };

  return ioctl(fd, SPI_IOC_MESSAGE(1), &tr);
}
```
:::

::: {.notes}
Now we move from configuration to actual transfer.

We describe one transaction using spi_ioc_transfer.

This structure tells the kernel:
- where the transmit buffer is,
- where to store received data,
- how many bytes,
- what speed,
- how many bits per word.

Then we execute it with ioctl using SPI_IOC_MESSAGE.

This single line triggers the hardware controller to generate clock pulses and exchange bytes.
:::

---

## Example: Read MCP3008 Channel 0

::: {.fragment}
> MCP3008 ADC requires a 3-byte SPI transaction
:::

- Build command bytes in `tx[]`
- Transfer 3 bytes
- Parse `rx[]` into a 10-bit ADC value

::: {.fragment}
```{.c code-line-numbers="|1-2|4|7"}
uint8_t tx[3] = {0x01, 0x80, 0x00};  // start, (SGL=1,ch0), dummy
uint8_t rx[3] = {0};

if (spi_transfer(fd, tx, rx, 3, speed, bits) < 1)
  perror("spi_transfer");

int value = ((rx[1] & 0x03) << 8) | rx[2];
printf("ADC = %d\n", value);
```
:::

::: {.notes}
Now we apply that mechanism to a real device.

The MCP3008 ADC requires a 3-byte SPI transaction.

First byte:
Start bit.

Second byte:
Configuration — single-ended mode and channel selection.

Third byte:
Dummy byte to generate clock pulses for the result.

After transfer,
the 10-bit ADC value is split across two bytes.

We mask and shift the bits
to reconstruct the numeric value.

This is a concrete example of:
device protocol layered on top of SPI.
:::


---

## This is a closing slide