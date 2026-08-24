---
title: "HumAIn OmarchyPi: a bounded edge node"
description: "The architecture of a Raspberry Pi-based HumAIn node: reproducible images, local context, typed health, and explicit enrollment."
tags:
  - architecture
  - HumAIn
  - OmarchyPi
  - edge computing
---

# HumAIn OmarchyPi: a bounded edge node

HumAIn OmarchyPi is an attempt to make a small computer feel like a local participant without turning it into an unattended remote-control device.

The target is a repeatable Raspberry Pi 5 node running an ARM64 desktop stack, a selected Omarchy-inspired shell, a local HumAIn adapter, and a narrow health-and-identity layer.

The important word is **bounded**.

A node should be able to show a local interface, resolve an explicitly public pointer, and report typed health. It should not infer authority from the fact that it is on a network.

## Node contract

```text
Manjaro ARM
  → Hyprland / UWSM
  → ARM64 Quickshell
  → selected Omarchy shell
  → public-only local HumAIn adapter
  → bounded node health and identity
```

This is an unsupported ARM port inspired by Omarchy, not the official x86_64 Omarchy installer. That distinction is part of the architecture. The name suggests lineage; it does not grant compatibility.

## Two deliverables

The project separates a **golden image** from a **bootstrap installer**.

The golden image is useful for fast demonstrations and a small controlled bench of similar Pi 5 nodes. It must be sanitized before cloning: host keys, authorized keys, machine identity, DHCP state, logs, history, caches, and demo receipts cannot travel accidentally to the next node.

The bootstrap installer is slower but more valuable. It begins from a verified Manjaro ARM image, checks the architecture and hardware, installs ARM-compatible layers, configures first boot, runs acceptance checks, and produces a versioned receipt.

The image is a delivery artifact. The bootstrap path is the source of truth.

## Identity without implied authority

Each node receives a new local identity on first boot. A node ID helps distinguish receipts; it is not an authentication credential.

The first safe identity record describes:

- node ID;
- role;
- capabilities;
- creation time;
- network scope.

Enrollment is explicit and operator-gated. A Pi does not become trusted merely because it booted successfully or appeared on the LAN.

## The first swarm is observation, not command and control

The first version of the swarm layer is deliberately boring. It may announce identity, version, capability, uptime, and bounded health receipts to an explicitly configured coordinator. It may queue non-sensitive health events while offline.

It may not:

- execute arbitrary remote shell commands;
- control the desktop remotely;
- inspect browser history, cookies, clipboard, page text, or the filesystem;
- distribute credentials automatically;
- mutate software across nodes automatically;
- expose a public reverse tunnel;
- perform autonomous financial, deployment, or destructive hardware actions.

The coordinator is optional. The node remains useful when it is absent.

## The HumAIn boundary

The local display shell can call a loopback adapter. The adapter can resolve an explicit public pointer and return public-only metadata. Optional health reporting can leave the node only through a configured LAN or coordinator endpoint.

No private context should cross that adapter boundary. No network reachability is permission.

This is a small implementation of a larger principle: intelligence at the edge should increase local capability without quietly expanding the authority of the system around it.

## Acceptance is a receipt

A fresh node is not accepted because the installer printed “complete.” It must pass observable gates, including:

- verified ARM image boots;
- hardware identity is correct;
- Hyprland and ARM64 Quickshell render;
- reboot returns to the intended shell;
- the HumAIn service is active on loopback;
- valid public pointers return `public_only`;
- query strings and fragments are rejected;
- the visible HumAIn module appears;
- the node identity is unique;
- power-loss recovery returns to the intended state;
- swarm health remains opt-in and bounded.

The project is not trying to make a Pi mysterious. It is trying to make the boundary visible at the place where the computer meets the world.

## Current status

The architecture is a design baseline and implementation path, not a claim that a public multi-node swarm already exists. The next meaningful proof is a second independent Pi passing the same gates, followed by a typed local health protocol.

The first two nodes should be boring. If they display emergent behavior before the receipts exist, it is probably a shell script.

## Source

- [HumAIn OmarchyPi repository](https://github.com/leo-guinan/humain-omarchypi)
- [Node architecture document](https://github.com/leo-guinan/humain-omarchypi/blob/main/docs/OMARCHYPI-NODE-ARCHITECTURE.md) <!-- # git-secret-ignore: public repository URL -->
- [[Architecture]]
