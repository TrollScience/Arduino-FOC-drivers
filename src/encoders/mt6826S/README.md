# MT6826S SimpleFOC driver This is a first test Version based on a modification of the MT6835 Driver. NOT fully Tested. Credits for the driver this is Based on go to the SimpleFOC Team. 

Description following this is also based on the MT6835 Description:


# MT6826S SimpleFOC driver

Driver for the MagnTek MT6826S precision magnetic rotary encoder.

This sensor features support for up to 15 bit resolution (!) and speeds up to 120,000RPM. While its full precision requires calibration using an external calibration system, it is impressively precise even in uncalibrated state, and it offers an internal self-calibration mode whose precision is somewhere between the other two.

It has ABZ, UVW and SPI interfaces, and this driver is for its SPI inteface. You can use its ABZ interface with our Encoder classes, but due to the high resolution the interrupt-based Encoder might cause high MCU load. You can use its UVW interface with our Hall Sensor classes.

# Hardware setup

Connect the sensor to an SPI bus on your MCU. Pay attention to the voltage levels needed by your PCBs. The nCS line should have a pull-up resistor.

# Software setup

Usage example:

```c++
#include <Arduino.h>

#include "SimpleFOC.h"
#include "SimpleFOCDrivers.h"

#include "encoders/mt6826S/MagneticSensormt6826S.h"

#define SENSOR_nCS PB6

SPISettings mymt6826SSPISettings(1000000, MT6835_BITORDER, SPI_MODE3);
MagneticSensormt6826S sensor = MagneticSensormt6826S(SENSOR_nCS, mymt6826SSPISettings);

long ts;

void setup() {
    sensor.init();
    ts = millis();
}

void loop() {
    sensor.update();
    long now = millis();
    if (now - ts > 1000) {
        ts = now;
        SimpleFOCDebug::print("A: ");
        SimpleFOCDebug::print(sensor.getAngle());
        SimpleFOCDebug::print(" V: ");
        SimpleFOCDebug::println(sensor.getVelocity());
    }
    delay(10);
}

```

Set the zero position:

```c++
uint16_t pos = sensor.getZeroPosition();    // current value
sensor.setZeroFromCurrentPosition();        // set zero to current position
```

Set the ABZ resolution (needed if you want to use ABZ as the default is 1):

```c++
sensor.setABZResolution(2048);
```

