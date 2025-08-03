# COBRA Framework: Cooperative Blockchain Resource Allocation for 6G UAV Networks

## 🔍 Overview

**COBRA** is a decentralized framework designed to manage task offloading in edge computing environments composed of **UAVs** (drones) and **Edge Servers (ECs)**, within the context of **6G/NTN** networks. It combines blockchain-based trust management with real-time decision-making to achieve energy-aware, reliable, and fair computation distribution.

The framework includes:

* A fully Dockerized **Hyperledger Fabric** blockchain (5 peers, 1 orderer, Raft consensus)
* Multiple offloading strategies implemented in **Go smart contracts** (COBRA, Round Robin, Random, Energy-Aware, ECP)
* A **Go simulation engine** for task generation and evaluation
* A **Go SDK** to interface with the blockchain (using `cobra-config.yaml`)
* CSV-based results and a **Python graph generator** for visualization

## 🧠 Supported Task Types

We simulate 6 classes of 6G tasks, derived from key academic sources:

* **Immersive Communication** (e.g., XR, holography)
* **Hyper-Reliable Low-Latency Communication** (e.g., industrial automation)
* **Ubiquitous Connectivity** (e.g., IoT in rural areas)
* **Massive Communication** (e.g., sensors in smart cities)
* **AI & Communication** (e.g., autonomous driving, offloading AI workloads)
* **Integrated Sensing and Communication** (e.g., navigation, movement detection)

Each task type varies in execution time and resource demand. The simulation uses a `time.Sleep` mechanism based on latency data from 5 reference papers.

## 🧪 Simulation Scenario

With the rise of **NTNs** and **6G technologies**, COBRA envisions a cooperative infrastructure where UAVs and ECs form temporary computing networks in remote or underserved regions. Blockchain ensures trust, fairness, and performance monitoring in this decentralized ecosystem.

UAVs from different providers contribute computing power. The system automates operations, manages autonomous fleets, and adapts to varying resource availability and task types.

## 🎯 Problem Statement

In this hybrid infrastructure, task offloading must:

* Minimize **energy use**, especially for UAVs with limited battery
* Maintain **high reliability** and **low latency**
* Incentivize participation through a **reputation-based system**

## ⚙️ Offloading Algorithms

The smart contract implements five strategies:

* **Round Robin**: simple cyclic distribution
* **Random**: random eligible device selection
* **ECP**: prioritize edge servers
* **Energy-Aware**: select based on battery and compute resources (from Wang et al.)
* **COBRA Algorithm**: hybrid of:

  * **Task Cost Index (TCI)**: based on energy + computation
  * **Reliability Index (RI)**: battery, resources
  * **Reputation Index (Rp)**: historical success ratio

## 🔧 Smart Contract Functions

* Register & update devices
* Log task execution
* Recalculate reputation over time
* Maintain ledgers: devices, tasks, reputation

## 🧬 Simulation Engine

Written in Go, it performs:

* Task generation (\~2000 tasks per run)
* Real-time interaction with blockchain
* Metrics tracking:

  * Task success/failure
  * Task duration & variance
  * Bandwidth (tasks/s)
  * Consensus delay
  * UAV battery & EC load

## 🧰 Repository Structure

```
cobra-framework/
├── blockchain/      # Hyperledger network configs, Docker, crypto
├── chaincode/       # Smart contracts (Go)
├── sdk/             # Go SDK and client tools
├── simulation/      # Go task simulation engine
├── results/         # CSV + Python plots
├── docs/            # Architecture, installation, troubleshooting
├── Makefile         # Automation for build, deploy, test
└── README.md        # Project overview
```

## 📋 Prerequisites

* Ubuntu 21.04
* Go (v1.18 or lower)
* Docker + Docker Compose
* Hyperledger Fabric v2.x

## 🚀 Deployment Guide (Simplified)

1. **Clone blockchain setup**:
   [https://github.com/AxelOscar/Hyperledger-Blockchain-Fabric-Network-COBRA](https://github.com/AxelOscar/Hyperledger-Blockchain-Fabric-Network-COBRA)
2. **Deploy Fabric with Docker** (5 peers + 1 orderer, Raft)
3. **Install Chaincode** using provided CLI scripts:

   * Package, Install, Approve, Commit, Init
4. **Run Simulation**:

   * Place Go files in `fabric-client`
   * Configure `cobra-config.yaml`
   * `go build simulation.go && ./simulation`

> 💡 Modify task distributions, Lambda/Epsilon weights in `simulation.go` to explore different behaviors

## 📊 Example Results

Run `generate_graphes.py` to visualize:

* UAV battery depletion curves
* Task delay (Tx + Exec)
* Offloading distribution by algorithm

## 📖 References

Key research sources used for simulation modeling:

* Feng et al. (URLLC)
* Hazarika et al. (XR latency)
* Ma et al. (IoT + reliability)
* Zhang et al. (sensing for AV)
* Yuan et al. (3D holography + MEC)

## ⚖️ License

Licensed under the Apache 2.0 License — see [`LICENSE`](./LICENSE) for details.

---

**Author**: Axel Oscar
Project maintained as part of ongoing research in cooperative edge & blockchain architectures.
