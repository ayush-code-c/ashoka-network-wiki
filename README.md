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

This section covers the essential protocols that act as the "brains" of the network, managing how devices find each other, get configured, and communicate efficiently. While the Network and Transport layers handle the raw data delivery, these protocols provide the critical services that make networking user-friendly and automated.

-----

### DNS (Domain Name System)

DNS is the **internet's phonebook**. It translates human-readable domain names (like `www.google.com`) into machine-readable IP addresses (like `142.250.196.196`). Without DNS, we would have to remember long strings of numbers to access any website.

#### How DNS Resolution Works (Recursive Query)

When you type a domain name into your browser, a multi-step process happens in the background to find the corresponding IP address.

1.  **Client Query**: Your computer first checks its own local cache. If the address isn't there, it asks its configured **DNS Resolver** (usually provided by your ISP or a public service like Google's `8.8.8.8`).
2.  **Resolver Asks Root**: The resolver, if it doesn't have the IP cached, asks one of the **Root Servers**. The root server doesn't know the IP but knows who manages the top-level domain (TLD), like `.com`. It replies, "I don't know, but ask the `.com` TLD server."
3.  **Resolver Asks TLD**: The resolver then asks the **TLD Name Server** for `.com`. The TLD server doesn't know the full IP but knows which server is authoritative for the `google.com` domain. It replies, "I don't know, but ask Google's authoritative name server."
4.  **Resolver Asks Authoritative Server**: Finally, the resolver asks Google's **Authoritative Name Server**. This server is the ultimate source of truth for the `google.com` domain and replies with the correct IP address.
5.  **Response & Caching**: The resolver sends this IP back to your computer and **caches** it for a set amount of time (called a TTL, or Time-To-Live) to speed up future requests.

#### Diagram: DNS Recursive Query Flow

```plaintext
              [ You ]
                 | 1. "What is www.google.com?"
                 v
+-----------------------------+     2. Asks Root Server --> 3. "Try .com TLD"
| Your Local/ISP DNS Resolver |<----------------------------------------------+
+-----------------------------+                                               |
                 | 4. Asks .com TLD Server --> 5. "Try google.com's server"    |
                 v                                                             |
+--------------------------------+     6. Asks Authoritative --> 7. "IP is X"  |
| Google's Authoritative Server  |---------------------------------------------+
+--------------------------------+
                 | 8. Resolver gives you IP "X"
                 v
              [ You now connect to IP "X" ]
```

-----

### DHCP (Dynamic Host Configuration Protocol)

DHCP is the protocol that **automatically assigns IP addresses** and other network settings to devices when they join a network. It's the reason you can connect to a Wi-Fi network and get online instantly without manually configuring your IP address, subnet mask, and default gateway.

#### How DHCP Works (The DORA Process)

DHCP uses a four-step process known as **DORA**:

1.  **Discover**: When a new device joins a LAN, it knows nothing about the network. It shouts out a **DHCP Discover** message to the broadcast address (`255.255.255.255`), asking, "Is there a DHCP server here that can give me an IP address?"
2.  **Offer**: Any DHCP server on the network that hears the broadcast can respond with a **DHCP Offer** message, sent directly to the device. This offer includes a proposed IP address, subnet mask, gateway IP, and a "lease" time (how long the device can use that IP).
3.  **Request**: The device receives one or more offers and chooses one. It then sends a **DHCP Request** message back (again, as a broadcast) to formally request the offered IP from the chosen server. This broadcast informs all other DHCP servers that it has made a choice.
4.  **Acknowledge**: The server that made the offer finalizes the process by sending a **DHCP Acknowledge (ACK)** message. This confirms the lease, and the device can now use the IP address.

#### Diagram: The DORA Process

