---
layout: post
title: CSCI-711 Setting the Scene
---

As I described previously, as a part of my Global Illumination course I will be building a ray tracer. The goal is to replicate the following image:

<figure>
<a href="/images/CSCI-711-Goal.jpg"><img src="/images/CSCI-711-Goal.jpg"></a>
<figcaption>The scene my ray tracer should be able to replicate.</figcaption>
</figure>

The first step to this assignment is to build the scene and log all of the positions, scales, and orientations of all objects (visible and invisible) in the scene. Below you will see my current rendering of the scene:

<figure>
<a href="/images/CSCI-711-Assignment1.png"><img src="/images/CSCI-711-Assignment1.png"></a>
<figcaption>The target scene very basically rendered in my NGen.</figcaption>
</figure>

The object transformations are as follows:

##Every object in the scene
- Orientation: Identity Matrix, every object is facing down the negative Z axis with the positive X on the right, and positive Y above.

##Ground
- Position:	[-15, -10, 0]
- Scale:	[20, 60, 1]

##Large Sphere
- Position:	[0, 0, -5]
- Scale:	[2, 2, 2]

##Small Sphere
- Position:	[-3, -2, -6.8]
- Scale:	[1.8, 1.8, 1.8]

##Camera
- Position:	[0, 0, 0]

It should be noted that although my camera is at the origin, the goal of my project will include an interactive camera to view the physical simulation.

##Directional Light
- Color:		White
- Direction:		[0, -1, 0]
- Ambient Intensity:	0.2
- Diffuse Intensity:	1.0

Aswell as the objects transformations, one of my goals is to have the ray tracer run in real time while my NGen is still able to process user input, object state, physics, and collision management. If the ray tracer significantly slows down my NGen it could severely impact the integrity of the other systems (notably the physics and collision management). As a direct result, I decided it would be necessary to then log the physical properties of each object and record the desired behavior of the system to look back on as a control group.

The physical properties of the objects are as follows (Note: If a field is unspecified it is either 0 or the identity):

##Ground
- Inverse Mass:				0	//Thanks Ph.D. David Schwartz!
- coefficients of Friction (both):	0.5
- rolling resistance:			0
- constraints: freeze linear, freeze angular

##Large Sphere:
- Inverse Mass:				.5
- coefficients of Friction (both):	.1
- coefficient of restitution:		0.9
- rolling resistance:			0.1

##Small Sphere:
- Inverse Mass:				0.555555
- coefficients of friction (both):	0.08
- coefficient of restitution:		0.8
- rolling resistance:			0.1

##Gravity
[0, -9.81, 0]


