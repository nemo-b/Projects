# 🛡️ Cybersecurity Tools: Core Tools & Skills to Learn

The goal of this section is to take basic notes on my learning adventure — documenting what I explore, practice, and understand over time. With some basic research, I learned that these are generally the most used tools for cybersecurity, so I hope this can become applicable knowledge later down the line.  

---
## 📂 Tools Index
- [x] 1. **Wireshark (Completed: Aug 25, 2025)**
- [x] 2. **Splunk** **(Completed: Jan 22, 2026)**
- [x] 3. **Nmap** **(Completed:: Jan 26, 2026)**
- [ ] 4. **Metasploit Framework**
- [ ] 5. **Burp Suite**
- [ ] 6. **Kali Linux Tools (General)**
- [ ] 7. **Security Onion**
- [ ] 8. **Snort / Suricata (IDS/IPS)**
- [ ] 9. **VMs & Sandbox Environments** **(Notes In Progress)**
- [ ] 10. **Python for Cybersecurity**
- [ ] 11. **Volatility (Memory Forensics)**
- [ ] 12. **GRC Tools (Governance, Risk, Compliance)**

---

## 🔬 Lab Notes Format (for each tool)

### 🧰 Tool: Wireshark ✅
*Completed: Aug 20, 2025*

**Purpose / Role:**  
WireShark is a network protocol analyzer that lets you capture and inspect packets traveling across a network. Its essential for network troubleshooting, detecting suspicious traffic, and understanding protocols at a deep level. 

**Core Skills to Learn (according to ChatGPT):**  
- [x] Install / Setup  
- [x] Basic Commands  
- [x] Common Use-Cases  
- [x] Using it on my network  

## Notes:
To learn Wireshark, I started with **YouTube tutorials**, and whenever I didn’t understand something I had just seen (or had a question the video didn’t answer), I asked ChatGPT for clarification.