```plaintext
+--------+                                          +-------------+
| Client |                                          | DHCP Server |
+--------+                                          +-------------+
    |                                                      |
    |  --- 1. Discover (Broadcast: "I need an IP!") --->   |
    |                                                      |
    |  <-- 2. Offer    (Unicast: "Here's 192.168.1.100") -- |
    |                                                      |
    |  --- 3. Request  (Broadcast: "I'll take that IP!") ->|
    |                                                      |
    |  <-- 4. Acknowledge (Unicast: "It's yours!") -------- |
    |                                                      |
```

-----

### Multicast & Broadcast

While most network traffic is **unicast** (one-to-one), broadcast and multicast are essential for one-to-many communication.

  * **Broadcast**: One-to-everyone. A broadcast packet is sent to a special address that every single device on the local network segment must process. It's like a public announcement in a large room—everyone has to stop and listen.

      * **Real-World Analogy**: A fire alarm in a building. It's non-selective and forces everyone to pay attention.
      * **Examples**: **ARP Requests** and **DHCP Discover** messages use broadcasts to find a specific device or service without knowing its address beforehand.

  * **Multicast**: One-to-many (but only to those who are interested). Devices can choose to "join" a multicast group. A single multicast packet is sent from a source and is only delivered by the network switches and routers to the members of that group.

      * **Real-World Analogy**: Streaming a live cricket match. The broadcaster sends out a single stream, and only the people who have tuned their devices to that specific stream (joined the multicast group) will receive it. This is far more efficient than sending a separate, identical stream to every single viewer (which would be multiple unicasts).
      * **Examples**: IPTV, online gaming, and stock market data feeds.

| Communication Type | Target | Analogy | Network Impact |
| :--- | :--- | :--- | :--- |
| **Unicast** | One specific device | A private phone call | Low |
| **Broadcast** | All devices on a LAN | A fire alarm | High (disrupts every device) |
| **Multicast** | A specific group of devices | A radio station broadcast | Medium (only affects subscribers) |

-----

### Neighbor Discovery Protocols

Neighbor Discovery protocols are used for **zero-configuration networking**. They allow devices like printers, smart TVs, and IoT gadgets to automatically announce their presence and services on a local network, making them easy to find and use.

#### How It Works

Instead of relying on a central server, devices use **multicast** messages to advertise themselves.

1.  **Announcement**: When a new network printer comes online, it sends a multicast message to a standardized address, saying, "Hi, I'm a printer at IP address `X`, and I support these printing services."
2.  **Listening**: Your computer or smartphone is constantly listening for these multicast announcements.
3.  **Discovery**: When you click "Add Printer," your computer already has a list of available printers it discovered through these announcements, with no manual IP configuration needed.

This is the magic behind features like Apple's AirPrint, Google's Chromecast discovery, and seeing shared media servers in your file explorer. Protocols like **mDNS** (multicast DNS) and **SSDP** (Simple Service Discovery Protocol) are the engines driving this process.

-----

### Common Ports & Services

Ports are used at the Transport Layer to identify specific applications or services running on a device. When data arrives at a device with an IP address, the port number tells the operating system which program should receive it (e.g., port 443 for the web browser, port 25 for the email client).

Here is a table of some of the most common ports and their associated services.

| Port | Protocol | Service | Description |
| :--- | :--- | :--- | :--- |
| **20/21** | TCP | **FTP** | File Transfer Protocol (for transferring files) |
| **22** | TCP | **SSH** | Secure Shell (for secure remote command-line access) |
| **25** | TCP | **SMTP** | Simple Mail Transfer Protocol (for sending email) |
| **53** | TCP/UDP | **DNS** | Domain Name System (for name resolution) |
| **67/68** | UDP | **DHCP** | Dynamic Host Configuration Protocol |
| **80** | TCP | **HTTP** | Hypertext Transfer Protocol (for unencrypted web traffic) |
| **123** | UDP | **NTP** | Network Time Protocol (for synchronizing clocks) |
| **443** | TCP | **HTTPS** | HTTP Secure (for encrypted, secure web traffic) |

-----

## Security & Design Considerations

xyz
