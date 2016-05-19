---
layout: post
title: IGME-470 Final Project
---

My final project in my Physical Computing and Alternative Interfaces course required me to have 2 devices communicating. I tried to do this with my PIC16F1829 chip but found that there were no libraries available for both my chip and the compiler I was using for inter-device communication. However, I looked in the data sheet and found that there was hardware available for I2C communication.

The I2C protocol is a 2 wire single master multi slave asynchronous protocol. One of the wires serves as a clock, while the other transmits data. Using an open-drain design, the two wires go from one device to another, with a pullup resistor in between ensuring the default state of each line is a logical one. The general pipeline for using I2C is as follows:

1)	The master transmits the address of the slave it wishes to communicate with & whether the master wants to send or recieve data.

2)	The called slave responds

3)	The master transmits / recieves the payload of the message

These of course are very general steps which can be expanded:

In step 1, the master first initializes communication by sending a start bit. Once the slaves recieve a start bit, they begin paying attention because the master might ask for their address. After the start bit, each bit of the 7 bit address is sent one at a time. The process for sending each individual bit will be outlined at the end of this section. Finally, after the 7 bit address is sent, a single bit indicating whether the master wishes to recieve or transmit is sent.

After each byte sent, regardless if it is the first byte containing a slave address, or a byte containing part of the message payload, a slave must send an ACK / NACK bit. The ACK / NACK bit is an acknlowedgement bit which is always sent as the 9th bit of a message (excluding start / continue bits) and indicates that everything is going according to plan on the recieving end. On the first byte of communication, containing a start bit and the slave address, all slaves respond with a NACK bit if they are not being called, and an ACK bit if the address belongs to them. While it may seem unintuitive that we could send two messages along the same line at once, this is made possible by the pullup resistors which we will discuss the implications of in a later section.

After each ACK/NACK bit, the master sends a continue / stop bit indicating that the master either still wants to communicate, or is finished. After recieving a NACK bit from a slave, or sending a NACK bit to a slave, a stop bit will almost always follow.

As made evident, there are many different "kinds" of bits which can be sent. Each of these is very precisely defined as follows:

1)	Start Bit - Sent by pulling the Data line low while the Clock line floats high (must follow a stop bit).
2)	Stop Bit - Sent by releasing the Data line while the Clock line floats high.
3)	Continue Bit - The same as a Start bit, but comes after an acknowledgement bit.

These special kinds of control bits are the only times where the Data line may change while the Clock line is high. When sending any other kind of bit the Clock line serves as a mutex of sorts (for the rest of you software people).
The process is the same for ACK bits, NACK bits, and bits belonging to the payload of the message:

1)	First the Clock line is pulled low. This indicates to the recieving party that the transmitting party has begun to load a bit into the Data line.

2)	While the Clock line is low, the Data line is set to the appropriate value of the data (1 for ACK, 0 for NACK).

3)	Once the Data line is set appropriately, the Clock line is released and allowed to float indicating to the reciever that the bit is loaded and ready to be read. At this point, the transmitter waits a minimum amount of time (4 microseconds for standard I2C) to ensure the reciever has time to read the data.

One of the great consequences of this setup is the inherent flow control mechanisms given to the slave devices. Because the default state of the Data and Clock lines are a logical one, if two devices are trying to control a line at the same time, and one device pulls the line low while the other lets it float, the resulting line will be pulled low. One of the devices realizes that the line has a different value than it expected, and realizes another device must be using the line-- and stops transmitting on it. This process is known as Clock Stretching when utilized by slave devices, or Arbitration when exploited by master devices.

After researching and understanding this protocol I tried to use the datasheet for my PIC16F1829 chip to utilize the on-chip hardware to send and recieve data using I2C. Unfortunately this effort was met with failure from every direction. Additionally, there were no examples for using those features of the chip available online or in the starter kit documentation which I own.

Out of luck, I decided things tend to break less if I create them myself. As such, I wrote my own adaptation of the I2C protocol. It is essentially equivalent, except it uses 4 wires, where each device has recieving Data and Clock lines as well as transmitting Data and Clock lines. Other than that, the protocol really remains nearly identical.

After I wrote this lightweight I2C library to use on my chips, I had to come up with a simple application to use it (and test it)! I decided to connect two of my PIC16F1829 chips via my 4 wire adaptation. The master waits for a series of 25 button presses, recording the time intervals between each press. The master then encodes this data, and sends it to the slave. After recieving all of the data, the slave then plays back the sequence by flashing an LED light for each button press.

This simple project caused me quite a bit of strife. Trying to keep track of the state of two separate devices using only 2 bits (the recieving and transmitting clocks) for flow control was very difficult. A lot of mistakes were made on my part dealing with concurrency on such a low level, but I was able to debug and work through all of them.

By the time I actually got this project working I had less than a day to try to make it as presentable as possible. I determined that if no action is taken for too long, the device should do something akin to a "screensaver mode". I decided it would be fun if I created a morse code lookup table, this way actual messages can be transmitted and read as morse code through a blinking LED.. Not very practical- but cute none the less. As of now, after a minute of inactivity, the master device (Emit is his name) says "I love you" in morse code to the slave device (known as sally).

Sally never responds.

I would put the code up for this assignment, but it is 4 files and spans several hundred lines of low level chip specific code. Over the next few days I will find an appropriate format to host the code (Perhaps github), but if I just dumped it here it would be essentially useless to everybody.

Additionally, here is a video of the project working:
[Video Link](https://drive.google.com/file/d/0B2ou4k3PM0y4UGdiV25jQzgwZmc/view?usp=sharing)
