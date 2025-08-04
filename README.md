# COBRA Framework: Cooperative Blockchain Resource Allocation for 6G UAV Networks

## 🔍 Overview

COBRA (Cooperative Blockchain Resource Allocation) is a decentralized framework designed to manage task offloading in edge computing environments composed of **UAVs** (drones) and **Edge Servers (ECs)**, within the context of **6G/NTN** networks. It combines blockchain-based trust management with real-time decision-making to achieve energy-aware, reliable, and fair computation distribution.

This repository includes:

* A fully Dockerized **Hyperledger Fabric** blockchain (5 peers, 1 orderer, Raft consensus)
* Multiple offloading strategies implemented in **Go smart contracts** (COBRA, Round Robin, Random, Energy-Aware, ECP)
* A **Go simulation engine** for task generation and performance evaluation
* A **Go SDK** to interface with the blockchain (via `cobra-config.yaml`)
* CSV-based results and a **Python graph generator** for visualization

The simulation tracks task completion, UAV battery life, EC compute resources, and system performance metrics like bandwidth, task duration, and consensus time.

> [!NOTE]  
> You can adapt the proportion of tasks and number of UAVs/ECs directly in the simulation file. Adjust the Lambda and Epsilon parameters to tune the importance of energy-awareness and reputation.

> [!IMPORTANT]  
> For identical results, use the provided Hyperledger network setup or adapt your configurations accordingly:  
> [https://github.com/AxelOscar/Hyperledger-Blockchain-Fabric-Network-COBRA](https://github.com/AxelOscar/Hyperledger-Blockchain-Fabric-Network-COBRA)

You can find in this repository, 2 Folder and 2 Files :
- The ***"fabric_simulation_client_code"*** folder contains some code in go to interecact with the Hyperledger blockchain, the ***"clean"*** code allow to delete all data in the blockchain, the ***"queryAll"*** code allow to show the data of an ledger in this case the device ledger or the task ledger, the ***"register_device"*** allow to register massively device in the blockchain you can chosse the number of device and the proportion beetween EC or UAV, finnaly the ***"cobra-config"*** yaml file is the most important is allow the communication beetween the client and the blockcahin he containes parameter and credential to acces on the blockchain.
-  The ***"result"*** folder contains different csv result files of the simulation and also a python code to generate graphes.
-  For the 2 files, there are the ***"Cobra_Algo_SC"*** go file is the smart contract inplement in my Blockchain and the ***"simulation"*** go file to simulate the task send and have the result, a more detailed explanation is available below.

## 🧐 Scenario Use Case

With the rapid evolution of **6G technologies** and the rise of **Non-Terrestrial Networks (NTNs)**, it becomes feasible to deploy temporary high-performance computing networks in areas lacking infrastructure. The integration of UAVs and edge servers enables dynamic, distributed, and resilient computation.

This scenario proposes a **cooperative computing platform** where UAVs from different providers offer aerial compute capacity and dynamic communication. This infrastructure supports industrial processes through AI-based automation and enhances transport systems in rural or remote regions. Blockchain ensures:

* Transparent interaction among entities
* Secure recordkeeping of task results
* Fair incentive mechanisms

The COBRA framework is especially relevant for intelligent transport systems, autonomous logistics, and critical infrastructure monitoring in edge scenarios.

## ❓ Problem Statement

In UAV/EC hybrid networks, multiple challenges arise:

* **Limited energy and compute**: UAVs have tight resource constraints.
* **Dynamic topologies**: UAVs join/leave the network.
* **Need for fairness and trust**: Providers must be rewarded for honest and effective participation.

COBRA addresses these challenges by combining blockchain with a multi-criteria offloading algorithm that evaluates:

* **Cost** (TCI): energy + compute usage
* **Reputation** (Rp): historical performance
* **Reliability** (RI): battery, availability, etc.

The result is a resilient, scalable, and fair offloading strategy for mission-critical edge networks.

## 🧠 Simulation infos and Supported Task Types

All the UAV and EC are simulate, an EC is 10 time more powerful than a UAV and for the task we simulate 6 classes of 6G tasks, derived from key academic sources(3GPP Worshop of August 2024):

* **Immersive Communication** (e.g., XR, holography)
* **Hyper-Reliable Low-Latency Communication** (e.g., industrial automation)
* **Ubiquitous Connectivity** (e.g., IoT in rural areas)
* **Massive Communication** (e.g., sensors in smart cities)
* **AI & Communication** (e.g., autonomous driving, offloading AI workloads)
* **Integrated Sensing and Communication** (e.g., navigation, movement detection)

Each task type varies in execution time and resource demand. The simulation uses a `time.Sleep` and 'ressource' mechanism based on latency data from 5 reference papers:

- Feng et al. (URLLC)
- Hazarika et al. (XR latency)  
- Ma et al. (IoT reliability) 
- Zhang et al. (sensing for AV)   
- Yuan et al. (3D holography + MEC)

Here an description of each types task:
- **Immersive communication**: This will provide users with a rich and interactive video experience, including interactions with machine interfaces. Typical use cases include communication for immersive XR, remote multi-sensory telepresence, and holographic communications. While 5G is theoretically also capable of supporting these use cases, the numbers supported by a single cell are very limited, so this is an extension of those capabilities.
- **Hyper-reliable and low-latency communication**: This will enhance communications in an industrial environment for full automation, control, and operation. These types of communications can help various applications such as machine interactions, emergency services, telemedicine, and monitoring for electrical power transmission and distribution. This is an extension of 5G’s URLLC.
- **Ubiquitous connectivity**: This will bridge the digital divide, especially for rural and remote areas. Typical use cases include IoT and mobile broadband communication but can also include access for hikers, farmers and others.
- **Massive communication**: This will connect many devices or sensors for various use cases and applications, including in smart cities, transportation, logistics, health, energy, environmental monitoring, and agriculture. This, too, is an extension of what has been defined in 5G. 
- **AI and communication**: This will support automated driving, autonomous collaboration between devices for medical assistance applications, offloading heavy computation operations across devices and networks, creation of and prediction with digital twins, and more.
- **Integrated sensing and communication**: This will improve applications and services requiring sensing capabilities, such as assisted navigation, activity detection, movement tracking, environmental monitoring, and sensing data on surroundings for AI and XR.


## 🎯 Offloading Algorithms and CoBRA Algorithm Overview

The smart contract implements five strategies:

* **Round Robin**: simple cyclic distribution
* **Random**: random eligible device selection
* **ECP**: prioritize edge servers
* **Energy-Aware**: select based on battery and compute resources (from Wang et al.)
* **COBRA Algorithm**: hybrid of uses three key metrics to guide its decision-making process:

  * **Task Cost Index (TCI):** Evaluates devices based on energy and computational costs to find the most efficient optio
  * **Reliability Index (RI):** Measures the reliability of devices based on their past performance, ensuring consistent task execution.
  * **Reputation Index (Rp):** Assesses UAVs and nodes based on their battery level and available resources, prioritizing devices that can reliably complete tasks.

The algorithm begins by calculating these indices for each eligible device. It then selects the device with the optimal combination of low TCI and high RI to execute the task. Throughout the process, the blockchain is used to monitor performance and update reputation scores, ensuring a fair and efficient task allocation while promoting collaboration within the network.

## 🔧 Smart Contract Functions

The smart contract maintains device and task ledgers, calculates and updates scores, and logs execution, with the goal of execut all the function below:

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
 
 Use `cobra-config.yaml` to configure Fabric connection.

## 🛠️ Go SDK Setup (Optional)

```bash
cd fabric-client/
go mod init fabric-client
go get github.com/hyperledger/fabric-sdk-go
go mod tidy
go mod vendor
go build simulation.go
./simulation
```

Edit `cobra-config.yaml` to match your Fabric parameters (channel, MSP ID, etc.).

## 🧰 Repository Structure

```
cobra-framework/
├── blockchain/      # Hyperledger setup
├── chaincode/       # Smart contracts (Go)
├── sdk/             # Go SDK interface
├── simulation/      # Simulation engine
├── results/         # CSV outputs + generate_graphes.py
├── docs/            # Deployment, troubleshooting
├── automation/      # CLI automation scripts
└── README.md        # This file
```

## 📋 Prerequisites

* Ubuntu 21.04
* Go (v1.18 or lower)
* Docker + Docker Compose
* Hyperledger Fabric v2.x

### Fabric Network Setup :
Ensure you have set up a Hyperledger Fabric network with multiple organizations and peers. You can follow the official Hyperledger Fabric documentation to set up the environment if not already done https://hyperledger-fabric.readthedocs.io/en/release-2.2/prereqs.html 

Or 

My tuto to set up a rapid network configure with already configure with a Hyperledger Blockchain with 5 Peer / 1 Orderer and based on Raft Consensus or with your preference [https://github.com/AxelOscar/Hyperledger-Blockchain-Fabric-Network-COBRA](https://github.com/AxelOscar/Hyperledger-Blockchain-Fabric-Network-COBRA)

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

## Deployment and Implementation in details
### Smart Contract Installation on you Blockchain:

To deploy the COBRA Framework on your Hyperledger Fabric network:
! All this command bellow work with my VM but you can addapt for your blockchain 

Prepare the environment:
```
export ORDERER_CA=/opt/gopath/fabric-samples/research-network/crypto-config/ordererOrganizations/research-network.com/orderers/orderer.research-network.com/msp/tlscacerts/tlsca.research-network.com-cert.pem
```

Edit and Save the Smart Contract:
```
vim /opt/gopath/src/chain/bto_chaincode/go/test_cobra/test_cobra.go
```

Package the Chaincode:
```
peer lifecycle chaincode package cobra_algo.tar.gz --path opt/gopath/src/chain/bto_chaincode/go/test_cobra/ --lang golang --label cobra_algo
```

Install the Chaincode (in all peer of you blockchain in my case 5 peer):
```
      cd /
      peer lifecycle chaincode install cobra_algo.tar.gz
      peer lifecycle chaincode queryinstalledè
```

Set the Package ID (in all organizations):
```
PACKAGE_ID=cobra_algo:<your-package-id>
```

Approve the Chaincode (in all organizations):
```
    peer lifecycle chaincode approveformyorg -o orderer.research-network.com:7050 --tls true --cafile $ORDERER_CA --channelID channelcoop --name cobra_algo --version 1 --init-required --package-id $PACKAGE_ID --sequence 1 --signature-policy "OR('Provider1MSP.peer', 'Provider2MSP.peer', 'Provider3MSP.peer', 'Provider4MSP.peer', 'Provider5MSP.peer')"
```

Check Commit Readiness:
```
      peer lifecycle chaincode checkcommitreadiness --channelID channelcoop --name cobra_algo --version 1 --sequence 1 --output json --init-required --signature-policy "OR('Provider1MSP.peer', 'Provider2MSP.peer', 'Provider3MSP.peer', 'Provider4MSP.peer', 'Provider5MSP.peer')"
```

Commit the Chaincode (on one peer):
```
      PRO1_CERTFILES_PEER=/opt/gopath/fabric-samples/research-network/crypto-config/peerOrganizations/pro1.research-network.com/peers/peer0.pro1.research-network.com/tls/ca.crt
      PRO2_CERTFILES_PEER=/opt/gopath/fabric-samples/research-network/crypto-config/peerOrganizations/pro2.research-network.com/peers/peer0.pro2.research-network.com/tls/ca.crt
      PRO3_CERTFILES_PEER=/opt/gopath/fabric-samples/research-network/crypto-config/peerOrganizations/pro3.research-network.com/peers/peer0.pro3.research-network.com/tls/ca.crt
      PRO4_CERTFILES_PEER=/opt/gopath/fabric-samples/research-network/crypto-config/peerOrganizations/pro4.research-network.com/peers/peer0.pro4.research-network.com/tls/ca.crt
      PRO5_CERTFILES_PEER=/opt/gopath/fabric-samples/research-network/crypto-config/peerOrganizations/pro5.research-network.com/peers/peer0.pro5.research-network.com/tls/ca.crt
      peer lifecycle chaincode commit -o orderer.research-network.com:7050 --tls true --cafile $ORDERER_CA --channelID channelcoop --name cobra_algo --peerAddresses peer0.pro1.research-network.com:7051 --tlsRootCertFiles $PRO1_CERTFILES_PEER --peerAddresses peer0.pro2.research-network.com:7051 --tlsRootCertFiles $PRO2_CERTFILES_PEER --peerAddresses peer0.pro3.research-network.com:7051 --tlsRootCertFiles $PRO3_CERTFILES_PEER --peerAddresses peer0.pro4.research-network.com:7051 --tlsRootCertFiles $PRO4_CERTFILES_PEER --peerAddresses peer0.pro5.research-network.com:7051 --tlsRootCertFiles $PRO5_CERTFILES_PEER --version 1 --sequence 1 --init-required --signature-policy "OR('Provider1MSP.peer', 'Provider2MSP.peer', 'Provider3MSP.peer', 'Provider4MSP.peer', 'Provider5MSP.peer')"
```

Query Committed Chaincode:
```
      peer lifecycle chaincode querycommitted --channelID channelcoop --name cobra_algo
```

    
Initialize the Ledger (on one peer):
```
      peer chaincode invoke -o orderer.research-network.com:7050 --tls true --cafile $ORDERER_CA -C channelcoop -n cobra_algo --peerAddresses peer0.pro1.research-network.com:7051 --tlsRootCertFiles $PRO1_CERTFILES_PEER --peerAddresses peer0.pro2.research-network.com:7051 --tlsRootCertFiles $PRO2_CERTFILES_PEER --peerAddresses peer0.pro3.research-network.com:7051 --tlsRootCertFiles $PRO3_CERTFILES_PEER --peerAddresses peer0.pro4.research-network.com:7051 --tlsRootCertFiles $PRO4_CERTFILES_PEER --peerAddresses peer0.pro5.research-network.com:7051 --tlsRootCertFiles $PRO5_CERTFILES_PEER --isInit -c '{"function":"initLedger","Args":[]}'
```

> [!TIP]
> In the case of modification of the smart contract, it will be necessary to reflect the modification on all the files, in the commands above the name is "cobra_algo" which is the one present in the sdk files, always be careful to use the correct SC and the sequence number for each modification the number is incremented

Now you have implement the SC in your Smart Contract, but now you have to use a SDK to communicate with the blockchain and so use your SC.

### Simulation Program and Creation of the Go SDK
The fist step in your user machine, is to install the good go version.
If you don't have a go version than 1.18 follow this step :
```
      sudo rm -rf /usr/local/go
      wget https://golang.org/dl/go1.17.5.linux-amd64.tar.gz
      sudo tar -C /usr/local -xzf go1.17.5.linux-amd64.tar.gz
      export PATH=$PATH:/usr/local/go/bin
      source ~/.bashrc  # OR source ~/.zshrc if you use zsh
      go version
      rm go1.17.5.linux-amd64.tar.gz
```

Build the Simulation:
```
mkdir fabric-client & cd fabric-client
```
      
  And copy inside all the contenu of the fabric_simulation_client_code
  In the folder you will retrieve 5 files, the most important is the cobra-config.yaml this files allow to connect your machine with blokckchain, after copy all the files do the command bellow, i will create the go environement
      
```
      go mod init fabric-client
      go get github.com/hyperledger/fabric-sdk-go
      go mod tidy
      go mod vendor
```

  After do juste the go build name_of the file

```
  go build Simulation.go
  ./Simulation
```

The simulation will generate 2,000 tasks, send them to the blockchain, and provide performance metrics about 2 Hours for each simulation with a model you can choose one of the 5 different model and modify the number of task and the proportion of task type.

> [!TIP]
> If of course you want this to work with your blockchain, you will need to modify your main config file to allow the connection with your blockchain and modify all the files with the correct information, in particular the use / name part of the channel and the SC
>````
>  // Initialize the SDK and channel client
>    sdk, channelClient, err := initSDKAndClient("cobra-config.yaml", "channelcoop", "Admin", "Provider1MSP")
> ````

## 📊 Example Results

Run `generate_graphes.py` to visualize:

* UAV battery depletion curves
* Task delay (Tx + Exec)
* Offloading distribution by algorithm

The different graph below show our the result of our framework, to have the same type of graphes use the generate_graphes python file

This graph the variation of the Battery after 1000 task send :


Here you can see the delay for task, this time delay includes the entire transaction time, also the time taken by the device to execute the task :

## 📖 References

Key research sources used for the reflexion of this model: 


