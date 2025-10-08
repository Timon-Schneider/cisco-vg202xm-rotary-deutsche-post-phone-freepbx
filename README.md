# Cisco VG202XM Rotary Phone Integration with FreePBX

This repository contains a minimal working configuration for connecting vintage rotary phones (like the Deutsche Post rotary phone) to FreePBX using a Cisco VG202XM Voice Gateway.

## Background Story

This configuration is the result of a journey to connect a classic Deutsche Post rotary phone to a modern VoIP system. After spending an evening with chatbots and ending up with a convoluted configuration full of unnecessary settings, I dedicated another evening to cleaning it up. The result is this minimal, working configuration that gets the job done without the bloat.

## Configuration

First, enter privileged EXEC mode and then configuration mode:
```cisco
enable
configure terminal
```

Then enter the following configuration:
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
voice-port 0/0
 timeouts interdigit 3
!
dial-peer voice 100 voip
 destination-pattern .T
 session protocol sipv2
 session target ipv4:<FREEPBX_IP>
 voice-class codec 1
!
dial-peer voice 101 voip
 session protocol sipv2
 incoming called-number <EXTENSION>
 voice-class codec 1
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

After entering the configuration, save it to startup-config:
```cisco
write memory
```

## Configuration Guide

Replace the following placeholders in the configuration:
- `<FREEPBX_IP>`: Your FreePBX server IP address
- `<EXTENSION>`: The extension number you want to assign to the rotary phone
- `<YOUR_PASSWORD>`: The password for your extension (plain text)

## Timeout Setting

The configuration includes a custom interdigit timeout of 3 seconds (`timeouts interdigit 3`). The default timeout is 10 seconds, which can feel quite long when using a rotary phone. You can adjust this value to your preference - lower values mean you need to dial faster, higher values give you more time between digits.

## Wiring Guide for Deutsche Post Rotary Phone

### Original Phone Wiring Colors
The phone originally comes with an AS4 connector and four wires:
- White (Position 1)
- Brown (Position 2)
- Green (Position 3)
- Yellow (Position 4)

### Converting to RJ11
1. Remove the AS4 connector
2. You only need two wires for the conversion:
   - Brown wire from phone → Brown wire of RJ11 (Position 2)
   - White wire from phone → Green wire of RJ11 (Position 3)
3. The green and yellow wires from the phone are not needed

### Important Notes
- Looking down at the RJ11 connector when it's plugged into the Cisco device, the brown and white (now connected to green) wires should be the middle pair
- You do not need to open the phone or modify any internal wiring
- This configuration ensures both voice and rotary dial functionality work correctly

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
- Custom interdigit timeout for better rotary dial experience

## Contributing

Feel free to submit issues and enhancement requests!
