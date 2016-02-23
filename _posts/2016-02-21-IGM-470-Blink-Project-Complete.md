---
layout: post
title: IGME-470 Blink - Project Completed
---

Recently I posted about a project proposal in one of my courses, Physical Computing and Alternative Interfaces. We had to create a project which used LEDs in some fashion. I decided to make a Simon Says game.

It game as no surprise most of my time was spent wiring and re-wiring, the actual programming was done relatively early. One thing that tripped me up a little more than expected was creating my own random number generator, and theres a lot of improvement which could be made. I completed mine by simulating a linear feedback shift register where the seed was based off of a timer value when recieving user input. The trouble is that the timer is set to update once per instruction cycle, which means that while I'm waiting for input the clock is incrementing by a fixed amount each time the loop executes to check the status of a switch. Unfortunately this lead to all seeds being a multiple of a number-- and with only 8 bits & 3 possible outcomes to work with this causes extremely similar "random" patterns to be generated every single time. This could be fixed in the future by altering the clock register so that it is incremented based off of elapsed time rather than instruction cycles. Alternatively, allowing myself a larger amount of memory with which to randomize the bits while generating a random number could have also solved this problem. It is possible that just having more outcomes rather than only 3 possible LEDs could have given the perception of it being more random than it is.

Here is a picture of the completed circuit:
<figure>
<a href="/images/2016-02-21-IGME-470-BlinkComplete.jpg"><img src="/images/2016-02-21-IGME-470-BlinkComplete.jpg"></a>
<figcaption>The completed Simon Says circuit</figcaption>
</figure>


I took a video earlier, but it seems to have become corrupted.

The behavior of the game is as follows:

When switched on the game begins, first the user will be shown a pattern of 5 lights. Each light has a button in front of it that the user must press in the order the sequence was presented. If the user finishes the sequence correctly, the lights will flash from right to left 5 times in a quick succession. At this point the game begins again adding one random element to the existing sequence. If, however, the user enters the sequence incorrectly, upon the first incorrect key being pressed all lights will light up simultaneously for 3 long blinks- inturrupting the user. The sequence will then play again (but almost imperceptibly faster as a penalty) without adding an element.
