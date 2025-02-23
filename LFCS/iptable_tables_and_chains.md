================================== IP TABLE ==========================================

             Incoming Packet
                    │
                    ▼
          ┌──────────────────┐
          │    raw (PREROUTING)   │  [1️⃣ First Rule Check]
          └──────────────────┘
                    │
                    ▼
       ┌──────────────────┐
       │  mangle (PREROUTING) │  [2️⃣ Modify QoS, TTL, etc.]
       └──────────────────┘
                    │
                    ▼
       ┌──────────────────┐
       │    nat (PREROUTING)    │  [3️⃣ DNAT Happens Here]
       └──────────────────┘
                    │
                    ▼
           Routing Decision
      ┌────────────────────┐
      │ Local or Forwarded? │
      └────────────────────┘
                    │
  ┌──────────────────┴─────────────────┐
  │                                     │
  ▼                                     ▼
 Local Process                        Forward to another machine
  │                                     │
  ▼                                     ▼
  ┌──────────────────┐                 ┌──────────────────┐
  │    raw (INPUT)       │                 │   mangle (FORWARD) │
  └──────────────────┘                 └──────────────────┘
  ▼                                     ▼
  ┌──────────────────┐                 ┌──────────────────┐
  │  mangle (INPUT)    │                 │     filter (FORWARD)   │  [4️⃣ Firewall for Forwarded Pkts]
  └──────────────────┘                 └──────────────────┘
  ▼
  ┌──────────────────┐
  │  filter (INPUT)      │  [5️⃣ Firewall for Local System]
  └──────────────────┘
  ▼
  Processed by local app (e.g., Web Server)

   --- OR ---

  If a response packet is generated:
  ▼
  ┌──────────────────┐
  │  raw (OUTPUT)       │
  └──────────────────┘
  ▼
  ┌──────────────────┐
  │ mangle (OUTPUT)   │
  └──────────────────┘
  ▼
  ┌──────────────────┐
  │  nat (OUTPUT)       │  [6️⃣ SNAT for Local Generated Traffic]
  └──────────────────┘
  ▼
  ┌──────────────────┐
  │  filter (OUTPUT)    │  [7️⃣ Firewall for Outgoing Packets]
  └──────────────────┘
  ▼
  ┌──────────────────┐
  │  nat (POSTROUTING) │  [8️⃣ SNAT / MASQUERADE]
  └──────────────────┘
  ▼
 Outgoing Packet Sent to Network

---

## **Breakdown of Tables and Their Purpose**
| Table      | Purpose |
|------------|---------|
| **raw**    | Used to **bypass connection tracking** (seen first in `PREROUTING` and `OUTPUT`). |
| **mangle** | Used for **modifying packet headers** (QoS, TTL, etc.). |
| **nat**    | Used for **address translation** (DNAT, SNAT, MASQUERADE). |
| **filter** | The **main firewall table** (handles `INPUT`, `FORWARD`, `OUTPUT`). |

---

## **Practical Example**
Imagine a **packet from 192.168.1.8 to 192.168.1.10 on port 9999**.

1️⃣ **PREROUTING (`raw`)**  
   - Connection tracking check (if needed).  

2️⃣ **PREROUTING (`mangle`)**  
   - Apply QoS, TTL changes (if defined).  

3️⃣ **PREROUTING (`nat`)**  
   - If **DNAT** is configured, the destination IP/port changes here.  
   - If **you want to drop traffic, use `mangle` instead of `nat`** (since `nat` doesn't support `DROP`).  

4️⃣ **Routing Decision:**  
   - If **destined for local machine** → goes to `INPUT` chain.  
   - If **going to another machine** → goes to `FORWARD` chain.  

5️⃣ **INPUT (`filter`)**  
   - If `iptables` rules block the packet here, it’s dropped.  

6️⃣ **Process packet in local application** (e.g., web server, SSH, etc.).  

7️⃣ **Outgoing response follows `OUTPUT`, `POSTROUTING`** and applies **SNAT if needed**.  

---

## **Key Takeaways**
✅ **Use `raw` or `mangle` to drop packets at PREROUTING.**  
✅ **Use `nat` for DNAT/SNAT but not for dropping traffic.**  
✅ **Use `filter` for actual firewall rules (`INPUT`, `FORWARD`, `OUTPUT`).**  

This structure ensures you understand where to place **DNAT, SNAT, DROP, and ACCEPT** rules. Let me know if you need an example rule setup! 🚀
