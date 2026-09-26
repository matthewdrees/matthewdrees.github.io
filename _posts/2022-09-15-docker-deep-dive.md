---
layout: post
title:  "Book notes: Docker Deep Dive"
category: book
---

![Book cover](/assets/docker-deep-dive.jpg)

By Nigel Poulton.

Book club time. We use docker but would like to know more about it. After a 15 minute scramble on the Internet we came up with Docker Deep Dive as the best available book. We paired this book with the Docker Getting Started Tutorial. [(My notes here)]({% post_url 2022-08-08-docker-getting-started %}). I recommend both.

I spent about 8 hours reading the book and doing various deep dives. We did 5 book club meetings, about 2 chapters per meeting. We skipped a few of the later chapters around stacks and didn't try to get all the code examples to run for multi-container apps (can follow up later on those if we ever need a swarm for something). This book struck me as the right balance of breadth vs depth for someone starting out learning about Docker. There were usually a few nuggets of real world wisdom in each chapter. And jokes. I like that. Keeps it lively.

I feel quite a bit more comfortable with Docker now. This is an important technology to be familiar with.

## 1. Containers from 30k feet

A bit of history about VMware and the start of Docker. Docker containers use the host OS... Linux containers must run on Linux (on Windows they run on WSL, on Macs they run on a "lightweight Linux VM"), Windows containers must run on Windows (p8-9).

## 2. Docker

Docker came from "Moby" open source project. The name came from the British phrase meaning "dock worker" (p11).

Architecture: (p12)
* Orchestration: Swarm
* Engine/daemon: Remote API, Networking, etc.
* Runtime: containerd, runc

Open Container Initiative (OCI) publishes [image-spec](https://github.com/opencontainers/image-spec) and [runtime-spec](https://github.com/opencontainers/runtime-spec) (p13). (I glanced but didn't find these to be scintillating reading.)

## 3. Installing Docker

_Why_ put this in a book?

## 4. Big Picture

Steps for getting you running with a container. All of this and more is covered in the getting started guide. [(My notes here)]({% post_url 2022-08-08-docker-getting-started %}).

## 5. The Docker Engine

A high level discussion of the runtime part of the docker engine and runtime.

runc - Small cli wrapper for the libcontainer library, both are written in Go. It's the reference OCI runtime implementation.

containerd - cli that manages container lifecycle, push/pull images, etc. I think of this as all the docker features that clients (i.e. Kubernetes) want that otherwise don't want to use the rest of the docker suite/engine.

Page 38 has a great diagram and explanation for what happens when you run a docker cli command to run a container: docker cli -> docker daemon (gRPC API) -> containerd (CRUD-style over gRPC) -> runc (actually creates/runs the container). A small "shim layer" controls stdin/stdout, return codes, etc.

Containers run independently of the docker daemon, so docker can be upgraded without having to restart the containers, which is a "huge benefit" (p39).

## 6. Images

"A Docker image is a unit of packaging that contains everything required for an application to run." (p51)

DockerHub - images deleted after 6 months of inactivity. Canonical offers 10 years of support for Ubuntu LTS docker images.

Registry:
* Repo 1
  + Image v1
  + Imave v2
* Repo 2
* Repo 3

Have to tag an image to push to DockerHub.

Tags are mutable... can use the same tag for an image that has changed (e.g. a bug fix). Use the hash in the image digest to see exact version.

Manifest lists for multiple architectures.

## 7. Containers

Containers run until the app exits (p74). Killing the app kills the container (p80).

VMs vs OCI containers... VMs have "VM tax"... each VM portions off hardware resources and has its own OS which consumes a lot of resources, slower to start, more licensing/admin costs. On the plus side, VMs are more isolated, which has security benefits (p76). OCI containers only use "OS resources".

Not good practice to modify files in a container (creates new layers?). Use volumes for this.

Stop containers gracefully. Prefer stop/rm vs rm -f (i.e. SIGTERM vs SIGKILL).

Restart policy: always, unless-stopped, on-failed.

## 8. Containerizing and App

Dockerfile details.

Multistage builds. Maintaining multiple Dockerfiles for dev/production is a pain. Multistage builds allow you to have multiple "FROM" instructions to build multiple images from a single Dockerfile. "COPY --from" instructions can copy production files from dev image to production image.

Build cache. This was covered in the getting started guide. A change in any layer of your image will cause all subsequent layers to be rebuilt, so pay attention to the order of instructions in your Dockerfile.

Squash layers. You can squash layers together. A typical good use would be to squash layers of a common base layer.

If using apt, consider --no-install-recommends flag.

## 10. Docker Swarm

Ability to configure and deploy several docker hosts as an application.

Competitor to Kubernetes. Claims to be easier to configure and deploy, but a smaller ecosystem and less momentum than Kubernetes. p127.

Example of building a swarm with 3 managers and 3 workers. Managers act as workers.

High availability (HA). Swarm uses [RAFT](https://en.wikipedia.org/wiki/Raft_(algorithm)). 3-5 managers is optimal, don't go over 7. One of the managers is the leader. Connect your nodes with reliable high-speed networks. Author claims that splitting nodes across services like AWS and Azure at the moment is "a bit of a daydream" (p133).

Swarm services specify the _desired state_ of the swarm. (e.g. how many nodes should run.)

Examples of backing up and restoring a swarm.

## 13. Volumes and Persistent Data

There is a thin read/write layer in docker containers for non-persistent data (p190). Suggestions for the proper storage driver for this based on OS on p191.

Use volumes for persistent data. Watch out for race conditions when multiple containers share a volume.

## 15. Security

Docker uses many security layers in the Linux and Docker levels.

Linux security technologies:
* Kernel namespaces: Process ID, network, filesystem, IPC, user, uts (p220). A common user mapping is the container root to non-root on the host.
* Control groups: Set limits on resources. See Liz Rize [Containers From Scratch](https://www.youtube.com/watch?v=8fi7uSYlOdc) talk for example of limiting process count to 20.
* [Capabilities](https://man7.org/linux/man-pages/man7/capabilities.7.html): Allow/disallow certain capabilities... the book gives examples of CAP_CHOWN for allowing changing file ownership. Looks like 41 of them at the time of this writing. (Hand count of man capabilities.)
* Mandatory Access Control Systems: Current implementations are SELinux and AppArmor.
* [seccomp](https://man7.org/linux/man-pages/man2/seccomp.2.html): Run in filter mode to allow/disallow different [syscalls](https://man7.org/linux/man-pages/man2/syscall.2.html). There are [326](https://medium.com/@akhandmishra/important-system-calls-every-programmer-should-know-8884381ceadb) system calls in linux 3.0.

Docker platform security technologies:
* Swarm mode: Lots of security by default in swarm mode: TLS encryption, rotating CA certificates, join tokens, etc.
* Image scanning: E.g. [Snyk](https://snyk.io/docker/)
* Docker content trust: Can sign images to verify.
* Docker secrets: System for providing passwords, certificates, etc to containers in a secure way.

Security config comes with reasonable defaults, because we "... know that security can be complicated and boring." (p218). Haha!
