# LL-DASH Cross-Layer Dataset

A large-scale, controlled dataset for Low-Latency DASH (LL-DASH) streaming research, capturing cross-layer interactions between Adaptive Bitrate (ABR) algorithms, Congestion Control (CC), and Active Queue Management (AQM) under both TCP and QUIC transport protocols.

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
├── quic/                          ← QUIC experiments (available now)
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
└── tcp/                           ← TCP experiments (coming soon)
```

### Folder Naming Convention

Each experiment folder follows the convention:

```
[ABR]_[CC]_[AQM]_BGflow-[N]_Exp[ID]
```

| Field | Values |
|---|---|
| `ABR` | `l2a`, `lolp`, `dynamic` |
| `CC` | `cubic`, `bbr1`, `prague` |
| `AQM` | `pfifo`, `fq_codel`, `cake`, `dualpi2` |
| `BGflow-N` | Number of background iperf flows (0, 1, 2, 3) |
| `Exp ID` | Run number (75–84) |

> **Example:** `l2a_cubic_fq_codel_BGflow-2_Exp80` — L2A ABR with CUBIC CC, FQ-CoDel AQM, 2 background flows, run 80.

---

## Measurement Layers

All three subdirectories share timestamps, enabling direct cross-layer correlation between player-side QoE metrics, CC dynamics, and AQM behavior.

| Layer | Directory | Key Files | Description |
|---|---|---|---|
| **Client / Player** | `client_log/` | `client_per_second.csv`, `client_per_segment.csv` | dash.js player metrics: bitrate, buffer level, live latency, stall events, playback rate, quality switches |
| **Server / CC** | `server_cc_log/` | `server_cc_metrics.csv` | Per-RTT QUIC congestion control metrics: CWND, RTT, pacing rate |
| **AQM / Network** | `aqm_network_log/` | `aqm_per_second.csv`, `ifstat_dash.csv`, `ifstat_iperf.csv` | Queue backlog, drop/mark counts, DualPI2 sub-queue delays, link utilization for DASH and background traffic |

---

## Experimental Setup

- **Testbed**: Mininet-based emulation on Ubuntu
- **Link capacity**: 7 Mbps bottleneck link
- **Background traffic**: 0–3 concurrent iperf UDP flows
- **ABR player**: dash.js with L2A, LoLP, and Dynamic algorithms
- **Video source**: HEVC live stream via livesim2
- **Runs per combination**: 10 repetitions (Run IDs 75–84)

---

## Availability

| Transport | Status |
|---|---|
| **QUIC** | ✅ Available — `datasets/quic/` |
| **TCP** | 🔜 Coming soon — `datasets/tcp/` |

---

## Citation

If you use this dataset in your research, please cite our paper:

```bibtex
@dataset{lldash_crosslayer_2025,
  title   = {LL-DASH Cross-Layer Dataset},
  author  = {},
  year    = {2025},
  url     = {https://github.com/cd-athena/ll-dash-crosslayer-dataset}
}
```

---

## License

This dataset is released for research purposes. Please refer to the repository license file for usage terms.
