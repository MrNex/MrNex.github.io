---
layout: post
title: CSCI-711 Ray Tracing Framework
---

As the semester continues, so does the development of my Ray Tracer for my Global Illumination Class at Rochester Institute of Technology. As planned, I have been pursuing the development of a real time ray tracer to be implemented into my game engine, NGen. In order to make a real time ray tracer feasible, I will be beginning with a deferred rendering geometry pass, collecting information about the models projected onto the viewing plane in various textures. This information includes the world position, world normal, and diffuse color of the surface in each fragment. I will then use this information to cast the second set of rays for reflection, refraction, and shadows. The casts will incorporate spatial partitioning data structures as well as OpenCL as needed in order to maintain an acceptable frame rate. The results of these casts will then be stored in textures which will be sent to the lighting pass(es) of the deferred rendering pipeline.

This checkpoint contains the Ray Tracing framework which has the ability to cast rays in a given direction from an origin to determine what object they hit, the first point of intersection, the normalized minimum translation vector depicting the direction of smallest translation in order to decouple the intersecting objects, and the magnitude of overlap along this minimum translation vector. This checkpoint also contains the deferred rendering pipeline which I will be using to generate the images and effectively skip the first set of ray casts.

Below are some images either depicting progress or depicting me having fun doing what I do.

<figure>
<a href="/images/2016-02-19-CSCI-Checkpoint2.png"><img src="/images/2016-02-19-CSCI-Checkpoint2.png"></a>
<figcaption>A scene rendered using a deferred rendering pipeline in NGen.</figcaption>
</figure>

<figure>
<a href="/images/2016-02-19-CSCI-Checkpoint2Bonus1.png"><img src="/images/2016-02-19-CSCI-Checkpoint2Bonus1.png"></a>
<figcaption>The same scene rendered from a different angle.</figcaption>
</figure>

<figure>
<a href="/images/2016-02-19-CSCI-Checkpoint2Bonus2.png"><img src="/images/2016-02-19-CSCI-Checkpoint2Bonus2.png"></a>
<figcaption>Suzanne, the Blender test model, joins the scene.</figcaption>
</figure>

<figure>
<a href="/images/2016-02-20-CSCI-Checkpoint2Lights.png"><img src="/images/2016-02-20-CSCI-Checkpoint2Lights.png"></a>
<figcaption>Various point lights join the scene depicting one of the strengths of the deferred rendering pipeline.</figcaption>
</figure>

<figure>
<a href="/images/2016-02-20-CSCI-Checkpoint2Suzanne.png"><img src="/images/2016-02-20-CSCI-Checkpoint2Suzanne.png"></a>
<figcaption>Suzanne threatens the earth in a way too dimly lit scene.</figcaption>
</figure>

<figure>
<a href="/images/2016-02-20-CSCI-Checkpoint2Normals.png"><img src="/images/2016-02-20-CSCI-Checkpoint2Normals.png"></a>
<figcaption>The same scene as above, but colored based on the direction of the normals.</figcaption>
</figure>
