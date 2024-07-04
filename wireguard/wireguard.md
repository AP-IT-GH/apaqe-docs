# WireGuard
## Why WireGuard
- Speed: WireGuard is generally faster than OpenVPN due to the efficient and modern cryptography it uses. This results in lower latency and higher throughput speeds.
- Efficiency: WireGuard is lightweight and requires fewer resources, leading to better performance, especially on less powerful hardware.
- Configuration: WireGuard is often easier to configure and manage. Using static IP addresses and fixed keys can be simpler than setting up OpenVPN’s extensive settings.
- Kernel Integration: WireGuard is integrated into the Linux kernel, making it easier to install and use on Linux systems.
- Encrypted point to point connection between VM and gateway.  

### Why we need WireGuard
We use WireGuard for set up a secure vpn tunnel between devices. In this case we use WireGuard for the communication between the gateways and a server/VM. 

## WGeasy 
### The Utility of WG-Easy in Our Project
WG-Easy is an invaluable tool for simplifying the management of WireGuard VPN servers.  In our project, we leverage the WG-Easy API to enhance our custom web user interface, resulting in a streamlined and user-friendly experience for managing our WireGuard.

Key Benefits of Using WG-Easy
- Automated Configuration: WG-Easy automates complex tasks such as key generation, user management, and client configuration. This reduces manual errors and simplifies the setup process.
- User Management: Through the API, we can easily add, edit, and remove users, ensuring efficient administration of VPN clients.
- API-Driven Approach: By utilizing WG-Easy’s API, we integrate its backend capabilities with our custom web user interface. 
- Enhanced Flexibility: The API provides flexibility in designing our interface to meet specific project requirements, enabling us to create a tailored user experience.

### Implementation in Our Project
In our project, we use WG-Easy’s API to manage and monitor our WireGuard VPN servers. Here’s how we integrate it:

- API Calls: We make API calls to WG-Easy to perform various tasks such as adding or removing clients. (see documentation (link na merge) API)
- Custom Web Interface: The data and functionality provided by WG-Easy’s API are integrated into our custom web interface. (see documentation (link na merge) Frontend)

# Project Implementation with WG-Easy
This section outlines how to integrate WG-Easy's API with your custom web interface for managing the WireGuard VPN server.

## Prerequisites:

- A Server with internet access (Server A) 
- A Client Device to connect to the VPN (Gateway)
- WG-Easy installed and configured on Server A
- Basic understanding of API calls and web development
- Open ports 51820 (udp) en 51821 (tcp)

## Steps:
1. Setup WG-easy:
Use the [documentation](https://github.com/wg-easy/wg-easy/blob/master/README.md) to run WG-Easy. 

Requirements
- A host with a kernel that supports WireGuard (all modern kernels).
- A host with Docker installed

We do not use the interface of wg easy directly because it raises some security issues. Moreover, it is more convenient to put this along in the general dashboard, so we have a coherent environment. This makes it easier to use. 

We use fastAPi to create routes that we can call at the frontend, which in turn calls the wg-easy api. Fast api works like a middleware. If we didn't do it this way we would run into problems with CORS. Besides, this is also clearer and easier to work with. 

2. Configure Server A (frontend dashboard):

Within the interface on the dashboard, create a new "Client" by just entering the name of the new gateway. Now you can see the IP address, the name and the gateway ID. 

Sidenote: It's not possible to enter the WireGuard interface directly
