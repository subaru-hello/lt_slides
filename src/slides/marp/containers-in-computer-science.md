---
marp: true
theme: default
paginate: true
---

<style>
  :root {
    --color-background: #ffffff;
    --color-foreground: #2C3E50;
    --color-highlight: #4CAF50;
    --color-accent: #3498DB;
    --color-muted: #7F8C8D;
  }

  section {
    font-family: 'Helvetica Neue', 'Hiragino Sans', 'Yu Gothic', sans-serif;
    background: white;
    color: var(--color-foreground);
  }

  /* Title slide style */
  section.lead {
    background: #E5E5E5;
    text-align: center;
    display: flex;
    flex-direction: column;
    justify-content: center;
  }

  section.lead h1 {
    font-size: 2.5em;
    font-weight: 500;
    color: #333;
    margin-bottom: 0.5em;
  }

  section.lead p {
    font-size: 1em;
    color: #666;
    margin-top: 0;
  }

  /* Content slide style */
  section:not(.lead) {
    padding: 70px;
  }

  section:not(.lead)::before {
    content: '';
    position: absolute;
    top: 60px;
    left: 70px;
    width: 80px;
    height: 4px;
    background: linear-gradient(90deg, #4CAF50 0%, #FF9800 50%, #2196F3 100%);
  }

  h1 {
    font-size: 2.2em;
    font-weight: 500;
    color: var(--color-foreground);
    border: none;
  }

  h2 {
    font-size: 1.8em;
    font-weight: 500;
    color: var(--color-foreground);
    margin-top: 0.5em;
    padding-bottom: 0.2em;
  }

  h3 {
    font-size: 1.3em;
    font-weight: 500;
    color: var(--color-accent);
    margin-top: 0.5em;
  }

  strong {
    color: var(--color-foreground);
    font-weight: 600;
  }

  blockquote {
    border-left: 4px solid var(--color-highlight);
    padding-left: 1em;
    color: var(--color-muted);
    font-style: italic;
    margin: 1em 0;
  }

  blockquote strong {
    color: var(--color-foreground);
  }

  ul, ol {
    line-height: 1.8;
  }

  table {
    margin: 1em auto;
  }

  th {
    background: #f5f5f5;
    font-weight: 600;
  }

  code {
    background: #f5f5f5;
    padding: 0.2em 0.4em;
    border-radius: 3px;
    font-size: 0.9em;
  }

  pre code {
    background: #f8f8f8;
    padding: 1em;
    display: block;
    overflow-x: auto;
  }

  /* Accent colors for special text */
  .accent { color: var(--color-accent); }
  .highlight { color: var(--color-highlight); }
  .muted { color: var(--color-muted); }
</style>

<!-- _class: lead -->

# How Containers Have Been Utilized in Computer Science

From physical boxes to virtual containers

---

## Previously...

We explored what **boxes and containers** do in the physical world:

- **Preserve** original value
- **Isolate** and protect contents
- **Standardize** for efficient delivery

Today: How these concepts apply to **software**

---

## Today's Journey

### From Physical to Virtual

We'll explore how containers are used in:
- Software development
- Internet infrastructure  
- Modern computing

Let's see how the **same principles** apply in the digital world

---

## Quick Recap

### Container = A portable place that preserves original value

✓ Encloses complexity  
✓ Protects what's inside  
✓ Packages requirements  
✓ Delivers efficiently

_Same concept, different implementation_

---

## Software Containers at a Glance

When "container" is mentioned in software, most think of **Docker**

Docker made containers **easy to use** on a host OS

- Shares the host kernel
- Borrows CPU/RAM/files
- Starts **far faster** than a VM

---

## Docker in One Minute

### Three Core Components

1. **CLI → Daemon → Runtime**  
   Docker CLI talks to daemon, which uses runtime to start containers

2. **Images and Registries**  
   Containers run from images; images live in registries

3. **Three Core Actions**  
   `pull`, `build`, `run` — start a server in seconds!

---

## Docker Architecture

![width:700px](https://storage.googleapis.com/zenn-user-upload/125e55bfadfb-20250830.jpeg)

**Client** → **Daemon** → **Runtime** → **Container**

_Source: Docker Documentation_

---

## Architecture Components

- **Client (CLI)**: Sends commands over REST API
- **Daemon (dockerd)**: Orchestrates builds, images, containers
- **Runtime chain**: Creates processes on the host
- **Registry**: Stores and serves images

> **Daemon**: A long-running background process waiting for requests

---

## Core Concepts You Should Know

### Isolation & Resource Management

- **Namespaces**: Isolate "what a process can see"
  - PID, NET, MNT, UTS, IPC, USER
  
- **cgroups**: Limit "how many resources it can use"
  - CPU, memory, I/O

- **Layers & CoW**: Efficient storage
  - Images = read-only layers
  - Containers add small writable layer

---

## More Core Concepts

### Networking & Security

- **Network drivers**: bridge, host, overlay
- **Security hardening**: 
  - Drop Linux capabilities
  - Use seccomp, AppArmor/SELinux
  - Prefer non-root users
- **Ephemeral by design**: Use volumes for persistence

---

## Tiny Hands-on: Build Process

### Let's see Docker in action!

```bash
$ docker build -t node-demo .
[+] Building 17.0s (14/14) FINISHED
 => [internal] load build definition from Dockerfile
 => [build 1/5] FROM docker.io/library/node:20-alpine
 => [build 2/5] WORKDIR /app
 => [build 3/5] COPY package*.json ./
 => [build 4/5] RUN npm ci --no-audit --no-fund
 => [build 5/5] COPY . .
 => exporting to image
 => naming to docker.io/library/node-demo
```

---

## Hands-on: Run & Test

### Launch the server

```bash
$ docker run --rm -p 3000:3000 --name node-demo node-demo
Server is running on http://0.0.0.0:3000
Environment: production
Press Ctrl+C to stop
```

### Test with curl

```bash
$ curl http://localhost:3000
{"message":"Hello from Docker! 🐳",
 "timestamp":"2025-08-31T05:09:06.619Z",
 "environment":"development"}
```

**Pretty easy, right?**

---

## A PC is Also a Container

### A box that packs hardware and software

![width:500px](https://storage.googleapis.com/zenn-user-upload/39e069b428a1-20250831.png)

Inside the case:
- CPU, memory, disk, ports
- Small programs that wake the machine
- OS that runs everything

---

## The PC as a Container

### Hides complexity, provides simplicity

- **Press power** → Tiny program checks parts
- **OS takes over** → Sets rules, manages resources
- **Apps run isolated** → Protected from each other
- **Files persist** → Work stays after power off

> The PC bundles many parts and gives them a **clean interface**

---

## Containers vs PCs

### Different scales, same concept

| **PC (The Port)** | **Container (The Box)** |
|-------------------|------------------------|
| Long-lived (weeks/months) | Short-lived (ephemeral) |
| Owns hardware | Borrows from host |
| Full OS | Shares host kernel |
| Stable base | Portable unit |
| Sets the rules | Follows the rules |

---

## The Perfect Fit

### How they work together

**PC = The stable base**
- Handles hardware, drivers, filesystems
- Provides security foundation
- Long-term persistence

**Container = The shipping unit**  
- Packages business logic
- Quick to start/stop/replace
- Consistent across environments

---

## Key Benefits

### Why containers shine

✓ **Portability**: Same build in dev and prod  
✓ **Speed**: Start in seconds, not minutes  
✓ **Isolation**: Apps don't interfere  
✓ **Efficiency**: Share kernel resources  
✓ **Reproducibility**: Build once, run anywhere  
✓ **Scalability**: Easy to replicate

---

## Summary

### Containers in Computer Science

"Container" is a **bigger idea** than Docker:
- Shipping boxes in the physical world
- PCs bundling hardware and OS
- VMs providing full isolation
- Docker containers for apps

All do the same job: **wrap, hide, protect, simplify, and make portable**

---

## Remember This

> **"The PC is the port, steady and in control;  
> The container is the box, portable and quick to move."**

This is the essence of containers in computing

---

<!-- _class: lead -->

# Thank You

Questions?

### Repository
github.com/subaru-hello/docker-basic-demo