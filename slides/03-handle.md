
## Abstracting the Wires

- Talk to the SPI harware 
  
  <span class="fragment"> → `open()` a file descriptor `/dev/spidevX.Y` </span>

::: {.fragment}
```{.c code-line-numbers="1"}
int fd = open("/dev/spidev0.0", O_RDWR);
if (fd < 0) {
    perror("Failed to open SPI bus");
    return 1;
}
```
:::

::: {.notes}
On Linux systems in Raspberry Pi, almost everything is accessed through the file interface.
To talk to the SPI hardware, we open a file descriptor.
SPI devices appear as files under /dev.

Give me access to the SPI device represented by /dev/spidev0.0, and allow me to both read from it and write to it.

The first number is SPI bus number. It refers to the SPI controller hardware on the Raspberry Pi.
The second number is the chip select number on that bus.

Till now, we basically have established the communication with the sensor that CS0 is connected to.
:::


---

## The Setup

::: {style="font-size: 0.8em;"}
- Use `ioctl()` from `<sys/ioctl.h>` to read or override the current settings for data transfer parameters in `<linux/spi/spidev.h>` 
  - `SPI_IOC_RD_MODE`, `SPI_IOC_WR_MODE`: Choose among constants `SPI_MODE_0`..`SPI_MODE_3`
  - `SPI_IOC_RD_BITS_PER_WORD`, `SPI_IOC_WR_BITS_PER_WORD`
  - `SPI_IOC_RD_MAX_SPEED_HZ`, `SPI_IOC_WR_MAX_SPEED_HZ`
  <!-- - `SPI_IOC_RD_LSB_FIRST`, `SPI_IOC_WR_LSB_FIRST`: The bit justification used to transfer SPI words -->
:::

::: {.fragment}
```{.c code-line-numbers="1,6|2,7|3,8"}
uint8_t mode = SPI_MODE_0;    // CPOL=0, CPHA=0 
uint8_t bits = 8;             
uint32_t speed = 1000000;     // 1 MHz

// These calls 'push' our configuration into the hardware registers
ioctl(fd, SPI_IOC_WR_MODE, &mode);
ioctl(fd, SPI_IOC_WR_BITS_PER_WORD, &bits);
ioctl(fd, SPI_IOC_WR_MAX_SPEED_HZ, &speed);
```
:::

::: {.notes}
Before transferring data, we configure the SPI parameters. Remember the modes we just introduced? This is where we tell the Pi which of those four physical timing patterns to use. Here, we need a special system call named `ioctl` (I/O Control) to 'push' our configuration into the hardware registers. We use it to set the mode, bits per word, and the clock speed. 

Here, we’ll use **Mode 0** at **1MHz**—fast for alerts, but stable for long outdoor cables.

Bits per word:
Typically 8 bits.

Speed:
Maximum clock frequency.
:::

---


## The Transfer


::: {style="font-size: 0.8em;"}
- Two essential helpers from `<linux/spi/spidev.h>`
  - Instantiate structure `spi_ioc_transfer` to let the kernel know how to perform each SPI transaction
  - Full-duplex access using `SPI_IOC_MESSAGE(N)`, where `N` indicates how many `spi_ioc_transfer` structures we are passing as a single message
:::

::: {.fragment}
```{.c code-line-numbers="|1-3|5-9|11|13,14|16-19"}
// The "Wildfire Detection" Request
uint8_t tx_buf[3] = {0x01, 0x80, 0x00}; // The command we want to send
uint8_t rx_buf[3] = {0};               // The buffer to catch the response

struct spi_ioc_transfer tr = {
    .tx_buf = (unsigned long)tx_buf,
    .rx_buf = (unsigned long)rx_buf,
    .len = 3,
};

ioctl(fd, SPI_IOC_MESSAGE(1), &tr);

// Combine the bits into a 10-bit smoke density value
int smoke_level = ((rx_buf[1] & 0x03) << 8) + rx_buf[2];

if (smoke_level > 500) {
    printf("🔥 Smoke levels rising... "
           "hopefully it's just someone's BBQ near UBCO.\n");
}
```
:::

::: {.notes}
Now we move from configuration to actual transfer.

We describe one transaction using spi_ioc_transfer.

This structure tells the kernel:
- where the transmit buffer is,
- where to store received data,
- how many bytes

Then we execute transaction using SPI_IOC_MESSAGE. This single line triggers the hardware controller to generate clock pulses and exchange bytes.

Now we apply that mechanism to a real device.

Look at this struct. It has a transmit buffer and a receive buffer. Remember what we saw in the drawing? SPI is Full-Duplex. For every bit we 'push' out on PICO, we 'pull' one in on POCI. You are always sending and receiving at the same time.

The SPI_IOC_MESSAGE(1) call does everything in one atomic burst: It drops the CS line, fires the Clock 24 times (3 \text{ bytes} \times 8 \text{ bits}), and captures the incoming bits. When it’s done, it lifts the CS line and hands the data back to your C program.

Right now, we’re just taking a snapshot.
To turn this into a monitoring system, we wrap this in a loop and sample continuously.

---

That's it. Within 50 lines of C code, you've built the heart of a wildfire early-warning system. You aren't just writing code; you are engineering solutions for the Okanagan.

You may have noticed some 'magic' numbers in our command: `0x01`, `0x80`. Why send 3 bytes? Why ignore the very first byte that comes back? Why the result value is in 10 bits? I’ll leave that as a challenge for you to solve tonight. Use our **Full-Duplex** timing diagram and the sensor's **Technical Manual** to figure out why.
:::

