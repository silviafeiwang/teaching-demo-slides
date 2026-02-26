
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

