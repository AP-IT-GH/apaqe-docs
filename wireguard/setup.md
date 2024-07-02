# Project Implementation with WG-Easy
This section outlines how to integrate WG-Easy's API with your custom web interface for managing your WireGuard VPN server.

## Prerequisites:

- A Server with internet access (We'll refer to this as Server A)
- A Client Device to connect to the VPN (We'll refer to this as Client B)
- WG-Easy installed and configured on Server A (refer to WG-Easy documentation for installation)
- Basic understanding of API calls and web development

## Steps:
1. setup WG-easy:
Use the [documentation](https://github.com/wg-easy/wg-easy/blob/master/README.md) to run WG-Easy. 

Requirements
- A host with a kernel that supports WireGuard (all modern kernels).
- A host with Docker installed

We do not use the interface of wg easy directly because it raises some security issues. Moreover, it is more convenient to put this along in the general dashboard. 

We use fastAPi to create routes that we can call at the frontend, which in turn calls the wg-easy api. If we didn't do it this way we would run into problems with CORS. Besides, this is also clearer and easier to work with. 

2. Configure Server A (frontend dashboard):

Within the interface on the dashboard, create a new "Client" by just entering the name of the new gateway. Now you can see the IP address, the name and the gateway ID. 
