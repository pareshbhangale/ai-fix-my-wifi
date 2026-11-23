# 📡 ai-fix-my-wifi

> The definitive, autonomous network diagnosis prompt for AI coding agents (**Claude Code**, **Google Antigravity**, **Cursor**, **Codex**, and terminal LLMs). Systematically benchmarks Wi-Fi health, isolates RF congestion & bufferbloat, guides surgical router fixes, and outputs verified Before-vs-After benchmark tables.

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Platform](https://img.shields.io/badge/Platform-macOS%20|%20Linux%20|%20Windows-green.svg)](#)
[![AI Agent Compatible](https://img.shields.io/badge/AI%20Agents-Claude%20Code%20|%20Antigravity%20|%20Cursor-purple.svg)](#)

---

## ⚡ The Problem with Asking AI to "Fix My Wi-Fi"

Most AI assistants give generic, placebo advice: *"flush your DNS"*, *"restart your Mac"*, or run pings concurrently with speed tests (which pollutes idle latency). 

`ai-fix-my-wifi` enforces a **strictly sequential, 3-phase engineering methodology** designed by senior network engineers:

* **Zero-Load Isolation**: Measures idle Wi-Fi vs. ISP jitter, scans Clear Channel Assessment (CCA) airtime utilization, checks Apple `awdl0` (AirDrop/Continuity) off-channel stutter, and tests uncached DNS latency with randomized subdomains.
* **Path MTU & Fragmentation**: Verifies full 1500-byte MTU integrity (`ping -D -s 1472`) to detect ISP PPPoE clamping mismatches.
* **Dual-Stack Peering Asymmetry**: Measures IPv4 vs. IPv6 latency deltas (RFC 8305 Happy Eyeballs) to catch unoptimized ISP IPv6 transit routing.
* **Saturation & Bufferbloat**: Runs Apple `networkQuality -v` to measure true working latency (RPM) and ECN/L4S status under load.
* **Router Recovery Logic**: Automatically detects router vendors via MAC OUI and recovers from embedded ONT web server bugs (like thttpd timeouts on `/` by probing `/login.cgi` and HTTPS).
* **Before vs. After Verification**: Re-runs the identical battery of tests after router changes to prove latency and airtime improvements with real delta metrics.

---

## 🚀 Quick Start

1. Open your AI agent terminal or IDE assistant (**Claude Code**, **Antigravity**, **Cursor**, or any terminal LLM).
2. Copy and paste the prompt below into the chat.
3. The agent autonomously runs Phase 1 and presents your baseline findings.
4. Apply the recommended router/DNS settings and confirm with `done` to generate your **Before vs. After report**.

---

## 📋 The Master Prompt

```markdown
Diagnose my Wi-Fi, LAN, and ISP connection end-to-end, identify the real bottlenecks behind latency spikes, packet loss, or slow speeds, guide me through surgical fixes, and produce a verified Before vs. After benchmark comparison.

Run all tests strictly sequentially (never run ping/traceroute concurrently with throughput or bufferbloat tests to prevent load-polluting idle baseline measurements). Auto-detect my OS (macOS, Linux, or Windows) and use the native CLI tools accordingly.

### Phase 1: Deep Sequential Diagnostics

1. Wi-Fi Physical Link, RF Airspace & Hardware Contention:
   - Identify active Wi-Fi interface, BSSID, PHY mode (802.11a/b/g/n/ac/ax/be), operating band (2.4 / 5 / 6 GHz), control channel, channel width (20 / 40 / 80 / 160 MHz), RSSI (signal), noise floor, calculated SNR, transmit rate, MCS index, and interface packet errors (Ierrs/Oerrs/Collisions).
   - On macOS: Inspect `airportd` Link Quality Metric (LQM) logs to extract Clear Channel Assessment (CCA) airtime metrics (`ccaSelfTotal` vs `ccaOtherTotal`) to measure how much airtime is consumed by neighbor APs.
   - On macOS: Inspect Apple Wireless Direct Link (`awdl0`) state to detect periodic off-channel hopping latency spikes caused by AirDrop / Continuity.
   - Scan local RF airspace to detect co-channel (exact channel) and adjacent-channel (overlapping channel) AP interference. Categorize clean channels across UNII-1 (36–48, non-DFS), UNII-2 (DFS radar sensitive), and UNII-3 (149–165).

2. Path MTU (PMTUD) & Packet Fragmentation:
   - Send 1472-byte ICMP payloads with the Don't Fragment (DF) flag set (`ping -D -s 1472` on macOS/Linux, `ping -f -l 1472` on Windows) to verify if the link cleanly supports full 1500 MTU without silent dropping or fragmentation (identifying ISP PPPoE 1492 MTU mismatches).

3. Hop-by-Hop Latency, Stability & Peering Asymmetry (Zero-Load Baseline):
   - Ping default gateway (30 ICMP packets) to isolate Wi-Fi hop jitter (standard deviation) from ISP jitter.
   - Ping Anycast DNS `1.1.1.1` (30 packets) over IPv4.
   - Ping Anycast DNS `2606:4700:4700::1111` (10 packets) over IPv6 (if active) to detect IPv6 peering asymmetry / routing penalties (RFC 8305).
   - Run a 8-hop traceroute to map the local gateway, ISP CGNAT/aggregation edge, and upstream transit to isolate where latency jumps occur.
   - Check for active local bandwidth hogs (established connections via lsof/netstat), active VPNs, proxies, or recent Wi-Fi disassociation/deauth events.

4. Cold vs. Warm DNS Resolver Benchmark:
   - Identify active system resolvers and query priority order (`nameserver[0]`, `nameserver[1]`).
   - Benchmark UNCACHED latency using randomized subdomains (e.g. `probe-<random>.cloudflare.com` to prevent DNS caching) against CACHED latency for: active system resolvers, Cloudflare (`1.1.1.1`), Google (`8.8.8.8`), Quad9 (`9.9.9.9`), and the local router gateway.

5. Throughput & Saturated Bufferbloat Breakdown:
   - Run macOS `networkQuality -v` (or cross-platform equivalent) to measure:
     - Downlink & Uplink capacity (Mbps)
     - Idle Latency (ms and RPM)
     - Saturated Loaded Latency & Responsiveness score (RPM)
     - HTTP loaded latency spike factor (ratio of loaded RTT to idle RTT)
     - Directional bufferbloat (ingress vs. egress queue bloat)
     - ECN (Explicit Congestion Notification) and L4S negotiation status.

---

### Phase 2: Root-Cause Analysis & Guided Action Plan

Deliver:
1. Baseline Summary Table: Exact measured values for all metrics above.
2. Ranked Bottlenecks: Real-world impact order (RF co-channel interference, bufferbloat queue bloat, MTU fragmentation, sub-optimal DNS order, or legacy 802.11ac limits).
3. Safe Local Quick Wins:
   - Non-destructive OS configuration tweaks (e.g., DNS resolver priority order).
   - On macOS: Testing temporary `awdl0` suppression if AirDrop off-channel scans cause periodic ping spikes.
4. Router & Gateway Deep Investigation:
   - Identify router vendor via default gateway MAC address (OUI lookup).
   - Test router admin accessibility on ports 80/443; probe both root (`/`) and specific login endpoints (e.g. `/login.cgi`, `/login.html`, or HTTPS self-signed overrides) to recover from web server redirect bugs.
   - Ask for my router model, ISP, or a screenshot of the wireless settings to guide exact click-by-click fixes:
     - 5 GHz: Locking to the cleanest 80 MHz channel (e.g., non-DFS UNII-1 36/44), eliminating neighbor overlap.
     - 2.4 GHz: Locking strictly to Channel 1, 6, or 11 at 20 MHz width only (never 40 MHz).
     - Band Steering / Smart Connect: Disabling if devices are improperly forced to 2.4 GHz.
     - Disabling unused Guest SSIDs to eliminate beacon frame airtime overhead.
     - Bufferbloat mitigation: Router SQM (CAKE / FQ-CoDel) or rate-limiting recommendations.

---

### Phase 3: Verification & Before vs. After Report

Once I apply the changes, re-run the identical sequential benchmark suite and present a structured "Before vs. After" comparison table showing:
- 5 GHz Channel, Bandwidth & CCA Airtime Congestion Delta
- DNS Uncached & Cached Latency (ms and % improvement)
- Downlink & Uplink Throughput (Mbps)
- Idle Latency vs Loaded Responsiveness (RPM and loaded ms reduction)
- Summary of verified real-world stability gains (gaming, VoIP, streaming)
```

---

## 📊 Sample Benchmark Output

Here is an example benchmark table produced by this prompt on a fiber connection:

| Diagnostic Metric | Before Optimization | After Optimization | Real Impact |
| :--- | :--- | :--- | :--- |
| **5 GHz Channel & Band** | Congested Channel (Co-channel overlap) | Clean Non-DFS Channel | **Zero neighboring AP collisions** |
| **Airtime Congestion (CCA)** | ~25–30% busy | < 5% busy | **Eliminated frame drops & TX rate drops** |
| **Uncached DNS Resolution** | ~30–40 ms (Unoptimized Resolver) | ~15 ms (Optimized Anycast) | **⚡ ~50% faster first-byte asset loading** |
| **Working Latency Under Load** | High (~110–120 ms / ~500 RPM) | Low (~50–60 ms / ~1000 RPM) | **🚀 2× Responsiveness boost under load** |
| **Packet Loss & Stability** | Intermittent jitter spikes | 0.0% loss / Flat RTT | **Rock-solid gaming, VoIP, and calls** |

---

## 🛠️ Supported Tools & AI Agents

* **Anthropic Claude Code**: Run directly inside the terminal.
* **Google Antigravity**: Paste directly into the chat session.
* **Cursor / Windsurf**: Run inside the integrated terminal agent.
* **OpenAI Codex / ChatGPT CLI**: Fully compatible with shell execution tools.

---

## 📄 License

MIT © [pareshbhangale](https://github.com/pareshbhangale)
