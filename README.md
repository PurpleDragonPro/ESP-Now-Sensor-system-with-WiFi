# ESP-Now Sensor system with WiFi
Low power battery operated Sensors based on ESP8266 or ESP32, sending sensor data using ESP-Now to an ESP Gateway which also is using WiFi and connected to the Internet (at the same time).

Typical usage is battery and/or solar powered weather stations with a Blynk app for visual display. Using ESP-Now and low deep sleep current boards allows for 1 year battery operation. Adding even a very small solar panel enables continuous operation. All thanks to the fast and low power ESP-Now protocol.

## Soon to be added:
- Code for sensor (sending on ESP-NOW)
- Code for gateway (receiving on ESP-NOW and sending further via WiFi to the Internet, like Blynk, Thingspeak and MQTT.)
- System overview sketch
- Battery operation sketches (for sensors)

## Sensor summary:

Sensor code is optimized for as low power usage as possible. Sensors operate in Deep Sleep mode and enables the WiFi module as late as possible just before sending the ESP-Now message. WiFi module is active less than 100 ms, more likely 50-70ms.

My ESP-Now messages are "unnecessary" long as it would only require a few bytes to send sensor data. However, I have standardized on a more general and longer format as the extra time to transmit has low impact on energy consumption and battery lifetime.

The energy consumption can be divided into 3 classes. 1. Deep Sleep period, 2. Wake time reading sensor, etc with WiFi off, 3. Sending data with WiFi on. With a deep sleep period of 5 mins between sensor readings and transmissions, it is the Deep Sleep period which is the major energy consumer for most standar ESP boards. Why it is very important to choose a board, or module, with as low deep sleep current as possible.
D1 Mini Pro V2.0 is the best standard board I have tested. 

(I have a FireBeetle ESP8266 IoT under test and evaluation which looks promising. Building your own sensor with a naked ESP-module without USB drivers and LDO would lower the deep sleep current potential. It would also be possible to disable sensors by SW or HW during sleep, but I have only tested this very little and with no resulting battery lifetime improvements.)

Best lifetime performance has been observed with LOLIN SHT30 temp and humid sensor, using a modified library driver. With DS18B20 temp sensor very close by. Then BME280, which consumes a bit more current which results in a bit shorter lifetime. DHT11, -21 and -22 all consumes more power (shorter battery lifetime) and also produce much more false readings and/or stops reading.

![](https://github.com/jonasbystrom/ESP-Now-Sensor-system-with-WiFi/blob/main/img/esp-now-temp-sensor-with-solar-panel.png)


## Life time:

Typical life time performance of a Sensor (LOLIN D1 Mini Pro V2.0.0 and a SHT30 temp/humidity sensor) is 6 months using a 1200mAh LiPo battery and about 12 months for a Li-Ion 2200mAh.
Using a small 80x55mm 5v solar panel and a TP4056 charger with a 1200 or 2200 mAh battery is well enough for "continuous operation".

## Gateway summary:

A gateway receives the sensor data and can send further to any service on the Internet such as Blynk, Thingspeak or MQTT. Gateways are based on ESP8266 or ESP32 with small code changes in ESP-Now and WifI API's. The Gateway should have both ESP-Now and WiFi actived at same time and the ESP's can only manage this if they are on the same channel. And it does only seem work to for channel 1, due to some implementation limitation in the ESP's. This means the WiFi Router must be set to channel 1 on the 2.4GHz band. It can not operate on "auto channel", it has to be set to channel 1 fixed.

By specification a Gateway can manage max 20 sending sensors on ESP-Now. It is fully possible to use several Gateways in parallel on the same channel if there is a need for more than 20 sensors. I have been running with 3 gateways at the same time without any collisions or problems.

## System summary:

A system comprise of up to 20 sensors and 1 gateway. Sensors send to the MAC-adress of the gateway. It is possible, and preferred, to use a software defined MAC-address in the gateway and not the default hardware MAC address. As this allows for an exchange of the gateway hardware without any problems. The ESP's support this.
Each system should have it's own MAC address to avoid collisions with other systems.
It is said ESP-Now has "3 times longer access range" than WiFi. I have never tested this but I have noted long enough access range for my sensors in my installations. The communication stability is very good. I have never experienced any communication losses in all normal cases. I havent tested this very much but i typically get 99 or 100 sucessful transmissions out of 100 tries.  
