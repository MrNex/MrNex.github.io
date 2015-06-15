---
layout: post
title: Torque Due To Friction
---

The bitter sweet part about having a great job that you love is that it leaves little time for my great personal projects that I love, among other things I need to do. Namely, I wish I had more time to work on [NGen](www.github.com/MrNex/NGen), my 3D simulation engine. However, in what little free time I do have (that I have the willpower to keep working through) I have been working hard. Recently I reached a milestone (and closed a github issue) for removing all warnings from the NGen when compiling with gcc using the -Wall flag! Coming from a visual studio oriented code base this left my 16 thousand lines of code riddled with hundreds of errors, but finally they have all been squashed (and a few bugs were found in the process)!

Among those bugs I found a prevelant one in my computation of torque due to friction in the physics engine. To be frank, it didn't at all work and coincidentally just *appeared* to be working for the given simulation. There seems to be a severe lack of understandable examples of computing torque due to friction in a physics engine, so I wrote my own algorithm! My algorithm is heavily based off of the [Coloumb friction model](https://en.wikipedia.org/wiki/Collision_response#Impulse-Based_Friction_Model). For anybody interested I will be posting a rundown of it here within the next week when I find the time.

Until then, be sure to check out the NGen, and keep on coding!
