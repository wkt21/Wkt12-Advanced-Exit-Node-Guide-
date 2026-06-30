1. Architecture Overview

A mobile device acting as a Tailscale exit node performs three simultaneous roles:

• WireGuard peer
• NAT gateway
• LAN egress router


Generic addressing example:

• Phone LAN IP: 10.0.0.25
• Router LAN IP: 10.0.0.1
• Phone Tailscale IP: 100.64.10.5
• Client Tailscale IP: 100.64.10.20


---

2. Routing Path (Advanced)

Client (100.64.10.20)
   ↓ WireGuard Tunnel
Phone Exit Node (100.64.10.5)
   ↓ NAT + Forwarding
Phone LAN (10.0.0.25)
   ↓
Router (10.0.0.1)
   ↓
Internet


This path confirms:

• Direct WireGuard connection
• No DERP relay
• Clean NAT traversal
• No asymmetric routing


---

3. DNS Behavior & MagicDNS

MagicDNS provides:

• Tailnet hostname resolution
• Automatic search domains
• Encrypted DNS over WireGuard
• Split DNS for internal zones


DNS Chain When Using a Mobile Exit Node

Client → 100.64.10.5 → 10.0.0.1 → Upstream Resolver


MagicDNS should be enabled when:

• Using exit nodes
• Using split DNS
• Accessing devices by hostname
• Using HTTPS tailnet certificates


MagicDNS should be disabled when:

• Testing raw DNS behavior
• Using Pi‑hole / AdGuard Home
• Using custom DNS servers


-m

4. NAT & Firewall Behavior

Phone NAT

SRC: 100.64.10.20 → 10.0.0.25


Router NAT

SRC: 10.0.0.25 → Public IP


This is double NAT, but WireGuard handles it cleanly.

Firewall Requirements

The phone must allow:

• Forwarding between tailscale0 and wlan0
• NAT masquerading
• Return traffic from LAN → phone → Tailscale


Tailscale configures these automatically.


5. Loopback Behavior

When the exit node is on the same LAN:

• Traffic leaves the phone
• Hits the router
• Returns to the phone
• Then goes back through Tailscale


This is expected and stable.

---

6. Performance Characteristics

Expected Speeds

• 150–300 Mbps: modern phones through WireGuard
• 50–150 Mbps: typical mobile exit node throughput


Bottlenecks

• Phone CPU
• Wi‑Fi throughput
• Double NAT
• Battery drain




7. Advanced Ping Diagnostics

Ping validates:

• WireGuard tunnel stability
• Direct vs DERP routing
• LAN interference
• NAT traversal
• Device forwarding performance


Expected Results (Correct Setup)

avg: 0.3–1.0 ms
min: 0.2 ms
max: < 2 ms
loss: 0%


Metric Interpretation

• Average Latency — Tunnel quality
• Minimum Latency — Best‑case path
• Maximum Latency — Detects spikes
• Packet Loss — Most important metric


Diagnostic Table

Test	Expected	Meaning	
Ping exit node	0–1 ms	Direct WireGuard path	
Ping router	1–3 ms	LAN stable	
Ping 1.1.1.1	10–40 ms	WAN latency	
Ping domain	10–60 ms	DNS + WAN	




8.  Troubleshooting Latency, Packet Loss & MagicDNS

High latency (20–200 ms)

• DERP relay
• Weak Wi‑Fi
• CPU throttling
• iOS power saving


Packet loss

• MTU mismatch
• Wi‑Fi interference
• Bufferbloat
• Radio power saving


DNS failures

• MagicDNS disabled
• Split DNS misconfigured
• Router DNS blocking
• Exit node not forwarding DNS




Troubleshooting Checklist

• Check Direct vs DERP
• Check MTU
• Check Wi‑Fi Quality
• Check DNS Forwarding



9. Exit Node Benchmarking

1. Latency Benchmark

Ping exit node → router → WAN
Run latency benchmark

2. Throughput Benchmark

iperf3 -c 100.64.10.5


Expected:

• 50–150 Mbps (mobile exit node)
• 150–300 Mbps (wired exit node)


3. DNS Benchmark

dig example.com @100.64.10.5


Expected:

• < 20 ms
• No SERVFAIL
• No timeouts


4. Stability Benchmark

ping -c 1000 100.64.10.5


Expected:

• 0% loss
• < 2 ms jitter


---

10. Benchmark Interpretation

• Low latency + low throughput → Phone CPU bottleneck
• High throughput + latency spikes → Wi‑Fi interference
• Slow DNS + fast ping → Router DNS slow
• Everything slow → Exit node overheating




11. Benchmark Table


Test
Good
Warning
Bad
Latency
< 1 ms
1–10 ms
    > 20 ms	
Packet Loss
>0.3%
0.1–1%
   > 1%	
Throughput
>100 Mbps
50–100 Mbps 
  < 50 Mbp
DNS
< 20 ms
20–50 ms  
> 50 ms

	            	            	               
	 
  		    	
Study Guide

            	     	
