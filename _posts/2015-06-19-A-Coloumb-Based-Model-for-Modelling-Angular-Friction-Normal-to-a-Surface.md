---
layout: post
title: A Coulomb Based Model for Simulating Angular Friction Normal to a Surface
---

So I said I came up with an algorithm for computing angular friction, and finally I tested it. At first I was unhappy with the results for reasons I can explain another time- but after a lot of thought (and reading a lot of papers about the physics of a top) realized that it is actually a correct & reliable way of computing discretized angular friction for motion that is about an axis normal to the surface.

Without further ado, the algorithm:

1. Determine the relative angular velocity between two objects which have collided (or are in a contact state).
   - By this I mean Obj2.AngularVelocity - Obj1.AngularVelocity
   - This would compute the relative angular velocity of Obj2 from the center of mass of Obj1.
2. Determine the normal force between the two objects to the surface.
   - If two objects collide they must each recieve equal and opposite reactions according to Newton's laws of motion.
   - You can compute the magnitude of this normal force by taking the collision impulse between the two objects and performing the absolute value of the dot product of this impulse with the surface normal on the surface of collision/contact.
   - You only need the magnitude of the normal force because it's direction is clear already (The direction of the surface normal).
3. Use the magnitude of this normal force to compute the static and dynamic magnitudes of friction (Much like the Coloumb model of friction).
   - These magnitudes are just going to be the respective coefficient of friction multiplied by the magnitude of the normal force.
4. Determine the magnitude of the relative angular velocity (1) in the direction of the colliding surface normal.
   - Again, just a simple dot product between the two vectors.
5. Determine the relative angular momentum of each object.
   - L1 = I1 * (relativeAngularVelocityPerp * surfaceNormal)
     * Where L1 is the angular momentum of object 1
     * I1 is the inertia tensor of Object 1
     * relativeAngularVelocityPerp is from (4)
     * And the surface normal is the normal vector of the colliding surface
     * Do note that in the case of point - point / edge - edge collisions and etc. I use the Minimum Translation Vector calculated during the collision resolution step.
  - And don't forget to calculate L2 aswell!
6. Determine & apply the frictional torque on each object.
   1. If the magnitude of L# (5) does not overcome the value of the staticMagnitude (3) simply apply +- L# opposing the direction of intended motion.
     * By +- I mean "Plus or Minus", determine which direction you must apply it to inhibit the angular motion. So in other words you may or may not need to scale L# by -1.
   2. If the magnitude of L# (5) does overcome the value of the staticMagnitude (3) simply apply a vector in the direction of L#, but scaled to the magnitude of +- dynamicMagnitude (3) opposing the direction of intending motion.
     * Again, by +- I mean "Plus or Minus", determine which direction you must apply it to inhibit the angular motion. You may or may not need to scale it by -1.
7. ???
8. Profit.

On a more serious note, I really hope somebody might find this helpful. It took me a few tries to get this right. It seems like there exists an abundance of sources for the Coulomb Model of Friction but nothing talks about the angular case explicitly. I give all credit to Coulomb and his model. This work was completely derived from that.

Happy Hacking,
Mr. Nex
