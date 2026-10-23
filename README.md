# Wireshark Network Forensics: Easy As 123

## Overview

This project documents a network traffic analysis and incident investigation exercise based on the **Easy As 123** traffic analysis challenge published by Malware-Traffic-Analysis.net.

Using **Wireshark on Kali Linux**, I investigated a provided packet capture (PCAP) file to analyse suspected NetSupport Manager Remote Access Trojan (RAT) activity. The investigation focused on identifying the compromised Windows workstation, extracting host and user information, documenting Indicators of Compromise (IOCs) and recommending defensive measures.

This project helped me develop practical skills in network forensics, packet analysis, incident response and threat investigation.

## Objectives

- Analyse network traffic to investigate suspected malicious activity.
- Identify the internal host communicating with a suspected command-and-control (C2) server.
- Extract host information from network protocols.
- Investigate authentication traffic to identify the associated domain user.
- Identify network-based and host-based Indicators of Compromise.
- Document investigation findings and recommend defensive measures.

## Tools and Technologies

- **Wireshark**: Network packet capture analysis and protocol inspection.
- **Kali Linux**: Operating system used for the investigation.
- **PCAP**: Packet capture file containing the network traffic under investigation.

## Investigation Scenario

The exercise simulated a Security Operations Center (SOC) investigation following alerts associated with NetSupport Manager RAT activity.

The provided packet capture contained outbound connections from an internal Windows workstation to a suspected external command-and-control server over TCP port 443.

My investigation used Wireshark display filters, TCP conversations, protocol fields, and packet-byte searches to trace the suspicious traffic and identify the affected host.

## Investigation Methodology

### 1. Identifying the Compromised Host

I used the following display filter to isolate traffic involving the suspected C2 server:

```text
ip.addr == 45.131.214.85
```

I then examined **Statistics → Conversations → TCP** to identify the internal IP address communicating with the external server.

### 2. Extracting the MAC Address

I inspected the Ethernet II header of packets originating from the identified internal host to retrieve its source MAC address.

### 3. Identifying the Hostname

I used the following display filter to examine NetBIOS Name Service traffic:

```text
nbns
```

By inspecting the relevant packet fields and matching the source IP address, I identified the hostname associated with the compromised workstation.

### 4. Identifying the Domain User Account

I filtered Kerberos traffic using:

```text
kerberos
```

I examined authentication responses and the `CNameString` field to identify the associated Active Directory user account.

### 5. Recovering the User's Full Name

I used Wireshark's **Find Packet** functionality to search packet bytes for a partial name. I then examined the relevant Security Account Manager Remote (SAMR) protocol response to locate the user's full display name.

### 6. Documenting Indicators of Compromise

I documented the suspicious external IP address, destination port, internal host details, and evidence of outbound RAT communication.

## Key Findings

The investigation recovered the following information from the provided packet capture:

|        Category         |        Finding         |
|-------------------------|------------------------|
| Suspected malware       | NetSupport Manager RAT |
| External C2 IP          | `45.131.214[.]85`      |
| Destination port        | TCP 443                |
| Compromised host IP     | `10.2.28[.]88`         |
| Hostname                | `DESKTOP-TEYQ2NR`      |
| Active Directory domain | `easyas123[.]tech`     |
| Domain user account     | `brolf`                |

The investigation also recovered the user's full display name from SAMR traffic. This demonstrated how protocol inspection can reveal sensitive identity information from a network capture.

*Note: The findings above relate to the supplied training exercise and do not indicate a real-world compromise.*

## Indicators of Compromise (IOCs)

The principal network indicator identified was the external IP address associated with suspected command-and-control communication.

Additional investigation evidence included:

- Outbound communication over TCP port 443.
- The internal IP address of the suspected compromised workstation.
- Hostname and domain account information extracted from network traffic.
- NetSupport Manager RAT-related activity described in the exercise.

These indicators can support further investigation, threat hunting, and the development of network detection rules. They should be validated against current threat intelligence before being used in a live environment.

## Defensive Recommendations

Based on the investigation, I identified several defensive measures:

- Investigate and restrict confirmed malicious connections to known C2 infrastructure.
- Monitor outbound traffic for suspicious protocols using commonly permitted ports.
- Use network intrusion detection and prevention systems to identify suspicious traffic patterns.
- Restrict unauthorised installation and use of remote administration software.
- Apply network segmentation and appropriate access controls to reduce exposure of sensitive internal protocols.
- Investigate the affected endpoint for persistence, additional malware and possible lateral movement.

## Skills Demonstrated

- Network traffic analysis using Wireshark.
- PCAP investigation and packet inspection.
- Incident response and evidence documentation.
- Network protocol analysis, including NBNS, Kerberos, and SAMR.
- Identification of Indicators of Compromise.
- Host and user attribution from network traffic.
- Cybersecurity reporting and defensive recommendations.

## References

1. Duncan, B. *Traffic Analysis Exercise: Easy As 123*. Malware-Traffic-Analysis.net.  
   https://www.malware-traffic-analysis.net/2026/02/28/index.html

2. Wireshark Foundation. *Wireshark User's Guide*.  
   https://www.wireshark.org/docs/wsug_html/

3. Microsoft. *[MS-SAMR]: Security Account Manager (SAM) Remote Protocol Specification*.  
   https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-samr/

4. NetSupport Ltd. *NetSupport Manager*.  
   https://www.netsupportsoftware.com/netsupport-manager/

## Disclaimer

This project was conducted for educational and cybersecurity training purposes using a provided traffic analysis exercise. All analysis was performed on the supplied packet capture. The findings should be interpreted within the context of the exercise.

Watch the full demonstration here: https://youtu.be/Nwn4syaL8bQ
