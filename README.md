# Wireshark Packet Capture: DNS and TLS Analysis

**Author:** Ezekiel Arom\
**Project:** Cybersecurity & Ethical Hacking Internship --- Week 1\
**Task:** Wireshark Packet Capture\
**Platform:** Windows\
**Tool:** Wireshark\
**Target website:** <https://networkwalks.com>

------------------------------------------------------------------------

## Project Overview

This project documents a practical packet-capture exercise using
Wireshark on a Windows laptop. The objective was to observe DNS
resolution and inspect TLS handshake traffic while accessing
`networkwalks.com`.

Wireshark captures and decodes network packets, helping analysts
understand network communication, troubleshoot connectivity, and
investigate potentially suspicious activity. In this exercise, the
capture was used to identify the laptop's IP address, DNS server,
website IP address, TLS Server Name Indication (SNI), server port, and
TLS version.

> **Scope and authorization:** This exercise concerns traffic generated
> by the author's own device while visiting the specified public
> website. Only capture traffic on networks and devices for which you
> have permission.

## Objectives

-   Capture network traffic from the Windows laptop's Wi-Fi interface.
-   Observe the DNS query and response for `networkwalks.com`.
-   Identify the laptop's local IP address and DNS server IP address.
-   Inspect the TLS Client Hello for the server name.
-   Identify the destination/server port used for the secure connection.
-   Inspect the Server Hello and record the TLS version shown in the
    capture.

## Lab Environment

  -----------------------------------------------------------------------
  Component                           Details
  ----------------------------------- -----------------------------------
  Operating system                    Windows

  Packet analyzer                     Wireshark

  Network interface                   Wi-Fi

  Browser                             Google Chrome (Incognito window
                                      used in the documented procedure)

  Website                             `https://networkwalks.com`

  Traffic examined                    DNS and TLS handshake packets
  -----------------------------------------------------------------------

## Procedure

### 1. Clear the DNS cache

Close all browser windows. Open **Command Prompt** and run:

``` bat
ipconfig /flushdns
```
<img src="https://github.com/pjay77094-wq/Wk1-Ezekiel_Arom_Wireshark-Packet-Capture-1-v1-DNS-Analysis-/blob/f1508c53e40dd51a286499d13b906e76d54825ad/FlushDns.png"
     alt="Wireshark DNS response showing the website IP address"
     width="800">


### 2. Start a Wireshark capture

1.  Open Wireshark.
2.  On the start screen, identify the active network interface---usually
    **Wi-Fi**.
3.  Double-click the interface to begin capturing packets.

### 3. Generate traffic to the target website

1.  Open a new Chrome Incognito window with `Ctrl + Shift + N`.
2.  Visit <https://networkwalks.com>.
3.  Wait for the page to load fully.
4.  Return to Wireshark and stop the capture using the red **Stop**
    button.

### 4. Find the DNS query and response

Enter this display filter in Wireshark:

``` wireshark
dns.qry.name == "networkwalks.com"
```

Inspect the matching query and response:

-   In the **query** packet, the source is the client laptop and the
    destination is the DNS resolver.
-   In the **response** packet, expand **Domain Name System** and then
    **Answers**.
-   Read the returned **Address** field to identify the website IP
    address.

If no DNS packets appear, close the browser, flush the DNS cache again,
and repeat the capture. Browser Secure DNS/DoH settings can affect
whether a conventional DNS query is visible in the capture.

### 5. Inspect the TLS Client Hello

Use this display filter:

``` wireshark
tls.handshake.type == 1
```

Select the relevant Client Hello packet. Expand:

**Transport Layer Security → Handshake Protocol: Client Hello →
Extension: server_name**

Look for the SNI value `networkwalks.com`. Inspect the packet's TCP
details to identify the destination port.

> **Note:** The source exercise notes that Chrome may use QUIC/HTTP/3 on
> UDP port 443 for some connections. If the traffic is QUIC rather than
> a TCP-based TLS handshake, packet details and filters may differ.
> Confirm the protocol and transport fields in your own capture.

### 6. Inspect the Server Hello and TLS version

Use this display filter:

