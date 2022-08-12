---
layout: post
title:  "Docker Getting Started Tutorial"
category: software
---

![Docker Icon](/assets/docker-icon.jpg)

We ran through the [Docker getting started tutorial](https://docs.docker.com/get-started/) as an appetizer for our next book club book, [Docker Deep Dive](https://www.amazon.com/Docker-Deep-Dive-Nigel-Poulton/dp/1521822808).

It took about 3 hours to walk through and get everything to run, though give yourself an extra couple of hours if you plan to poke at some of the supporting links/videos.

The getting started guide is at the above link or you can run the guide locally from the getting-started docker image.

I found this tutorial to be well worth the time. You run a toy javascript application, modify it a bit, redeploy it (the slow way, then the much faster way with a bind mount), share it on the docker hub repository, use a volume for a persistent db, then use a separate image for a database, and finally manage the 2-image app with docker-compose.

What I learned:
* Got a lot of "touches" using the docker command line and Docker Desktop app.
* Use a bind mount to mount a local folder rather than re-building the image each time after a change (which is maddeningly slow).
* Always scan for vulnerabilities (they recommend using Snyk). For me on the node app it found 254 vulnerabilities, 5 of which were critical in the version of imagemagick in the container.
* Use "docker image history ..." on a an image to see it's "layer history" (which is basically the list of commands in the Dockerfile).
* The order of commands in the Dockerfile are important! Each is cached and will only re-run if needed! You can speed up builds quickly with this.

There are a few links you can follow from the tutorial for more information:
* [From GOTO 2018: Containers From Scratch by Liz Rice](https://www.youtube.com/watch?v=8fi7uSYlOdc). 35 min video. Worth watching. "Don't run a fork bomb on a machine unless you know it's constrained by a Cgroup." :-)
* [Why you shouldn't use ENV variables for secret data](https://blog.diogomonica.com//2017/03/27/why-you-shouldnt-use-env-variables-for-secret-data/). 5 min read. Worthwhile.
* [Demystifying Containers](https://medium.com/@saschagrunert/demystifying-containers-part-i-kernel-space-2c53d6979504). Didn't read. I figure I'll get this from our book club soon enough.
