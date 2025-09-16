# Networks Capstone Project

This crash course is designed to provide a layered overview of computer networking, breaking down complex concepts into practical, easy-to-digest sections. Each contributor focuses on a specific part of the networking stack—from the physical media that carries signals, up through protocols that enable communication, all the way to security and design best practices.

The documentation is divided according to the OSI and TCP/IP models, ensuring that every layer is addressed in context. Along the way, examples, diagrams, and real-world analogies are included to make abstract ideas more concrete.

By the end, readers will have a holistic understanding of how networks function, how data moves across systems, and how to design stable, secure environments.

## Table of Contents

1. [Foundations (Physical + Data Link Layers)](#foundations-physical--data-link-layers)
2. [Network & Transport Layers](#network--transport-layers)
3. [Core Protocols](#core-protocols)
4. [Security & Design Considerations](#security--design-considerations)

## Foundations (Physical + Data Link Layers)

### Physical Layer Media: Copper, Fiber, and Wireless

The **physical layer** deals with the raw transmission of bits across a medium. Three primary media dominate: **copper cables, fiber optics, and wireless signals**.

-   **Copper (twisted pair, coaxial)** is inexpensive and widely deployed in LANs. It supports moderate bandwidth and is subject to electromagnetic interference and attenuation over distance. Typical Ethernet over copper supports up to 100m without repeaters.
-   **Fiber optics** offer very high bandwidth, long-distance transmission, and immunity to electromagnetic interference. They use light pulses rather than electrical signals, making them the backbone of modern high-speed networks.
-   **Wireless** media (Wi-Fi, Bluetooth, cellular) use radio waves and provide mobility, but face challenges like interference, limited spectrum availability, and shared medium contention.

**Bandwidth trade-offs** are evident: copper is cheap but distance-limited, fiber is expensive but offers enormous capacity, and wireless is convenient but less predictable in speed and reliability.

### Data Link & MAC Layer Basics

The **Data Link Layer** ensures reliable transmission of frames between nodes over the physical medium. It has two sublayers:

-   **Logical Link Control (LLC):** Provides error detection, flow control, and links higher layers with the physical medium.
-   **Medium Access Control (MAC):** Determines how multiple devices share a common medium. MAC protocols prevent collisions, manage access fairness, and encapsulate data into frames with MAC addresses for device identification.

The **MAC layer** is critical in shared environments like Ethernet and Wi-Fi. It uses rules (protocols) to decide who “speaks” on the medium and when, ensuring data integrity and minimizing collisions.

In shared mediums (like early Ethernet), **collisions** occur when multiple devices transmit simultaneously. **CSMA (Carrier Sense Multiple Access)** reduces this by making each device “listen” before sending:

-   **CSMA/CD (Collision Detection):** Used in Ethernet, devices sense the medium, transmit if idle, and stop if they detect overlapping signals (a collision). They then send a jam signal and retry after a random backoff time.
-   **CSMA/CA (Collision Avoidance):** Used in Wi-Fi, where collision detection is impractical. Instead, devices use **handshaking mechanisms** like RTS/CTS (Request to Send / Clear to Send) to avoid collisions, especially in hidden node scenarios.

These strategies maximize throughput but trade efficiency in high-traffic environments.

**Collision in Ethernet (Hub-based):**

```
A -----> data ----\
                   >--- COLLISION ---X
B -----> data ----/
```

#### MAC Layer (Details)

The **MAC layer** is the sublayer of the Data Link Layer that directly interacts with the physical medium and decides **when and how devices access the shared channel**. Its main goals are:

-   To prevent data collisions (or deal with them if unavoidable).
-   To fairly allocate the medium among multiple users.
-   To encapsulate network-layer packets into **frames** with proper headers (source/destination MAC addresses, control info, error checks).
-   To handle retransmissions if errors or collisions occur.

Think of the MAC layer as the **traffic controller** for devices on a shared road (the channel). Without it, multiple devices would transmit simultaneously, leading to chaos and wasted bandwidth.

#### Categories of MAC Protocols

**1. Contention-based Protocols (Random Access):**

-   Devices compete for the channel.
-   Collisions can occur, but protocols define how to detect or avoid them.
-   Efficient under low-to-moderate load, but performance degrades with high traffic.
    Examples:
-   **ALOHA / Slotted ALOHA**: Early wireless MAC protocol where devices transmit randomly. Collisions are resolved by retransmissions after random backoff.
-   **CSMA (Carrier Sense Multiple Access):** Devices listen before transmitting. Variants:

    -   **CSMA/CD (Collision Detection):** Ethernet; detects collisions and retries after random backoff.
    -   **CSMA/CA (Collision Avoidance):** Wi-Fi; avoids collisions using inter-frame spacing, random backoff, and optional RTS/CTS handshakes.

**2. Controlled Access Protocols:**

-   Devices take turns, coordinated by a centralized or distributed mechanism.
-   Collisions are avoided because access is scheduled or polled.
    Examples:
-   **Token Passing:** A special “token” circulates, and only the device holding the token can transmit (used in Token Ring, FDDI).
-   **Polling:** A central controller asks each device in turn if it wants to transmit.

**3. Channelization Protocols (Deterministic Access):**

-   The channel is divided into fixed portions (time, frequency, or codes), allocated to devices.
-   Eliminates collisions but can be inefficient if some slots go unused.
    Examples:
-   **FDMA (Frequency Division Multiple Access):** Each user gets a frequency band.
-   **TDMA (Time Division Multiple Access):** Each user gets a time slot.
-   **CDMA (Code Division Multiple Access):** Each user gets a unique code to spread signals across the same frequency.

##### Efficiency and Trade-offs

-   **Contention-based protocols** are flexible and scalable, but collisions waste bandwidth.
-   **Controlled access protocols** guarantee fairness and predictability, but introduce overhead (e.g., token passing delays).
-   **Channelization protocols** are collision-free and predictable but require careful planning and may waste capacity if some users are idle.

### LAN Limitations: Distance, Speed, and Collisions

Local Area Networks (LANs) face **physical and logical constraints**:

-   **Distance:** Copper Ethernet segments are limited (\~100m), while Wi-Fi coverage depends on signal strength and interference. Fiber extends LANs further but at higher cost.
-   **Speed:** Shared bandwidth can bottleneck performance. For example, in a hub-based Ethernet, all devices share the same collision domain.
-   **Collisions:** More devices in a shared medium increase collision probability, reducing efficiency. Modern networks mitigate this using switches, which isolate collision domains and provide full-duplex links.

### Address Resolution Protocol (ARP): Bridging MAC & IP

The **Address Resolution Protocol (ARP)** is a crucial protocol that operates between the **Data Link Layer (Layer 2)** and the **Network Layer (Layer 3)** of the OSI model. Its primary purpose is to map **logical IP addresses** (used for routing) to **physical MAC addresses** (used for local delivery). Since actual packet delivery on a LAN depends on MAC addresses, ARP acts as the translator that ensures packets reach the right device.

**Example:**

```
Host A:  "Who has 192.168.1.10?"  ---> [BROADCAST to LAN]
Host B:  "I have 192.168.1.10, my MAC is 00:1A:2B:3C:4D:5E" ---> [UNICAST reply]
```

#### How ARP Works

1. **ARP Request (Broadcast):**

    - When a host wants to communicate with another device on the same subnet, it needs the destination device’s MAC address.
    - If it doesn’t already have it cached, it sends an **ARP request frame** to the broadcast MAC address `FF:FF:FF:FF:FF:FF`, asking: _“Who has IP x.x.x.x?”_

2. **ARP Reply (Unicast):**

    - The device with that IP responds with an **ARP reply**, sent directly (unicast) to the requester: _“I am IP x.x.x.x, and my MAC is 00:1A:2B:3C:4D:5E.”_

3. **Caching:**

    - The sender stores this mapping in its **ARP table (cache)** to avoid repeated broadcasts.
    - Entries usually have a time-to-live (TTL), after which they expire and must be refreshed.

**Example Diagram: Normal ARP Resolution:**

```
Host A (192.168.1.5) → Broadcast: "Who has 192.168.1.10?"
Host B (192.168.1.10) → Unicast Reply: "MAC = 00:1A:2B:3C:4D:5E"
Host A updates ARP cache: [192.168.1.10 → 00:1A:2B:3C:4D:5E]
```

#### ARP Packet Structure

An ARP packet is encapsulated in an Ethernet frame and contains:

-   **Sender MAC address**
-   **Sender IP address**
-   **Target MAC address** (empty/unknown in ARP request)
-   **Target IP address**

This structure allows devices to both request and announce address bindings.

#### ARP in Practice

-   **Within a LAN:** ARP is used to resolve next-hop MAC addresses for local delivery.
-   **For remote destinations:** A device uses ARP to find the MAC address of the **default gateway (router)**, which forwards traffic outside the subnet.

Example:

-   Host A (192.168.1.5) wants to send to Host B (192.168.1.10).
-   Host A sends ARP request → Host B replies with its MAC.
-   If Host A instead wants to reach 8.8.8.8 (outside subnet), it sends ARP request for the **router’s IP** (e.g., 192.168.1.1), then forwards traffic via that gateway.

#### Security Issues with ARP

Since ARP lacks authentication, it’s vulnerable to attacks:

-   **ARP Spoofing/Poisoning:** An attacker sends false ARP replies to associate their MAC with another IP (e.g., the default gateway). This allows interception (Man-in-the-Middle) or denial of service.
-   **Mitigation:** Tools like **Dynamic ARP Inspection (DAI)** on switches, static ARP entries for critical systems, and encrypted higher-layer protocols (HTTPS, VPNs) reduce risks.

**Example Diagram: Attack Scenario (ARP Poisoning):**

```
Attacker: "I am 192.168.1.1 (Gateway), my MAC is 11:22:33:44:55:66"
Host A believes traffic for the gateway should go to attacker.
Attacker intercepts/forwards packets (Man-in-the-Middle).
```

## Network & Transport Layers

xyz

## Core Protocols

xyz

## Security & Design Considerations

xyz