**First video I used to learn is this:**  
[Learn Wireshark in 10 minutes - Wireshark Tutorial for Beginners - Vinsloev Academy](https://www.youtube.com/watch?v=lb1Dw0elw0Q&t=68s)

The video began with download instructions, but since I’m on Linux, I was able to quickly find Wireshark in the app center.

The first noticeable detail was the short list of different networks connected to my machine. Each one had small, updating graphs showing how much traffic was moving through that connection. One key point (at least for me) was that if I want to track things like _who is visiting which sites on my network_, I could connect via **Ethernet cable** to my router and track the Ethernet interface directly. Public Wi-Fi networks are generally encrypted from methods like this (as the video explained). Also, as a security best practice: never connect to open public Wi-Fi.

To start watching a network, you simply double-click one of the available interfaces. On my laptop, I tested this using `wlp1s0`. After connecting to an active network, I started seeing many packets (logs). They are clearly categorized at the top with columns like **No., Time, Source, Destination, Protocol…**. For my initial testing, I focused on the **Protocol** column.

This is where the video introduced filtering (though I had already started experimenting with this myself). At the top of the window there’s a filter bar: if you type in, for example, `http`, Wireshark only shows HTTP packets. Since HTTP is insecure, I didn’t see much of it on my machine, but I did find useful packets under protocols like **DNS**.

After I started seeing results, I wanted to understand how to “translate” what was on my screen — so I asked ChatGPT.

#### Default Wireshark Columns:
- **No. (Number)** → The order the packet was captured in
- **Time** → A timestamp of when the packet was seen
- **Source** → The sender’s address (IP, MAC, or hostname, depending on the layer)
- **Destination** → The receiver’s address
- **Protocol** → What “language” they’re speaking (TCP, UDP, HTTP, DNS, TLS…)
- **Length** → The size of the packet
- **Info** → A quick summary of what’s inside the packet

#### Filtering Issues:
One thing I found interesting — but couldn’t get working — was detailed filtering, specifically using `tcp contains youtube`. After troubleshooting with ChatGPT, I learned that the problem was encryption: since YouTube uses HTTPS, the word “youtube” doesn’t appear inside the packet contents. Instead, you need to look at **metadata** around the connection.

The most useful solution for me was:

`dns.qry.name contains "youtube"`

This worked perfectly!

#### ChatGPT’s Suggested Alternatives:

> 1. **Use DNS Queries (best method for domains)**
>     
>     - Filter: `dns.qry.name contains "youtube"`
>         
>     - Shows when a device asks a DNS server to resolve `youtube.com`.
>         
> 2. **Look at SNI (Server Name Indication) in TLS Handshake**
>     
>     - Filter: `tls.handshake.extensions_server_name contains "youtube"`
>         
>     - Works unless TLS 1.3 + ESNI hides it.
>         
> 3. **Filter by IP Ranges**
>     
>     - Example: `ip.addr == 142.250.0.0/15` (Google’s IP range).
>         
> 4. **Quick Check**
>     
>     - Use the simple `dns` filter to see every site your device asked for.
>         

#### Packet Bytes Pane:
While tinkering, I noticed another section in Wireshark — the raw data at the bottom of the screen. When I hovered over different parts, the left box highlighted the related section in plain English.

Later, I learned from ChatGPT that this section is called the **Packet Bytes Pane**. The right-side box was essentially a _translation_ of the raw packet data into something more human-readable.

This connects back to the video: when the instructor searched for `tcp contains youtube`, Wireshark was actually looking for the word “youtube” inside this **packet bytes** section.

#### The Three Panes in Wireshark
1. **Top Pane (Packet List Pane)**
    - Each row = one packet
    - Shows No., Time, Source, Destination, Protocol, Info
2. **Middle Pane (Packet Details Pane)**
    - Expands the packet into protocol fields (Ethernet, IP, TCP, HTTP, etc.)
    - Like a “translation guide” of the raw bytes
3. **Bottom Pane (Packet Bytes Pane)**
    - Shows the raw data as captured
    - **Left side** → Byte offset (positions in the packet)
    - **Middle column** → Hexadecimal values (actual bytes)
    - **Right column** → ASCII representation (human-readable characters, when possible)
### Packet Capture Example

<p align="center">
  <img src="images/Screenshot%20One.png" alt="Packet Example Image" width="500">
</p>

*Example packet capture used during traffic analysis.*



#### Plaintext Traffic Example:
The video also showed an example of capturing **unencrypted data**. The instructor visited a test site that used **HTTP** instead of HTTPS. He entered a login (`admin123`) on the site, and then used the filter:

`tcp contains admin123`

Sure enough, the username appeared in plaintext in the packets. This is a clear example of why HTTP is insecure — anyone capturing traffic could read the username or even the password.

##### Key Takeaways:
- Learned how to filter packets generally.
- Surprised by how much “background noise” is always on a home network.
- Still need to revisit details of different protocols (this part is confusing for me).


---
### 🧰 Tool: Splunk ✅
*Completed: Jan 22, 2026*

**Purpose / Role:** 
Splunk is a **SIEM (Security Information and Event Management)** platform used to collect, centralize, search, and visualize logs from multiple systems. In a SOC environment, Splunk helps analysts detect suspicious behavior, investigate incidents, and understand what is happening across an organization’s infrastructure from a single interface.

**Core Skills to Learn (according to ChatGPT):**  
- [x] Installation and initial setup
- [x] Understanding data ingestion (what data is coming in and from where)
- [x] Basic searching and filtering using SPL
- [x] Interpreting log fields (host, source, sourcetype, time)
- [x] Creating simple dashboards for visibility


**Learning Approach Summary: **
To learn Splunk, I intentionally combined **hands-on experimentation** with **guided explanations**:
- **Primary learning resource:**
    - YouTube walkthrough for visual understanding
        - [https://www.youtube.com/watch?v=3CiRs6WaWaU](https://www.youtube.com/watch?v=3CiRs6WaWaU)
- **Supplemental learning:**
    - ChatGPT, used to clarify concepts, terminology, and _why_ Splunk behaves the way it does (not to blindly copy steps)

My goal was not to rush into advanced detections, but to first understand how Splunk thinks about data.

## Notes:

#### Initial Setup & First Observations: 
I downloaded **Splunk Enterprise** from the official website and learned early that Splunk does not behave like a traditional desktop application. Instead of opening as a standalone app, Splunk runs as a **local web service**, accessed through a browser.

**Key observation:**  
Splunk operates as a server-style application, even on a single machine. This design makes sense because Splunk is meant to ingest data from many systems and provide centralized access via a web interface.

I also installed the **Splunk Universal Forwarder** on a Windows 11 system to forward logs into the main Splunk instance, allowing me to simulate a small multi-device environment.

Data Inputs:
- Learned how to configure log ingestion via:  
    **Settings → Data → Data Inputs**
- Enabled ingestion of:
    - Application logs
    - Security logs
    - System logs
- Noted that inputs can be scoped to local data or forwarded data depending on configuration

This reinforced the idea that Splunk does _nothing_ until data is explicitly told to flow in.

#### Searching & Filtering (SPL Basics):
- Located the main search interface inside the **Search & Reporting** app
- Learned that searches can be refined by:
    - Host
    - Source
    - Sourcetype
    - Time range
- Discovered that highlighting a field and selecting **“Add to search”** automatically builds valid SPL

Example learning moment:
- Filtering by a specific host allowed me to isolate logs from a single machine instead of drowning in all system noise.

#### Table Views & Fields
- Converted raw search results into **table views**
- Used field selection to:
    - Add relevant fields
    - Remove unnecessary default metadata
- Learned how to clean tables using SPL commands like:

	`| fields - _bkt, _cd`

This was what i noted as the difference between “raw logs” and “readable investigation data.”

#### Dashboards: 
- Created a custom dashboard using:
    - **Dashboard Studio**
    - **Grid layout**
- Added a **table visualization** driven by a saved SPL search
- Observed how dashboards allow:
    - Faster visibility
    - Repeatable analysis
    - SOC-style “at a glance” monitoring

This helped connect Splunk usage to real SOC workflows.

#### Problems Encountered & Resolutions
- **Confusion locating the search bar:**  
    → Resolved by learning Splunk’s app-based layout (Search & Reporting app)
- **Information overload from logs:**  
    → Resolved by narrowing scope using host and field-based searches
- **Initial misunderstanding of forwarders:**  
    → Learned that forwarders are not required for local data, but are critical for multi-system visibility

Each issue reinforced the importance of understanding _data flow_ before analysis.

##### Key Takeaways:
- Splunk is fundamentally about **data ingestion first, analysis second**
- Logs are useless without filtering and context
- Dashboards are not cosmetic — they represent analyst workflows
- Splunk feels overwhelming until you understand how fields and searches interact
- This tool rewards structured thinking more than memorization

## Open Questions / Next Steps (For future)

- Build a **consistent** baseline of “normal” activity, mostly for dashboards
- Learn more SPL beyond basic filtering
- Begin correlating logs across multiple devices
- Transition from familiarization to detection-focused use cases

---

**Core Skills to Learn (according to ChatGPT):**  
- [x] Installation and initial setup
- [x] Understanding data ingestion (what data is coming in and from where)
- [x] Basic searching and filtering using SPL
- [x] Interpreting log fields (host, source, sourcetype, time)
- [x] Creating simple dashboards for visibility


**Learning Approach Summary: **
To learn Splunk, I intentionally combined **hands-on experimentation** with **guided explanations**:
- **Primary learning resource:**
    - YouTube walkthrough for visual understanding
        - [https://www.youtube.com/watch?v=3CiRs6WaWaU](https://www.youtube.com/watch?v=3CiRs6WaWaU)
- **Supplemental learning:**
    - ChatGPT, used to clarify concepts, terminology, and _why_ Splunk behaves the way it does (not to blindly copy steps)

My goal was not to rush into advanced detections, but to first understand how Splunk thinks about data.

## Notes:

#### Initial Setup & First Observations: 
I downloaded **Splunk Enterprise** from the official website and learned early that Splunk does not behave like a traditional desktop application. Instead of opening as a standalone app, Splunk runs as a **local web service**, accessed through a browser.

**Key observation:**  
Splunk operates as a server-style application, even on a single machine. This design makes sense because Splunk is meant to ingest data from many systems and provide centralized access via a web interface.

I also installed the **Splunk Universal Forwarder** on a Windows 11 system to forward logs into the main Splunk instance, allowing me to simulate a small multi-device environment.

Data Inputs:
- Learned how to configure log ingestion via:  
    **Settings → Data → Data Inputs**
- Enabled ingestion of:
    - Application logs
    - Security logs
    - System logs
- Noted that inputs can be scoped to local data or forwarded data depending on configuration

This reinforced the idea that Splunk does _nothing_ until data is explicitly told to flow in.

#### Searching & Filtering (SPL Basics):
- Located the main search interface inside the **Search & Reporting** app
- Learned that searches can be refined by:
    - Host
    - Source
    - Sourcetype
    - Time range
- Discovered that highlighting a field and selecting **“Add to search”** automatically builds valid SPL

Example learning moment:
- Filtering by a specific host allowed me to isolate logs from a single machine instead of drowning in all system noise.

#### Table Views & Fields
- Converted raw search results into **table views**
- Used field selection to:
    - Add relevant fields
    - Remove unnecessary default metadata
- Learned how to clean tables using SPL commands like:

	`| fields - _bkt, _cd`

This was what i noted as the difference between “raw logs” and “readable investigation data.”

#### Dashboards: 
- Created a custom dashboard using:
    - **Dashboard Studio**
    - **Grid layout**
- Added a **table visualization** driven by a saved SPL search
- Observed how dashboards allow:
    - Faster visibility
    - Repeatable analysis
    - SOC-style “at a glance” monitoring

This helped connect Splunk usage to real SOC workflows.

#### Problems Encountered & Resolutions
- **Confusion locating the search bar:**  
    → Resolved by learning Splunk’s app-based layout (Search & Reporting app)
- **Information overload from logs:**  
    → Resolved by narrowing scope using host and field-based searches
- **Initial misunderstanding of forwarders:**  
    → Learned that forwarders are not required for local data, but are critical for multi-system visibility

Each issue reinforced the importance of understanding _data flow_ before analysis.

##### Key Takeaways:
- Splunk is fundamentally about **data ingestion first, analysis second**
- Logs are useless without filtering and context
- Dashboards are not cosmetic — they represent analyst workflows
- Splunk feels overwhelming until you understand how fields and searches interact
- This tool rewards structured thinking more than memorization

## Open Questions / Next Steps (For future)

- Build a **consistent** baseline of “normal” activity, mostly for dashboards
- Learn more SPL beyond basic filtering
- Begin correlating logs across multiple devices
- Transition from familiarization to detection-focused use cases

---
### 🧰 Tool: Nmap ✅
*Completed: Jan 26, 2026*

**Purpose / Role:**  
Nmap is a free, open-source tool used to discover hosts and services on a computer network by sending specially crafted packets and analyzing the responses to identify open ports, operating systems, and potential security vulnerabilities. 

**Core Skills to Learn (according to ChatGPT):**  
- [x] Install / Setup  
- [x] Basic Commands  
- [x] Common Use-Cases  
- [x] Using it on my network  

## Notes:

#### **1. Tool Familiarization & Objective**

To establish a baseline for the SOC Home Lab, I utilized **Nmap (via the Zenmap GUI)** to perform network discovery. The primary objective was to map the local network topology and identify active services that require monitoring within the SIEM (Splunk).
- **Learning Resource:** [Zenmap Lab Tutorial - Nmap/Zenmap Basics](https://www.youtube.com/watch?v=ayx8luaHk2c)
- **Methodology:** Initial testing was conducted against the `scanme.nmap.org` sandbox to verify Nmap output formats and service detection capabilities without exposing local infrastructure during the learning phase.
#### **2. Service Enumeration & Security Observations**

During testing, I observed a significant delta in open ports between the tutorial environment and live targets.
- **Discovery:** Identified nearly 1,000 scanned ports on the test target, with a high density of "Open" vs. "Closed/Filtered" states.
- **SOC Insight:** A high number of open ports increases the **Attack Surface**. In a production environment, this would trigger a "Hardening" task to close any non-essential services.

#### **3. Topology Mapping & Subnet Discovery**

I successfully executed a subnet scan (e.g., `192.168.1.0/24`) to generate a visual **Network Topology Map**.
- **Process:** Used CIDR notation to define the scan range, identifying all interconnected nodes (VMs, host machines, and gateway).
- **Data Sanitization (OPSEC):** For documentation purposes, all screenshots of the network topology have been sanitized. Public IP addresses and sensitive internal MAC addresses were redacted to maintain **Operations Security (OPSEC)** while still demonstrating network architectural knowledge.

📸 Example Visuals
<p align="center">
  <img src="images/Nmap%20Output%20Example%201.png" alt="Nmap Output Example 1" width="500"> 
</p>

<p align="center">
  <img src="images/Nmap%20Output%20Example%202.png" alt="Nmap Output Example 1" width="500"> 
</p>

*Example of Nmap Output.*

