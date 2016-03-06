---
layout: post
title: CSCI-711 Ray Tracing Framework
---

Recently in my Global Illumination course we were asked to implement basic phong shading into our ray tracer, aswell as complete the first shadow ray pass. Things went smoothly as far as implementing phong, however in performing the shadow pass I finally lost my real time framerate. With the shadow pass enabled my NGen only renders at 1-2 FPS for an 800x600 image. However, in light of this I have began learning OpenCL in preparations to implement the ray tracing algorithms using that. I will probably not be implementing the OpenCL calculations by before the next checkpoint. Instead I plan on working over the spring break to try and implement the ray casts in OpenCL.

But without further delay, here is the scene with shadows rendered in all of it's glory:
<figure>
<a href="/images/2016-03-05-Checkpoint3.png">
<img src="/images=/2016-03-05-Checkpoint3.png">
</a>
<figcaption>A scene rendered with shadows depicting two spheres made of different materials</figcaption>
</figure>
