# 📦 Reliable File Transfer Protocol over UDP

Implementation of a reliable file transfer system over UDP, featuring **Stop-and-Wait** and **Selective Repeat** protocols. Developed as an academic project for the Computer Networks course at the University of Buenos Aires (UBA).

---

## 🚀 About the Project

This project implements a client-server architecture that provides reliable file transfer over UDP.

It features an application-level three-way handshake for connection establishment and supports testing in virtual network environments using **Mininet** under adverse network conditions, including packet loss and IP fragmentation.

## 🛠️ Tech Stack

- **Programming Language:** Python
- **Transport Protocol:** UDP
- **Reliability Protocols:** Stop-and-Wait, Selective Repeat
- **Network Simulation:** Mininet, `netem`, iperf
- **Network Analysis:** Wireshark
- **Version Control and Packaging:** Git, pip
- **Testing:** Python `unittest`

---

## ⚙️ Installation and Setup

### 1. Navigate to the project root directory

Open a terminal and navigate to the project's root directory.

### 2. Install the project in editable mode

```bash
pip install -e .
```

### 3. Run the tests

```bash
python3 -m test.run [-vv] [-vq]
```

Logging modes:

- **Default:** Normal logging mode.
- `-vv`: Verbose logging mode.
- `-vq`: Quiet logging mode.

### 4. Run the application locally

Start the server:

```bash
python3 ./src/start-server.py \
  -H 0.0.0.0 \
  -p 11111 \
  -s ./serverfiles \
  -v
```

Upload a file to the server:

```bash
python3 ./src/upload.py \
  -H 0.0.0.0 \
  -p 11111 \
  -s /home/damaris/clientfiles/prueba5M.txt \
  -n file_udp.txt \
  -v
```

Download a file from the server:

```bash
python3 ./src/download.py \
  -H 0.0.0.0 \
  -p 11111 \
  -d /home/damaris/clientfiles/prueba5Mdownload.txt \
  -n file_udp.txt \
  -v
```

---

## 🌐 Virtual Network Testing with Mininet

### 1. Start the virtual topology

Run the following command from the project directory containing `mytopo.py`:

```bash
sudo mn --custom mytopo.py --topo mytopo
```

### 2. Start the server and client

The following examples assume that `h1` runs the server and `h2` runs the client, with the server reachable at `10.0.0.1`.

#### Stop-and-Wait

Start the server on `h1`:

```bash
mininet> h1 python3 ./src/start-server.py \
  -H 10.0.0.1 -p 11111 -s ./serverfiles -v \
  > logfile 2> errorlogfile &
```

Upload a file from `h2`:

```bash
mininet> h2 python3 ./src/upload.py \
  -H 10.0.0.1 -p 11111 \
  -s /home/damaris/clientfiles/prueba5M.txt \
  -n file_udp.txt -v \
  > logupload 2> errorlogupload
```

Download a file from `h2`:

```bash
mininet> h2 python3 ./src/download.py \
  -H 10.0.0.1 -p 11111 \
  -d /home/damaris/clientfiles/prueba5Mdownload.txt \
  -n file_udp.txt -v \
  > logdownload 2> errordownload
```

#### Selective Repeat

Start the server on `h1` with Selective Repeat enabled:

```bash
mininet> h1 python3 ./src/start-server.py \
  -H 10.0.0.1 -p 11111 -s ./serverfiles -v -r sr \
  > logfile 2> errorlogfile &
```

Upload a file from `h2`:

```bash
mininet> h2 python3 ./src/upload.py \
  -H 10.0.0.1 -p 11111 \
  -s /home/damaris/clientfiles/prueba5M.txt \
  -n file_udp.txt -v -r sr \
  > logupload 2> errorlogupload
```

Download a file from `h2`:

```bash
mininet> h2 python3 ./src/download.py \
  -H 10.0.0.1 -p 11111 \
  -d /home/damaris/clientfiles/prueba5Mdownload.txt \
  -n file_udp.txt -v -r sr \
  > logdownload 2> errordownload
```

### 3. Verify network initialization

Inspect the virtual network topology:

```bash
mininet> dump
```

### 4. Simulate packet loss

Configure a **10% packet loss rate** on the specified switch interface:

```bash
mininet> sh tc qdisc add dev s3-eth2 root netem loss 10%
```

Verify connectivity using `ping` between the hosts.

### 5. Modify switch MTUs

Inspect network interfaces:

```bash
mininet> sh ifconfig
```

Set the MTU of a switch interface to 1000 bytes:

```bash
mininet> sh ifconfig s2-eth2 mtu 1000
```

### 6. Generate network traffic with iperf

Use `iperf` to generate traffic and investigate packet fragmentation under different MTU configurations.

#### UDP traffic

Start a UDP server on `h2`:

```bash
mininet> h2 iperf -s -u &
```

Generate UDP traffic from `h1` using a 2000-byte datagram payload:

```bash
mininet> h1 iperf -c h2 -u -b 10M -l 2000 -t 5
```

#### TCP traffic

Start a TCP server on `h2`:

```bash
mininet> h2 iperf -s &
```

Generate TCP traffic from `h1` with a specified maximum segment size:

```bash
mininet> h1 iperf -c h2 -b 10M -M 1400 -t 5
```

**Note:** Fragmentation behavior depends on the effective MTU, packet headers, and network configuration. A UDP payload larger than the path MTU may result in IP fragmentation when fragmentation is permitted. TCP segment sizes are handled differently because TCP adjusts segment sizes to the path MTU.

---

## 🎯 Key Features

- Reliable file transfer over UDP.
- Stop-and-Wait and Selective Repeat protocol implementations.
- Client-server communication architecture.
- Application-level three-way handshake for connection establishment.
- Automated tests using Python's `unittest` framework.
- Network emulation with Mininet and `netem`.
- Testing under packet loss and different MTU configurations.
- Network traffic analysis using Wireshark and iperf.

---

**Academic Project** — Computer Networks, University of Buenos Aires (UBA).