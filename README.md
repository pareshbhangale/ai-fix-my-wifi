# 📡 ai-fix-my-wifi

> An autonomous, zero-noise prompt for AI coding agents (**Claude Code**, **Google Antigravity**, **Cursor**, **Codex**, or CLI LLMs) to systematically diagnose Wi-Fi health, defeat bufferbloat & RF congestion, and produce verified Before-vs-After benchmark tables.

---

## ⚡ Why This Prompt Exists

Most AI assistants give generic, placebo advice: *"flush your DNS"*, *"restart your Mac"*, or run pings concurrently with speed tests (which pollutes idle latency). 

`ai-fix-my-wifi` enforces a **strictly sequential, 3-phase engineering methodology**:
1. **Zero-Load Isolation**: Measures idle Wi-Fi vs. ISP jitter, inspects Clear Channel Assessment (CCA) airtime utilization, and tests uncached DNS latency with randomized subdomains.
2. **Saturation & Bufferbloat**: Runs Apple `networkQuality -v` to measure true working latency (RPM) and ECN status under load.
3. **Router Recovery Logic**: Troubleshoots embedded ONT quirks (like router web servers timing out on root `/` by probing `/login.cgi` and HTTPS).
4. **Before vs. After Verification**: Re-runs the identical benchmark suite after router changes to prove latency and airtime improvements with real delta metrics.

---

## 🚀 Quick Start

1. Open your AI agent terminal or IDE assistant (**Claude Code**, **Antigravity**, **Cursor**, or any terminal LLM).
2. Copy and paste the prompt below into the chat.
3. The agent autonomously runs Phase 1 and presents your baseline findings.
4. Apply the recommended router/DNS settings and confirm with `done` to generate your **Before vs. After report**.

---

## 📋 The Master Prompt

```markdown
Diagnose my Wi-Fi and home network, identify the real bottlenecks behind slow speeds or instability, provide tailored fixes, and generate a Before vs. After benchmark comparison.

Execute all diagnostics strictly sequentially (never run ping/traceroute concurrently with throughput/bufferbloat tests to prevent load-polluting idle measurements).

### Phase 1: Sequential Baseline Diagnostics

1. Wi-Fi Link & RF Congestion:
   - Identify active Wi-Fi interface, PHY mode (802.11ac/ax/be), band (2.4 / 5 / 6 GHz), channel, channel width (20 / 40 / 80 / 160 MHz), signal RSSI, noise floor, calculated SNR, MCS index, and interface packet errors (Ierrs/Oerrs/Collisions).
   - Inspect macOS `airportd` Link Quality Metric (LQM) logs to measure Clear Channel Assessment (CCA) airtime utilization (`ccaOtherTotal` vs `ccaSelfTotal`) to detect hidden co-channel contention.
   - Scan local RF airspace to detect competing BSSIDs and identify clean, non-interfering channels (distinguishing non-DFS UNII-1 channels 36–48 from DFS channels and UNII-3).

2. Hop-by-Hop Latency & Stability (Under Zero Load):
   - Ping default gateway (30 ICMP packets) to isolate local Wi-Fi hop jitter from WAN jitter.
   - Ping public Anycast IP `1.1.1.1` (30 packets) and IPv6 counterpart (if dual-stack is active).
   - Run a traceroute (up to 8 hops) to map the first ISP peering hops and locate latency jumps.
   - Check for active bandwidth hogs (established sockets/processes), active VPNs/proxies, or recent Wi-Fi roam/deauth events.

3. DNS Resolver Latency Benchmark:
   - Identify active system resolvers and their priority order (`nameserver[0]`, `nameserver[1]`).
   - Benchmark both UNCACHED (using randomized subdomains to bypass local cache) and CACHED lookups across: current system resolvers, Cloudflare (`1.1.1.1`), Google (`8.8.8.8`), and the router gateway.

4. Throughput & Bufferbloat Under Saturation:
   - Run `networkQuality -v` (on macOS) or equivalent load test to measure downlink/uplink capacity, idle latency, loaded latency / responsiveness (RPM), and ECN/L4S status.

---

### Phase 2: Analysis & Action Plan

Deliver:
1. Baseline Summary Table: Exact measured values for all metrics above.
2. Ranked Bottlenecks: Top root causes ordered by real-world impact (RF co-channel interference, bufferbloat, unoptimized DNS priority, or legacy Wi-Fi modes).
3. Immediate Local Fixes: Non-destructive local OS commands (e.g., instant DNS optimization via `networksetup`).
4. Router & Gateway Investigation:
   - Identify the router's vendor via default gateway MAC address (OUI lookup).
   - Test router admin accessibility on standard ports (80/443) and test both root (`/`) and specific login paths (e.g., `/login.cgi`, `/login.html`, or HTTPS self-signed overrides) in case the web server times out on root redirects.
   - Ask for my router model/ISP or screenshot of the Wi-Fi settings page to provide exact click-by-click instructions:
     - 5 GHz: Locking to cleanest 80 MHz channel (e.g. Channel 36/44), avoiding neighbor overlap.
     - 2.4 GHz: Channel 1, 6, or 11 only, locked strictly to 20 MHz channel width.
     - Band steering / Smart Connect: Disabling if steering causes sticky 2.4 GHz drops.
     - Disabling unused Guest SSIDs to reclaim beacon airtime.

---

### Phase 3: Verification & Before vs. After Report

Once I apply the changes, re-run the identical sequential benchmark suite and present a structured "Before vs. After" comparison table showing:
- 5 GHz Channel, Bandwidth & CCA Airtime Congestion
- DNS Uncached & Cached Resolution Latency (ms and % delta)
- Downlink & Uplink Throughput (Mbps)
- Idle Latency vs Loaded Responsiveness (RPM and loaded ms % delta)
- Summary of real-world stability improvements
```

---

## 📊 Sample Benchmark Output

Here is an example benchmark table produced by this prompt:

| Diagnostic Metric | Before Optimization | After Optimization | Real Impact |
| :--- | :--- | :--- | :--- |
| **5 GHz Channel & Band** | Congested Channel (Co-channel overlap) | Clean Non-DFS Channel | **Zero neighboring AP collision** |
| **Airtime Congestion (CCA)** | ~25–30% busy | < 5% busy | **Eliminated frame drops & TX rate degradation** |
| **Uncached DNS Resolution** | ~30–40 ms (Unoptimized Resolver) | ~15 ms (Optimized Anycast) | **⚡ ~50% faster first-byte asset loading** |
| **Working Latency Under Load** | High (~110–120 ms / ~500 RPM) | Low (~50–60 ms / ~1000 RPM) | **🚀 2× Responsiveness boost under load** |
| **Packet Loss & Stability** | Intermittent jitter spikes | 0.0% loss / Flat RTT | **Stable VoIP, conferencing, and gaming** |

---

## 📄 License

MIT © pareshbhangale
