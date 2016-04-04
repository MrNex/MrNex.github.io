---
layout: post
title: IGME 470 - Musical Gloves
---

Last week marked the end of Project 2 in my Alternative Interfaces and Physical Computing course. For our second project we had to create a wearable interactive experience for users. I decided that I wanted to try to create an instrument!

My thought was that I can connect 5 force sensative resistors to a glove, reading an analog input from each one. Then, representing the 5 fingers as a 5 digit binary number, N. N, now represents the number of halfsteps above the root note the instrument is tuned (or programmed) to using an approximation of the traditional 12 intervals used in western music theory. I use an equal temperment system, a compromise of a just temperment system where each of the 12 tones within one octave can be found by multiplying the closest member of the same harmonic series with the ratio of two whole numbers. The difference between an equal temperment system and a just temperment system is that a just temperment system will vary the interval constants with the root frequency the instrument is tuned to. Equal temperment, on the other hand, approximates these constants as 12 uniform slices of the square root of 2- this way they translate between different root frequencies better. This allows you to play in any key you desire as well or as poorly as you can manage. Unfortunately because of the imprecision of choosing a root frequency (a problem I am still trying to deal with), this means it will be very very difficult to play along with any other instrument or song (as the key will probably never even be close to matching).

The interval constants I chose to use can be found via the following link: [phy.mtu.edu/~suits/scales.html].

For this project I utilised the PIC16F1829, which has been really growing on me throughout this year. The controller has a boatload of pins which support ADC, aswell as PWM capabilities. I strive to try and maintain a 50% duty cycle ratio for a nice full sound, and found that I can approximately generate notes from ~290 Hz to ~4200 Hz. This range can be dynamically adjusted by intelligently setting the prescaler of the timer I am using for my PWM output. I also include a potentiometer for adjusting volume (Unfortunately the neighbors did not seem to like the lulling cry of square waves through my thin Riverknoll walls- the small price to pay for science).

At the following link you can find a video of the glove in action: NOT HERE YET

And below you can find the code for the project (Released under QPL if anybody wants to use it):
ALSO NOT HERE YET
