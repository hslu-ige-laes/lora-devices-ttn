---
layout: default
title: The Things Network
nav_order: 2
has_children: false
permalink: /docs/ttn
---

<img src="https://raw.githubusercontent.com/hslu-ige-laes/lora-devices-ttn/master/docs/ttn-logo.svg" width="128" align="right" class="inline"/>

# The Things Network
{: .no_toc }
The Things Network (TTN) is a global, community-driven and open-source LoRaWAN network. It provides an infrastructure for connecting own devices free of charge. 

---
## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## What is LoRaWAN 
LoRaWAN (Long Range Wide Area Network) is generally a wireless technology that allows devices such as sensors to send data over long distances. These devices are not directly connected to the Internet.
They send the data at regular intervals via the LoRaWAN to a so-called gateway. This gateway then forwards the data to the Internet.

LoRaWAN is perfect for connecting devices that need to send small amounts of data, like temperature readings, air quality information, or water level measurements. These devices can be placed anywhere, no internet connection or power supply is needed.
Normally these devices are battery powered and have a long lifetime.

---

## Network Components of a LoRaWAN
In a LoRaWAN, the setup involves the following components

- **Sensors/Devices/Nodes**<br>
  These are the end devices equipped with sensors that collect data, such as temperature, humidity, or motion. They are also called LoRa Nodes.
- **Gateway**<br>
  Gateways act as communication hubs in the network. They receive data from the devices over long distances and forward it to a central network server in the internet.
- **Network Server**<br>
  The network server manages the entire LoRaWAN network. It processes data received from gateways, deduplicates telegrams, manages device registrations, handles security, and routes data to the appropriate applications.
- **Application Server**<br>
  The application server is where data is sent after passing through the network server. Here, the data is processed, stored, and integrated with other systems or applications.
- **End User Application**<br>
  The end user application is for example a dashboard where the transmitted data gets processed and visualized. The data can also get transmitted to an automation system.

<img src="https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/ttn_components.PNG" width="700"><br>

The data flow is as follows
1. The sensor sends data
1. The gateway(s) in the receiving area receive the data and forward them to the network server
1. The network server validates, deduplicate and decrypts the data, determines the appropriate application, and sends it to the respective application server
1. The application server processes and stores the data, making it available for analysis or integration with other services
1. The End User application receives the data from the application server and may process and/or visualize it

---

## Whats special about TTN?
This public network is free of charge and built on the principle of shared infrastructure, meaning that users can benefit from existing gateways deployed by others in the community.
As long as there are TTN gateways within the reception range of your sensor device, you can utilize the network without the need to deploy your own gateway.

---

## Differences to other LoRaWAN networks
In Switzerland, for example, the company Swisscom offers a similar network, although there is a charge for it.
Here, Swisscom maintains the entire network infrastructure and you only have to integrate your own sensor devices. With the TTN, every individual can contribute to this infrastructure and 
install a gateway at home, for example. This gateway then not only forwards LoRa telegrams from one's own devices, but also from other users to the Internet.

---

## Security
LoRaWAN networks are designed with robust security measures, for example including encryption, device authentication and message integrity checks.
Overall LoRaWAN networks are considered secure for various IoT applications.
