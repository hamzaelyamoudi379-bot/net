*This project has been created as part of the 42 curriculum by hel-yamo.*

# NetPractice

## Description

NetPractice is a project about the basics of computer networking.

The goal is simple: I get a small network diagram that does **not** work. I must fix it. To do this, I change the IP addresses, the subnet masks, and the routing tables until all the goals at the top of the page are green (OK).

The networks are **fake** (simulated). They run in a web page in my browser, so I cannot break a real network.

There are **10 levels**. Each level is a little harder than the one before. In each level I learned something new:

- how an IP address and a subnet mask work together,
- how devices in the same network talk to each other,
- how a router connects two different networks,
- how a default gateway and a routing table tell a packet where to go.

At the end, I export one configuration file per level and I put the 10 files in my Git repository.

---

## Instructions

### 1. Run the training interface

1. Download the file from the project page.
2. Extract the files in any folder.
3. In this folder, run the script:

```bash
./run.sh
```

The script starts a small web server and opens the browser on the right page.

If `run.sh` does not work, I can do it by hand:

```bash
python3 -m http.server 49242
```

Then I open this address in my browser: `http://localhost:49242` (I can change the port number if it is already used).

### 2. Use the interface

1. Write my login (`hel-yamo`) in the field. **This is very important**, because the project uses my login to build my own configuration.
2. Click on **Start!** (tab *Training*).
3. Read the goals at the top of the page.
4. Change only the **white (unshaded) fields**: IP addresses, masks, and routes.
5. Click **Check again** to test my answer.
6. Read the logs at the bottom of the page. They tell me what is wrong (for example: a missing gateway or a bad IP).
7. When the level is OK, click **Get my config** to download the file.
8. Click **Next level**.

> Warning: I must click **Get my config** *before* going to the next level. If I forget, I must come back and solve it again.

The tab *Evaluation* gives a random configuration. I use it to train for the defense.

### 3. Submission requirements

- I must submit **10 exported configuration files** (one per level).
- I must put them at the **root** of my Git repository.
- This `README.md` must also be at the root.
- Only the work inside my repository is checked during the defense.
- I must double-check the names of my files.

### 4. The defense

During the defense I must solve **3 random levels** in a limited time. I cannot use external tools. A very simple calculator like `bc` is tolerated, but nothing more. So I must understand how to calculate subnets by myself.

---

## Network concepts (my notes)

### What is a network?

A network is a group of devices (computers, phones, printers, servers...) that are connected together so they can **share data**.

### Why do we use networks?

- To share information and files.
- To use the Internet (websites, mail, videos, games).
- To share things like a printer or a server.
- To talk with other people (chat, calls).
- To work together, even if we are far away.

Without networks, every computer would be alone.

### IP address

An IP address is the **address of a device** in a network. It is like the address of a house: it tells where to send the data.

An IPv4 address has 4 numbers from 0 to 255, separated by dots. Example: `192.168.1.10`.
In fact, it is 32 bits (4 bytes). Example: `192.168.1.10` = `11000000.10101000.00000001.00001010`.

**Private IP addresses** (used inside a home or a company, not on the Internet):

| Range | Mask |
|---|---|
| `10.0.0.0` - `10.255.255.255` | `/8` |
| `172.16.0.0` - `172.31.255.255` | `/12` |
| `192.168.0.0` - `192.168.255.255` | `/16` |

**Other special addresses:**

- `127.0.0.1` = *localhost* (my own computer).
- `0.0.0.0` = "any address" / "default route".
- Network address = first address of a network (all host bits are 0). It cannot be used for a device.
- Broadcast address = last address of a network (all host bits are 1). It cannot be used for a device.

### Subnet mask

The subnet mask tells which part of the IP is the **network** and which part is the **host** (the device).

Example: `255.255.255.0` is the same as `/24`.
It means: the first 24 bits are the network, the last 8 bits are for the hosts.

