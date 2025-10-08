# Cisco VG202XM Rotary Phone Integration with FreePBX

This repository contains a minimal working configuration for connecting vintage rotary phones (like the Deutsche Post rotary phone) to FreePBX using a Cisco VG202XM Voice Gateway.

## Background Story

This configuration is the result of a journey to connect a classic Deutsche Post rotary phone to a modern VoIP system. After spending an evening with chatbots and ending up with a convoluted configuration full of unnecessary settings, I dedicated another evening to cleaning it up. The result is this minimal, working configuration that gets the job done without the bloat.

## Configuration

```cisco
version 15.7
!
hostname VG202XM
!
voice class codec 1
 codec preference 1 g711ulaw
!
interface FastEthernet0/0
 ip address DHCP
 no shutdown
!
interface FastEthernet0/1
 shutdown
!
dial-peer voice 100 voip
 destination-pattern .T
 session protocol sipv2
 session target ipv4:<FREEPBX_IP>
!
dial-peer voice 101 voip
 session protocol sipv2
 incoming called-number <EXTENSION>
!
dial-peer voice 1 pots
 destination-pattern <EXTENSION>
 port 0/0
!
sip-ua
 authentication username <EXTENSION> password <YOUR_PASSWORD>
 registrar ipv4:<FREEPBX_IP>:5060
!
end
```

## Configuration Guide

Replace the following placeholders in the configuration:
- `<FREEPBX_IP>`: Your FreePBX server IP address
- `<EXTENSION>`: The extension number you want to assign to the rotary phone
- `<YOUR_PASSWORD>`: The password for your extension (plain text)

## Notes

1. This configuration uses the g711ulaw codec by default. You can add more codecs in the `voice class codec 1` section or configure them directly in FreePBX.
2. The configuration assumes DHCP for IP addressing. If you need a static IP, modify the FastEthernet0/0 interface configuration accordingly.
3. The second Ethernet port (FastEthernet0/1) is disabled by default.

## Features

- Minimal working configuration
- Single codec setup (easily expandable)
- DHCP-based networking
- Basic SIP authentication
- Support for incoming and outgoing calls

## Contributing

Feel free to submit issues and enhancement requests!
