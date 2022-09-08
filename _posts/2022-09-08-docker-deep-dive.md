---
layout: post
title:  "Book Notes: Docker Deep Dive"
category: book
---

![Book cover](/assets/docker-deep-dive.jpg)

By Nigel Poulton.

Book club time. We use docker but would like to know more about it. After a 15 minute scramble on the Internet we came up with Docker Deep Dive as the best available book.

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

Steps for getting you running with a container. All of this and more is covered in the getting started guide. My notes here: {% post_url 2022-08-08-docker-getting-started %}.

## 5. The Docker Engine

A high level discussion of the runtime part of the docker engine and runtime.

runc - Small cli wrapper for the libcontainer library, both are written in Go. It's the reference OCI runtime implementation.

containerd - cli that manages container lifecycle, push/pull images, etc. I think of this as all the docker features that clients (i.e. Kubernetes) want that otherwise don't want to use the rest of the docker suite/engine.

Page 38 has a great diagram and explanation for what happens when you run a docker cli command to run a container: docker cli -> docker daemon (gRPC API) -> containerd (CRUD-style over gRPC) -> runc (actually creates/runs the container). A small "shim layer" controls stdin/stdout, return codes, etc.

Containers run independently of the docker daemon, so docker can be upgraded without having to restart the containers, which is a "huge benefit" (p39).

This was odd... The back half of the chapter (8/16 pages) is detailed instructions for TLS configuration between containers.

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
