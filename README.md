
# 🔥 WKT12 ADVANCED EXIT NODE GUIDE

Using a Mobile Device as a Tailscale Exit Node on a LAN

A complete deep‑dive into routing, NAT, DNS, MagicDNS, diagnostics, and benchmarking.


• Advanced architecture
• Routing + NAT
• DNS + MagicDNS
• Ping diagnostics
• Troubleshooting
• Benchmarking
• Guided Links embedded throughout
---

---



# WKT12 Advanced Exit Node Guide: Study Compendium
This study guide provides a structured review of the technical architecture, routing mechanisms, and diagnostic procedures required to use a mobile device as a Tailscale exit node. It is designed to facilitate a deep understanding of NAT traversal, DNS behavior, and performance benchmarking within a Tailscale network.

Part 1: Short-Answer Quiz

Instructions:  Answer the following questions in 2–3 sentences, based on the provided technical documentation.
What three simultaneous roles does a mobile device perform when acting as a Tailscale exit node?

Describe the specific routing path of a packet from a client to the internet when using a mobile exit node.
What is the sequence of the DNS chain when a client uses a mobile exit node?
Under what specific conditions should MagicDNS be enabled?

What does "Double NAT" mean in the context of this architecture, and how is it handled?

Which firewall requirements must the phone satisfy to function correctly as an exit node?

What are the expected "Correct Setup" results when performing ping diagnostics on the exit node?

If a diagnostic test shows high latency (20–200 ms), what are the most likely technical causes?

What do the specific metrics of "Minimum Latency" and "Packet Loss" represent in a diagnostic context?

In a benchmarking scenario, what does the combination of low latency and low throughput typically indicate?

 Part 2: Quiz Answer Key
 
Roles:  A mobile device acting as an exit node functions as a WireGuard peer, a NAT gateway, and a LAN egress router. These roles allow it to manage encrypted tunnel traffic while simultaneously routing that traffic to the local network and the broader internet.


Routing Path:  Traffic travels from the client via a WireGuard tunnel to the phone's Tailscale IP. From there, it undergoes NAT and forwarding to the phone’s LAN IP, moves to the local router, and is finally sent to the internet.


DNS Chain:  The DNS request begins at the client and is sent to the mobile exit node (100.64.10.5). It then progresses to the local router (10.0.0.1) before finally reaching the upstream resolver.
Enabling MagicDNS:  MagicDNS should be enabled when the user needs Tailnet hostname resolution, automatic search domains, or encrypted DNS over WireGuard. It is also required for split DNS for internal zones and when using HTTPS tailnet certificates.


Double NAT:  Double NAT occurs because traffic is translated first at the phone (from the Tailscale IP to the LAN IP) and then again at the router (from the LAN IP to a Public IP). While this creates two layers of translation, WireGuard is designed to handle this configuration cleanly and maintain stability.


Firewall Requirements:  The phone must allow forwarding between the tailscale0 and wlan0 interfaces and support NAT masquerading. Additionally, the firewall must permit return traffic flowing from the LAN back through the phone to the Tailscale network.
Ping Results:  A correct setup should yield an average latency between 0.3 and 1.0 ms, with a minimum of 0.2 ms and a maximum of less than 2 ms. 

Most importantly, there should be 0% packet loss to ensure a stable connection.
High Latency Causes:  High latency is typically caused by the connection being routed through a DERP relay rather than a direct path. Other contributing factors include weak Wi-Fi signals, CPU throttling on the mobile device, or iOS power-saving measures.


Metric Interpretation:  Minimum latency represents the best-case path or the most efficient possible connection speed. Packet loss is considered the most important diagnostic metric, as it indicates fundamental instability in the tunnel or network interference.

Benchmark Interpretation:  If diagnostics show low latency but low throughput, the bottleneck is usually the phone's CPU. This indicates the processor cannot handle the encryption and routing demands of higher speeds, even though the connection path itself is clean.

Part 3: Essay Questions
Instructions:  Use the provided documentation to develop comprehensive responses to the following prompts.

The Impact of Hardware Constraints on Exit Node Performance:  Analyze how mobile-specific limitations—such as CPU power, Wi-Fi throughput, and battery management—affect the performance of a Tailscale exit node compared to a wired alternative.

Diagnostic Methodology for Network Instability:  Detail a step-by-step diagnostic plan for a user experiencing "Bad" network performance (as defined by the benchmark table), explaining which tools (ping, iperf3, dig) to use and what the results indicate.


The Role of MagicDNS in Tailnet Architecture:  Discuss the advantages and disadvantages of MagicDNS, specifically addressing why a network administrator might choose to disable it in favor of tools like Pi-hole or custom DNS servers.
Loopback Behavior and LAN Stability:

Explain the phenomenon of loopback behavior when an exit node is on the same LAN as the client and evaluate why this behavior is considered expected and stable rather than a routing error.

NAT and Forwarding Mechanics:  Compare the flow of traffic through the phone’s NAT and the router’s NAT. Explain the necessity of NAT masquerading and interface forwarding in maintaining a transparent connection for the client.

Part 4: Glossary of Key Terms
Term,Definition

DERP Relay,"A relay used by Tailscale to facilitate connections when a direct WireGuard path cannot be established, often resulting in higher latency."

Double NAT,A network configuration where two separate NAT processes occur—first at the exit node and then at the local router—before traffic reaches the public internet.

iperf3,A benchmarking tool used to measure the maximum achievable throughput (bandwidth) of the exit node.

Loopback Behavior,"A scenario where traffic leaves a device, hits the local router, and returns to the same device before being processed through the Tailscale tunnel."

MagicDNS,"A Tailscale feature that provides automatic hostname resolution, search domains, and encrypted DNS within a tailnet."

MTU Mismatch,A configuration error regarding the Maximum Transmission Unit size that can lead to packet loss or connection issues.

NAT Masquerading,"A process that allows the exit node to mask the source IP of client traffic with its own LAN IP, enabling proper routing back from the local network."

Split DNS,A DNS configuration that allows different resolution rules for internal network zones versus public internet queries.

tailscale0,The virtual network interface created by Tailscale on a device to handle tailnet traffic.
WireGuard,"The underlying VPN protocol used by Tailscale to provide secure, encrypted communication between peers."

