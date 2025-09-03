# How Containers have been utilized in Computer Science?

Previously we explored what boxes and containers do in the physical world.
This article shifts to the virtual world: how containers are used in software and on the internet.

## What containers do (quick recap)

Container = a portable place that preserves original value.
It encloses complexity, protects what’s inside, and packages requirements so they can be delivered efficiently.

Reference: https://www.docker.com/resources/what-container/
![](https://storage.googleapis.com/zenn-user-upload/eb4919679913-20250830.jpeg)

## Software containers at a glance

When “container” is mentioned in software, most people think of Docker.
Docker made containers easy to use on a host OS. Because a container shares the host kernel (and borrows CPU/RAM/files), it starts far faster than a virtual machine.

### Docker in one minute
- CLI → Daemon → Runtime. The Docker CLI talks to the Docker daemon, which uses a runtime to start containers.
- Images and registries. Containers run from images; images live in registries.
- Three core actions: pull, build, run. With these, a server can be started in seconds.

### Docker architecture (high-level)
![](https://storage.googleapis.com/zenn-user-upload/125e55bfadfb-20250830.jpeg)
https://docs.docker.com/get-started/docker-overview/#docker-architecture

- Client (CLI). Sends commands over a REST API.
- Daemon (dockerd). Orchestrates builds, images, and containers.
- Runtime chain. The daemon delegates to a supervisor and runtime to create processes (containers) on the host.
- Registry. Stores and serves images for pull / push.

Daemon: a long-running background process that waits for requests or events.
See also: “[daemon (software)](https://ja.m.wikipedia.org/wiki/%E3%83%87%E3%83%BC%E3%83%A2%E3%83%B3_(%E3%82%BD%E3%83%95%E3%83%88%E3%82%A6%E3%82%A7%E3%82%A2))” for the general concept.


 ### Core concepts you should know
 Docker has core concepts. It helps you to understand other technologies used in the Internet.
- Namespaces (PID/NET/MNT/UTS/IPC/USER): isolate “what a process can see.”
- cgroups: limit “how many resources it can use” (CPU, memory, IO).
- Layers & CoW: images are read-only layers; each container adds a small writable layer (copy-on-write). Copy on write means that CPU does not copy data even CPU has been requested to copy the data unless it required to write its original data.
- Networking drivers: bridge (default, single host), host (no isolation), overlay (multi-host), others.
- Security hardening: drop Linux capabilities, use seccomp, AppArmor/SELinux; prefer non-root users.
- Ephemeral by design: persist data with volumes or external services.

More: [Docker overview](https://docs.docker.com/get-started/docker-overview/)


### Tiny hands-on: build → run → curl
Here is a quick demo for understanging how portable the Docker is.
You can also see entire codes at repository: https://github.com/subaru-hello/docker-basic-demo


#### build
You only need to write a one [Dockerfile](https://github.com/subaru-hello/docker-basic-demo/blob/main/Dockerfile) and put it same directory where you type docker build command.

> build process
```terminal
 docker-basic-demo % docker build -t node-demo .
[+] Building 17.0s (14/14) FINISHED                    docker:desktop-linux
 => [internal] load build definition from Dockerfile                   0.0s
 => => transferring dockerfile: 522B                                   0.0s
 => [internal] load .dockerignore                                      0.0s
 => => transferring context: 210B                                      0.0s
 => [internal] load metadata for docker.io/library/node:20-alpine      5.5s
 => [build 1/5] FROM docker.io/library/node:20-alpine@sha256:df025585  6.9s
 => => resolve docker.io/library/node:20-alpine@sha256:df02558528d3d3  0.0s
 => => sha256:d0ee4f72f137e857ba5cd1c63b0e215506c801c 1.26MB / 1.26MB  0.8s
 => => sha256:df02558528d3d3d0d621f112e232611aecfee7c 7.67kB / 7.67kB  0.0s
 => => sha256:b87d314c74869bc6a87e06644c8eb297cc17022 1.72kB / 1.72kB  0.0s
 => => sha256:03ddb5ef6c074527c82bc2dd18c363785238ee4 6.44kB / 6.44kB  0.0s
 => => sha256:6e174226ea690ced550e5641249a412cdbefd2d 4.13MB / 4.13MB  1.0s
 => => sha256:daf846a830553a0ff809807b7f2d956dbd9dc 42.67MB / 42.67MB  5.6s
 => => sha256:b81f90efe9ad6644ae45d55b7509bdcbcc35c508747 445B / 445B  1.4s
 => => extracting sha256:6e174226ea690ced550e5641249a412cdbefd2d09871  0.1s
 => => extracting sha256:daf846a830553a0ff809807b7f2d956dbd9dcb959c87  1.1s
 => => extracting sha256:d0ee4f72f137e857ba5cd1c63b0e215506c801c1bc88  0.0s
 => => extracting sha256:b81f90efe9ad6644ae45d55b7509bdcbcc35c5087478  0.0s
 => [internal] load build context                                      0.0s
 => => transferring context: 30.71kB                                   0.0s
 => [build 2/5] WORKDIR /app                                           0.2s
 => [build 3/5] COPY package*.json ./                                  0.0s
 => [stage-1 3/6] RUN apk add --no-cache dumb-init                     2.9s
 => [build 4/5] RUN npm ci --no-audit --no-fund                        1.6s
 => [build 5/5] COPY . .                                               0.0s
 => [stage-1 4/6] COPY package*.json ./                                0.0s 
 => [stage-1 5/6] RUN npm ci --omit=dev --no-audit --no-fund &&     n  1.3s
 => [stage-1 6/6] COPY --from=build /app/server.js ./server.js         0.0s
 => exporting to image                                                 0.1s
 => => exporting layers                                                0.0s
 => => writing image sha256:a89d2a81ee3d6b224bb5c64fc985b07011ee93910  0.0s
 => => naming to docker.io/library/node-demo    
```

#### run
Once you've finished building, let's launch a server.
> run
```terminal
docker-basic-demo % docker run --rm -p 3000:3000 --name node-demo node-demo
Server is running on http://0.0.0.0:3000
Environment: production
Press Ctrl+C to stop
```

#### test
Then after the server runnning on port 3000, hit the URL with curl.
```terminal
 % curl http://localhost:3000
{"message":"Hello from Docker! 🐳","timestamp":"2025-08-31T05:09:06.619Z","environment":"development"}%                                   
```

Hey, it was pretty easy, right?

### A PC is also a kind of container
A PC is a box that packs both hardware and software.
Inside the case you have the CPU, memory, disk, and ports.

![](https://storage.googleapis.com/zenn-user-upload/39e069b428a1-20250831.png)

You also have small programs that wake the machine, and the operating system that runs everything. The PC hides the messy wires and steps. It gives you a simple screen and keyboard so you can work.

When you press the power button, a tiny program checks the parts. Then it hands control to the operating system. The OS sets the rules. It decides which app gets the CPU. It keeps apps in their own space so they do not break each other. It saves files on disk so your work stays after power off. In short, the PC bundles many parts and gives them a clean interface.

The PC also protects what is inside. You log in with a user. Files have owners and permissions. The firewall blocks bad traffic. If one app is noisy, the OS can limit it so others stay healthy. The PC is a stable base you can trust for a long time.

Because parts follow common rules—like USB, files, and sockets—apps do not need to know every hardware detail. They talk to the OS, and the OS talks to the metal. That is why a PC feels like a big container that keeps many small containers in order.

## Containers vs PCs
Think of a PC as a port, and a container as a shipping box. The PC runs the operating system. It owns the CPU, memory, disk, and the network card. It sets the rules, keeps time, loads drivers, and protects the machine. It is long-lived. It stays up for weeks or months and gives us a safe place to run apps.

A container is just an app packed with the tools it needs. It is not a whole computer. It uses the same operating system as the PC, so it starts fast and stops fast. We first build an image. An image is like a sealed box with our app inside. Then we start a container from that image. If something breaks, we do not fix the box; we throw it away and start a fresh one from the same image. That is why runs are repeatable.

By default a container does not keep data forever. If we need data to last, we put it in a volume or a database outside the container. By default it also does not talk to the outside world. If we want to accept traffic, we open a port and map it to the host, like saying “use door 3000.” For safety, we give the container only the permissions it needs and avoid running it as root. The host can also limit CPU and memory so one noisy app does not hurt the others.

So the fit is simple: the PC is the stable base; the container is the unit we ship. Put business logic in containers, and let the PC handle hardware, drivers, filesystems, and security. When we need very strong walls—like running untrusted code—we can use a virtual machine instead. When we need quick rollouts, easy rollback, and the same build in dev and prod, containers shine.

If you remember just one line, remember this: the PC is the port, steady and in control; the container is the box, portable and quick to move.


## Summary
“Container” is a bigger idea than Docker. We see it in hardware and software alike: a shipping box, a PC that bundles hardware and OS, a VM, a process sandbox, and a Docker container. All of them do the same kind of job: they wrap something, hide the messy parts, protect the inside, give a simple interface to the outside, add a bit of identity (a name, a label), and make it easy to move, start, stop, and replace.

The walls are different—steel for a box, firmware + OS for a PC, the kernel’s rules for a container—but the purpose stays the same: keep things together, keep them safe, and make them portable. So think of your PC as the steady “big container,” and your software containers as the small, standard boxes you ship your apps in.
