---
layout: post
title: IGME-470 Blink - Project Completed
---

Recently I posted about a project proposal in one of my courses, Physical Computing and Alternative Interfaces. We had to create a project which used LEDs in some fashion. I decided to make a Simon Says game.

It game as no surprise most of my time was spent wiring and re-wiring, the actual programming was done relatively early. One thing that tripped me up a little more than expected was creating my own random number generator, and theres a lot of improvement which could be made. I completed mine by simulating a linear feedback shift register where the seed was based off of the clock value when recieving user input. The trouble is that the clock is set to update once per instruction cycle, which means that while I'm waiting for input the clock is incrementing by a fixed amount each time the loop executes to check the status of a switch. Unfortunately this lead to all seeds being a multiple of a number-- and with only 8 bits to work with this causes same random pattern is generated every single time. This could be fixed in the future by altering the clock register so that it is incremented based off of elapsed time rather than instruction cycles. Alternatively, allowing myself a larger amount of memory with which to randomize the bits while generating a random number could have also solved this problem.

Here is a picture of the completed circuit:
<figure>
<a href="/images/2016-02-21-IGME-470-BlinkComplete.jpg"><img src="/images/2016-02-21-IGME-470-BlinkComplete.jpg"></a>
<figcaption>The completed Simon Says circuit</figcaption>
</figure>


I took a video earlier, but it seems to have become corrupted.