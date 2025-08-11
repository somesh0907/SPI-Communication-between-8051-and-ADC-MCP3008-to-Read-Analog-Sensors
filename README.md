# SPI Communication with MCP3008 ADC on 8051 Microcontroller

## Project Overview

This project demonstrates how to interface the 8051 microcontroller with the MCP3008 10-bit analog-to-digital converter (ADC) using the SPI protocol. The 8051 acts as SPI master to read analog sensor data (such as from a potentiometer or temperature sensor) through the ADC and obtain digital values for further processing.

## Hardware Setup

- **Microcontroller:** 8051 (e.g., AT89C51)  
- **ADC:** MCP3008 10-bit SPI ADC  
- **Analog Input:** Sensor connected to MCP3008 analog input channel (e.g., CH0)  
- **SPI Pins Connection:**  
  - MOSI (Master Out Slave In) —> P1.0  
  - MISO (Master In Slave Out) —> P1.1  
  - SCLK (Serial Clock) —> P1.2  
  - CS (Chip Select) —> P1.3  
- **Power supply** and common ground connections

## How It Works

1. The 8051 initiates communication by pulling the CS (chip select) low.
2. It sends the start bit and channel selection bits over MOSI, clocked by SCLK.
3. The MCP3008 converts the selected analog channel input to a 10-bit digital value.
4. The 8051 reads the digital data bits back from MCP3008 over MISO.
5. The CS line is set high to end communication.
6. The digital ADC value can be used for applications like sensor reading, monitoring, or control.

## Code Explanation


#include <reg51.h>

#define MOSI P1_0
#define MISO P1_1
#define SCLK P1_2
#define CS   P1_3

unsigned int SPI_ReadADC(unsigned char channel);

void Delay();

void main() {
    unsigned int adc_value;

    CS = 1;     // Ensure CS is high initially
    SCLK = 0;   // Clock low

    while(1) {
        adc_value = SPI_ReadADC(0);  // Read analog input from channel 0

        // adc_value now holds the 10-bit ADC result (0-1023)
        // Add code here to use adc_value, e.g., display or control

        Delay();
    }
}

unsigned int SPI_ReadADC(unsigned char channel) {
    unsigned char i;
    unsigned int adc_val = 0;

    CS = 0;  // Select the ADC

    // Construct command byte: Start bit (1), single-ended mode (1), channel (3 bits)
    unsigned char command = 0x18 | (channel & 0x07);

    // Send command bits (8 bits)
    for(i = 7; i != 0xFF; i--) {
        MOSI = (command >> i) & 0x01;
        SCLK = 1;
        SCLK = 0;
    }

    // Read 12 bits from ADC (first null bit + 10-bit data + one extra bit)
    for(i = 11; i != 0xFF; i--) {
        SCLK = 1;
        adc_val <<= 1;
        if(MISO) adc_val |= 0x01;
        SCLK = 0;
    }

    CS = 1;  // Deselect the ADC

    return adc_val >> 1;  // Right-align to 10 bits
}

void Delay() {
    unsigned int i, j;
    for(i=0; i<500; i++)
        for(j=0; j<1275; j++);
}


## How to Use

1. Connect MCP3008 ADC SPI pins to the 8051 microcontroller pins as specified above.
2. Connect your analog sensor or potentiometer to one of the MCP3008 input channels (e.g., CH0).
3. Compile the code using Keil µVision IDE and load the HEX file onto your 8051 development board or use simulation software like Proteus.
4. Power the system and monitor the ADC values (you can extend the code to display or send via UART).
5. Change the analog input and observe the corresponding digital output value.

## Extending the Project

* Interface multiple MCP3008 channels to read several analog sensors.
* Add UART or LCD output to display the ADC values.
* Implement SPI interrupt-driven communication for efficiency.
* Use the ADC values to control actuators or motors based on sensor input.

## Prerequisites

* Keil µVision IDE or equivalent 8051 compiler.
* Proteus Design Suite or real hardware setup.
* Basic understanding of SPI protocol and 8051 microcontroller programming.

---

## Contact and Contributions

For issues, suggestions, or improvements, please open an issue or submit a pull request.

---

**Thank you for exploring the SPI Communication with MCP3008 ADC project!**

```


