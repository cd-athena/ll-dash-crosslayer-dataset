# LL-DASH Cross-Layer Dataset

A large-scale, controlled dataset for Low-Latency DASH (LL-DASH) streaming research, capturing cross-layer interactions between Adaptive Bitrate (ABR) algorithms, Congestion Control (CC), and Active Queue Management (AQM) under both TCP and QUIC transport protocols.
📄 [**Paper**](https://drive.google.com/file/d/1tWgZHZ9nflEXVGQ_5nrxBLJh8oQfsXbQ/view?usp=sharing)

If you use MATGen in your research, please cite the associated paper.

---

## Abstract

Low-latency video streaming, such as Low-Latency DASH (LL-DASH), requires maintaining high Quality of Experience (QoE) under varying network conditions. In LL-DASH, QoE is jointly influenced not only by Adaptive Bitrate (ABR) decisions, but also by transport-layer Congestion Control (CC) and network-layer Active Queue Management (AQM), whose interactions remain insufficiently characterized due to limited cross-layer experimentation.

We present a large-scale LL-DASH dataset comprising approximately **2,000 controlled sessions** across:
- **3 dash.js ABR algorithms**: L2A, Dynamic, LoLP
- **3 CC schemes**: CUBIC, BBRv1, Prague
- **2 transport protocols**: TCP and QUIC
- **4 AQM configurations**: FIFO, FQ-CoDel, CAKE, DualPI2
- **Multiple congestion scenarios**: 0–3 background iperf flows

---

## Dataset Structure

The dataset is organized under a two-level transport hierarchy, `dataset/tcp/` and `dataset/quic/`, facilitating unified cross-protocol comparisons.

```
dataset/
├── quic/                          ← QUIC experiments 
│   └── [ABR]_[CC]_[AQM]_BGflow-[N]_Exp[ID]/
│       ├── client_log/
│       │   ├── client_per_second.csv
│       │   └── client_per_segment.csv
│       ├── server_cc_log/
│       │   ├── server_cc_metrics.csv
│       │   └── server_cc_metrics_raw.log
│       └── aqm_network_log/
│           ├── tc_qdisc_stats.log
│           ├── aqm_per_second.csv
│           ├── ifstat_dash.csv
│           └── ifstat_iperf.csv
└── tcp/                           ← TCP experiments 
```

### Folder Naming Convention

Each experiment folder follows the convention:

```
[ABR]_[CC]_[AQM]_[Delay]*_BGflow-[N]_Exp[ID]
```

| Field | Values |
|---|---|
| `ABR` | `l2a`, `lolp`, `dynamic` |
| `CC` | `cubic`, `bbr1`, `prague` |
| `AQM` | `pfifo`, `fq_codel`, `cake`, `dualpi2` |
| `Delay*` | `delay-1ms`, `delay-3ms`, `delay-5ms`, `delay-10ms` (Only applicable for Prague+DualPI2) |
| `BGflow-N` | Number of background iperf flows (0, 1, 2, 3) |
| `Exp ID` | Run number (75–84 for QUIC, 80–89 for TCP) |

> **Example:** `l2a_cubic_fq_codel_BGflow-2_Exp80` — L2A ABR with CUBIC CC, FQ-CoDel AQM, 2 background flows, run 80.
> **Example L4S:** `dynamic_prague_dualpi2_delay-1ms_BGflow-0_Exp85` — Dynamic ABR, Prague CC, DualPI2 (1ms delay), 0 background flows, run 85.

---

## Measurement Layers

All three subdirectories share timestamps, enabling direct cross-layer correlation between player-side QoE metrics, CC dynamics, and AQM behavior.

| Layer | Directory | Key Files | Description |
|---|---|---|---|
| **Client / Player** | `client_log/` | `client_per_second.csv`, `client_per_segment.csv` | dash.js player metrics: bitrate, buffer level, live latency, stall events, playback rate, quality switches |
| **Server / CC** | `server_cc_log/` | `server_cc_metrics.csv`, `server_cc_metrics_raw.log` | Transport congestion control metrics (TCP & QUIC): CWND, RTT, pacing/delivery rate, bytes sent/acked, retransmissions/loss |
| **AQM / Network** | `aqm_network_log/` | `aqm_per_second.csv`, `ifstat_dash.csv`, `ifstat_iperf.csv` | Queue backlog, drop/mark counts, DualPI2 sub-queue delays, link utilization for DASH and background traffic |

---

## Experimental Setup

- **Testbed**: Mininet-based emulation on Ubuntu
- **Background traffic**: 0–3 concurrent iperf TCP flows
- **ABR player**: dash.js with L2A, LoLP, and Dynamic algorithms
- **Video source**: Live stream via Livesim2
- **Runs per combination**: 10 repetitions (Run IDs 75–84 for QUIC, 80–89 for TCP)

---
## Results & Analysis

Detailed evaluations for both transport protocols are available:

- 📄 **[`ll_dash_tcp_findings.md`](ll_dash_tcp_findings.md)**: Summarises the key findings from the TCP evaluation, including loss-induced throughput collapse under FIFO, the DualPI2 3ms tuning rationale, the CAKE testbed artefact, and the 10 core performance metrics.
- 📄 **[`ll_dash_quic_findings.md`](ll_dash_quic_findings.md)**: Details the QUIC evaluation, comparing QUIC userspace CC behavior against TCP. Covers the proxy-induced micro-burst overhead requiring a 20ms DualPI2 tuning, QUIC's aggressiveness under FIFO against TCP cross-traffic, and the performance differences under various AQMs.

All corresponding evaluation plots (QoE, bitrate, stall, quality switches, latency, etc.) are available in the [`figures/`](figures/) directory.
