---
layout: post
title: CSCI-711 Reflections
---

I am way to tired- so this one will be kept short. This assignment posed a lot of challenges. I found that the architecture of my game engine was not ready for a parallelized reflection pass. This caused me to need to build a mildly makeshift foundation that will serve as the skeleton for a more robust implementation later on. This turned out to me way more work than I bargained for, but I got it done. I've been awake for a collective 60 hours over the last 3 days, and so I was too tired to revert my scene back to the original Whitter image before I go to take a nap. Here are some images of various stages of progress, aswell as one final image depicting the working reflections:
<figure>
<a href="/images/2016-04-17-Reflections1.png">
<img src="/images/2016-04-17-Reflections1.png">
</a>
<figcaption>Mapping texture coordinates to reflections</figcaption>
</figure>
<figure>
<a href="/images/2016-04-17-Reflections2.png">
<img src="/images/2016-04-17-Reflections2.png">
</a>
<figcaption>Not quite correct</figcaption>
</figure>
<figure>
<a href="/images/2016-04-17-Reflections3.png">
<img src="/images/2016-04-17-Reflections3.png">
</a>
<figcaption>Shaded reflections from sphere on the boxes</figcaption>
</figure>
<figure>
<a href="/images/2016-04-17-Reflections4.png">
<img src="/images/2016-04-17-Reflections4.png">
</a>
<figcaption>Adding reflections from boxes on sphere, and boxes on boxes, but there is Z-fighting between reflections</figcaption>
</figure>
<figure>
<a href="/images/2016-04-17-Reflections5.png">
<img src="/images/2016-04-17-Reflections5.png">
</a>
<figcaption>No more Z-Fighting, but we lost a sphere! I love how you can see the edges of the platform behind the camera in the reflections on the sphere here.</figcaption>
</figure>
<figure>
<a href="/images/2016-04-17-Reflections6.png">
<img src="/images/2016-04-17-Reflections6.png">
</a>
<figcaption>A final image, exhibiting the working reflections-- just not in the whitted scene. Two boxes and one sphere instead of visa versa!</figcaption>
</figure>
