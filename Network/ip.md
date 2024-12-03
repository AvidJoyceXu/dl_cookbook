# Play with ip
## Steps to Route Traffic through Different Interfaces
Identify the Network Interfaces: First, you need to determine the names of your Ethernet and WiFi interfaces. Use the following commands to identify them:

To list all network interfaces:

```bash
ip link show
ifconfig
```

Commonly, your Ethernet interface might be named eth0 (or something similar), and your WiFi interface might be named wlan0 (or similar).

## Setup Routing Rules
- First, ensure that the Ethernet interface is used for the local network (SSH):
```bash
ip route add <cluster_ip>/32 dev eth0
```

- Next, ensure that the WiFi interface is used for internet traffic by adding the default route for internet access through the WiFi interface:
```
ip route add default via <wifi_gateway_ip> dev wlan0
```
Replace `<wifi_gateway_ip>` with the default gateway IP address of your WiFi network.

- Check Routing Table: You can check your routing table to verify the routes
```
ip route show
```