``` wireshark
tls.handshake.type == 2
```

Find the Server Hello associated with the target website and inspect the
Protocol column and decoded TLS details. Record the negotiated TLS
version indicated by the capture.

## Results

The following values are the results recorded in the supplied project
PDF. IP addresses and negotiated protocol details can vary by network,
DNS resolver, browser, and time; repeat the capture to verify them in
your own environment.

  Question                             Recorded result
  ------------------------------------ --------------------
  Laptop IP address                    `192.168.18.134`
  DNS server IP address                `192.168.18.1`
  `networkwalks.com` IP address        `192.232.216.135`
  TLS Client Hello server name (SNI)   `networkwalks.com`
  Secure server port                   `443`
  TLS version recorded                 `TLSv1.3`

## Screenshot Evidence

Add screenshots from your own Wireshark capture to the repository.
Remove or obscure unrelated personal information, internal hostnames,
and traffic not needed to demonstrate the task.

### 1. DNS response showing the website IP

Show the filtered DNS query/response and expand **Domain Name System →
Answers** so the returned address is visible.

![DNS response showing the website IP](screenshots/dns-response.png)

*Replace `screenshots/dns-response.png` with the path to your actual
screenshot.*

### 2. TLS Client Hello showing SNI

Show the Client Hello packet and expand the `server_name` extension so
`networkwalks.com` is visible.

![TLS Client Hello showing SNI](screenshots/tls-client-hello.png)

*Replace `screenshots/tls-client-hello.png` with the path to your actual
screenshot.*

### 3. Server Hello showing TLS version

Show the relevant Server Hello and the decoded TLS version in Wireshark.

![TLS Server Hello and version](screenshots/tls-server-hello.png)

*Replace `screenshots/tls-server-hello.png` with the path to your actual
screenshot.*

## Key Observations

-   DNS resolution maps a domain name to an IP address so a client can
    locate the server.
-   The DNS response's **Answers** section is where the returned address
    can be inspected.
-   TLS Client Hello metadata may expose the requested server name
    through SNI, depending on the protocol and configuration.
-   HTTPS commonly uses port `443`; the transport may be TCP or UDP
    depending on whether the connection uses TLS over TCP or
    QUIC/HTTP/3.
-   The recorded capture identified TLS 1.3. The exact version and
    packet visibility should be confirmed against the relevant handshake
    in the actual capture.

## Troubleshooting

  -----------------------------------------------------------------------
  Issue                               What to try
  ----------------------------------- -----------------------------------
  No DNS packets appear               Run `ipconfig /flushdns`, start a
                                      fresh capture, and reload the
                                      website.

  DNS query is not visible            Check whether browser Secure
                                      DNS/DoH is enabled; it can prevent
                                      ordinary DNS queries from
                                      appearing.

  No TLS Client Hello appears         Confirm the correct interface was
                                      captured, try a new connection, and
                                      check whether the browser used
                                      QUIC/HTTP/3.

  Filter returns no packets           Verify the website was loaded
                                      during the capture and that the
                                      display filter was entered
                                      correctly.

  Results differ from the table       Record the values from your own
                                      capture; DNS answers and network
                                      addresses may change.
  -----------------------------------------------------------------------

## Repository Structure

A simple repository layout could be:

``` text
wireshark-packet-capture/
├── README.md
└── screenshots/
    ├── dns-response.png
    ├── tls-client-hello.png
    └── tls-server-hello.png
```

Save the screenshots in the `screenshots/` directory and ensure the
image filenames match the links in this README.

## Conclusion

This exercise provided hands-on practice capturing Wi-Fi traffic and
using Wireshark display filters to examine DNS resolution and TLS
handshake metadata. The recorded results identified the local client and
DNS resolver addresses, the target website address, the TLS SNI, port
`443`, and TLS 1.3. The screenshots should be added to the repository to
provide visual evidence of the packet fields used to reach these
conclusions.

------------------------------------------------------------------------

**Project attribution:** Based on the *Wireshark Packet Capture* project
task supplied by Networkwalks. The procedure and recorded results above
reflect that source document; screenshots should be replaced with
evidence from the author's own capture.
