---
layout: project
title: Infrastructure
---

## Self-Hosted Website Infrastructure, Monitoring, and Resilience

A hands-on infrastructure project for hosting public websites on self-managed hardware, publishing them through existing Cloudflare routes and Nginx Proxy Manager, monitoring availability with Uptime Kuma, and delivering operational alerts through Telegram.

This documents verified work, real constraints discovered during testing, and the staged path toward independent cross-device monitoring.

## Project Goals

- Host and maintain public websites on self-managed infrastructure
- Keep website delivery and proxying under direct operational control
- Monitor public website availability and local services
- Deliver outage notifications through Telegram
- Validate monitoring paths before claiming they provide meaningful alerts
- Maintain recovery readiness for critical infrastructure

## Infrastructure Overview


| Component | Role | Status |
|---|---|---|
| Raspberry Pi | Hosts website services and Uptime Kuma | Online at last verification |
| Ubuntu Studio laptop | Hosts a separate website/server environment | Online at last verification |
| Cloudflare | Publishes routes to the internet | Existing routes in use |
| Nginx Proxy Manager | Routes incoming traffic to internal services | Existing deployment in use |
| Uptime Kuma | Availability checks and alert events | Running on the Raspberry Pi |
| Telegram | Receives alert notifications | Test delivery verified |

## Monitoring and Alerting

<img src="/assets/images/kuma2.png" width="75%">

<img src="/assets/images/kuma4.png" width="75%">

<img src="/assets/images/kuma3.png" width="75%">

| Monitor | Type | Purpose | Status |
|---|---|---|---|
| `pdm.la` | HTTP(S) | Public site availability | Green |
| `sumatralabs` | HTTP(S) | Public `.org` site availability | Green |
| Raspberry Pi — Direct Network Check | Ping | Pi reachability | Green |

Uptime Kuma's Telegram notification path has been tested and confirmed: a test alert was sent and received while the service was online.

## Monitoring Validation — Being Honest About the Limits

A green monitor is only meaningful if the monitoring system can genuinely reach its target and survive the failure it's meant to report.

**The Raspberry Pi power-loss blind spot:** Uptime Kuma runs on the same Raspberry Pi it's monitoring. If the Pi loses power, Kuma loses power with it — so it cannot report its own host going down. The current Pi monitor is a valid *reachability* indicator while the Pi is on, not a validated power-loss alert system.

**The Pi-to-laptop LAN test:** before creating any cross-device monitor, a direct test was run from the Pi to the Ubuntu Studio laptop. Both a ping test and a TCP port 22 (SSH) test failed — 100% packet loss, connection timed out. Rather than deploy a monitor that would silently never work, no monitor was created. The laptop itself was confirmed online separately, by loading its live website.

## Planned Cross-Device Monitoring

The next milestone is validating whether the Pi and laptop share a usable Tailscale path, then building independent monitoring in both directions — Pi watching the laptop, laptop watching the Pi — so neither device's monitoring depends on the device it's reporting on.

## Resilience and Recovery

- Created a complete image backup of the Raspberry Pi's microSD card
- Verified the backup by writing it to a spare card and confirming a successful boot and SSH access
- Isolated a suspected noisy USB drive to prevent further writes or damage

## Lessons From the Build

- Self-hosting isn't just deploying websites — it's validating delivery, observability, notification, and recovery paths
- A successful page load and a successful Telegram test prove different things, not the same thing
- A monitor that shares power or a host with its target can't report that target's total failure
- Testing found a real reachability gap between the Pi and the laptop *before* an unreliable monitor was ever deployed
- Documenting what isn't finished yet is more credible than claiming resilience that hasn't been tested

[View the full repository on GitHub →](REPLACE-WITH-YOUR-REPO-LINK)
