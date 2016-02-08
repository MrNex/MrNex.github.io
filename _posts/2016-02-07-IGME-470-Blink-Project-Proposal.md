---
layout: post
title: IGME-470 Blink - A  project proposal
---

I am in this one *incredible* class this semester called Physical Computing and Alternative Interfaces. We focus on prototyping computing devices and systems with which humans can interact on a physical level. The first assignment, called Blink, requires you to build something using LEDs.

Having given my project a lot of thought, I decided that I want to make a Simon Says game. The game will contain N LEDs and N + 1 push buttons. When the "Start" button is pushed, a sequence of lights will light up. The user then needs to push the buttons positioned in front of the lights in the order which the corresponding lights lit up. If the user is correct, a single push will be added to the sequence and the next round will begin. If the user is incorrect, the sequence will play again, but all sequences will play faster from now on. Pushing the start button again will restart the game.

While a simple project, it will have a lot of components to manage. One thing I noticed in my projects is that I am not storing pins in variables, I always simply remember what goes where and access it with whatever factory name the data sheet says that pin is called. This project will focus on managing the components of the physical circuit in the code, as I have identified this as an area which I can improve.