| CIDR | Mask | Total addresses | Usable hosts |
|---|---|---|---|
| `/24` | `255.255.255.0` | 256 | 254 |
| `/25` | `255.255.255.128` | 128 | 126 |
| `/26` | `255.255.255.192` | 64 | 62 |
| `/27` | `255.255.255.224` | 32 | 30 |
| `/28` | `255.255.255.240` | 16 | 14 |
| `/29` | `255.255.255.248` | 8 | 6 |
| `/30` | `255.255.255.252` | 4 | 2 |

Usable hosts = 2^(host bits) - 2 (we remove the network address and the broadcast address).

**Example:** `192.168.1.77/26`

- Block size = 64 (because the mask is `.192`, 256 - 192 = 64).
- Blocks: `0-63`, `64-127`, `128-191`, `192-255`.
- 77 is in the block `64-127`.
- Network address: `192.168.1.64`
- Broadcast address: `192.168.1.127`
- Usable hosts: `192.168.1.65` to `192.168.1.126`.

**Rule I learned:** two devices can talk directly only if they are in the **same network** (same network address with the same mask). If not, they need a router.

### MAC address

A MAC address is the **physical address** of a network card. It is made by the manufacturer. It has 48 bits, written in hexadecimal. Example: `00:1A:2B:3C:4D:5E`.

- IP address = can change, works at the network level (like a postal address).
- MAC address = fixed on the card, works inside the local network (like the name on the door).

To find the MAC address that goes with an IP address in the local network, the computer uses the **ARP** protocol ("Who has this IP?").

### Switch

A switch connects many devices **in the same local network** (LAN). It works with **MAC addresses** (OSI layer 2).

When a frame arrives, the switch looks at the destination MAC address and sends it only to the right port. It is smarter than an old *hub*, which sends data to everybody.

### MAC table (switch table)

The MAC table (also called CAM table) is a list inside the switch. It says: **"this MAC address is on this port"**.

The switch builds it alone:

1. A frame arrives on port 3 with source MAC `AA`. The switch writes: `AA -> port 3`.
2. If the destination MAC is in the table, the switch sends the frame only to that port.
3. If the destination is not known, the switch sends the frame to all other ports (flooding), and learns the answer later.

| MAC address | Port |
|---|---|
| `AA:AA:AA:AA:AA:AA` | 1 |
| `BB:BB:BB:BB:BB:BB` | 2 |
| `CC:CC:CC:CC:CC:CC` | 3 |

### Router

A router connects **different networks** together (for example my home network and the Internet). It works with **IP addresses** (OSI layer 3).

A router has **one IP address on each network** it is connected to (one per interface). When a packet arrives, the router looks at the destination IP and uses its routing table to choose where to send it next.

### Default gateway

The default gateway is the **router IP address** that a device uses when the destination is **not in its own network**.

Example: my PC is `192.168.1.10/24`, the router is `192.168.1.1`.
If I want to reach `8.8.8.8`, my PC knows it is not in `192.168.1.0/24`, so it sends the packet to `192.168.1.1` (the gateway).

**Important:** the gateway must be in the **same network** as the device. If not, it cannot work.

### Routing table

The routing table is a list of rules inside a router (or a host). It says: **"to go to this network, send the packet to this next hop"**.

In NetPractice, a route looks like this: `destination/mask => gateway`.

| Destination | Gateway | Meaning |
|---|---|---|
| `192.168.2.0/24` | `10.0.0.2` | To reach the network `192.168.2.0/24`, send to `10.0.0.2` |
| `0.0.0.0/0` | `10.0.0.1` | **Default route**: everything else goes to `10.0.0.1` |

Rules I learned:

- The router chooses the **most precise** route (the longest mask) that matches.
- `0.0.0.0/0` matches everything, so it is used last.
- The gateway (next hop) must be reachable directly, in the same network as one interface of the router.
- If no route matches, the log says: *destination does not match any route*.

### TCP

TCP (Transmission Control Protocol) is a **reliable** protocol (OSI layer 4).

- It makes a **connection** first (the "3-way handshake": SYN, SYN-ACK, ACK).
- It checks that all data arrives, in the right order.
- If a part is lost, it sends it again.
- It is a bit slower, but safe.
- Used for: websites (HTTP/HTTPS), email, SSH, file transfer.

### UDP

UDP (User Datagram Protocol) is a **fast** protocol without connection (OSI layer 4).

- It just sends the data. No handshake.
- It does not check if the data arrived. No resend.
- It is faster and lighter, but not reliable.
- Used for: video calls, online games, live streaming, DNS.

| | TCP | UDP |
|---|---|---|
| Connection | Yes | No |
| Reliable | Yes | No |
| Order of data | Kept | Not kept |
| Speed | Slower | Faster |
| Example | Web, email | Games, video calls |

### OSI model

The OSI model has 7 layers. It helps to understand how data goes through a network.

| # | Layer | Example |
|---|---|---|
| 7 | Application | HTTP, DNS, SSH |
| 6 | Presentation | Encryption, formats |
| 5 | Session | Open and close sessions |
| 4 | Transport | **TCP, UDP** |
| 3 | Network | **IP, router** |
| 2 | Data link | **MAC address, switch** |
| 1 | Physical | Cables, Wi-Fi, signals |

In this project I work mostly with layer 3 (IP addresses, routers) and I need layer 2 (switches, MAC) to understand how a local network works.

### How a packet travels (simple example)

PC A (`192.168.1.10/24`) sends data to PC B (`192.168.2.20/24`):

1. A sees that B is **not** in its network.
2. A sends the packet to its **default gateway** (the router).
3. The router looks at its **routing table** and finds the network of B.
4. The router sends the packet out of the right interface.
5. B receives the packet.

If both PCs were in the same network, A would talk to B directly (through a switch, using MAC addresses) and no router would be needed.

---

## My method to solve the levels

1. Read the goal: who must talk to who?
2. For each link, check that both IP addresses are in the **same network** (same mask).
3. Check that no IP is a **network address** or a **broadcast address**.
4. Check that no two devices have the **same IP**.
5. Check that every gateway is in the **same network** as the interface that uses it.
6. Check the routing tables (use `0.0.0.0/0` for a default route when it is possible).
7. Read the logs when it still fails.

---

## Resources

### Classic references

- RFC 791 - Internet Protocol (IP)
- RFC 793 - Transmission Control Protocol (TCP)
- RFC 768 - User Datagram Protocol (UDP)
- RFC 1918 - Address Allocation for Private Internets
- RFC 4632 - CIDR (Classless Inter-Domain Routing)
- RFC 826 - Address Resolution Protocol (ARP)
- Wikipedia pages: "IPv4", "Subnetwork", "Default gateway", "Routing table", "OSI model", "MAC address", "Network switch"
- Cloudflare Learning Center: articles about TCP/UDP, IP address, and the OSI model
- Videos and tutorials about subnetting and CIDR on YouTube

### Networking concepts studied

- TCP/IP addressing (IPv4, private addresses, network and broadcast address)
- Subnet masks and CIDR notation
- Default gateways
- Routers and switches
- Routing tables and default routes
- MAC addresses, ARP, and the MAC table of a switch
- TCP and UDP
- OSI layers

### How I used AI

I used AI as a helper to learn, not to do the project for me.

- **Used for:** explaining the networking definitions in simple words (TCP, UDP, switch, router, gateway, MAC table, routing table), and helping me write and organize this `README.md` in good English.
- **Not used for:** solving the levels. I solved the 10 levels by myself in the training interface, and I checked my calculations (subnets, ranges, masks) by hand.
- I read and checked everything the AI gave me, and I compared it with other sources and with my peers. I can explain every part of this README and of my configurations.

---

## Submission

- 10 exported configuration files (one per level) are placed at the **root** of this repository.
- Login used in the interface: `hel-yamo`.
- This `README.md` is at the root too.
