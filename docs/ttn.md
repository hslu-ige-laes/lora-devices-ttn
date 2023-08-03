---
layout: default
title: The Things Network
nav_order: 2
has_children: true
permalink: /docs/ttn
---

# The Things Network
## What is LoRaWAN 
LoRaWAN (Long Range Wide Area Network) is generally a wireless technology that allows devices such as sensors to send data over long distances. These devices are not directly connected to the Internet.
They send the data at regular intervals via the LoRaWAN to a so-called gateway. This gateway then forwards the data to the Internet.

LoRaWAN is perfect for connecting devices that need to send small amounts of data, like temperature readings, air quality information, or water level measurements. These devices can be placed anywhere, no internet connection or power supply is needed.
Normally these devices are battery powered and have a long lifetime.

## Network Components of LoRaWAN
In a LoRaWAN network, the setup involves the following components

- Sensors/Devices
  These are the end devices equipped with sensors that collect data, such as temperature, humidity, or motion.
- Gateway
  Gateways act as communication hubs in the network. They receive data from the IoT devices over long distances and forward it to a central network server in the internet.
- Network Server
  The network server manages the entire LoRaWAN network. It processes data received from gateways, deduplicates telegrams, manages device registrations, handles security, and routes data to the appropriate applications.
- Application Server
  The application server is where data is sent after passing through the network server. Here, the data is processed, stored, and integrated with other systems or applications.
- End User Application
  The end user application is for example a dashboard where the transmitted data gets processed and visualized. The data can also get transmitted to a automation system which can the data as it would come from a conentional cable connected sensor.

<img src="https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/ttn_components.PNG" width="700"><br>

The data flow is as follows
1. The sensor sends data
1. The gateway(s) in reception range receive the data and forwards it to the network server
1. The network server validates and decrypts the data, determines the appropriate application, and sends it to the respective application server
1. The application server processes and stores the data, making it available for analysis or integration with other services
1. The End User application receives the data from the application server and may process and/or visualize it

## Typical range of transmission
The typical range for LoRaWAN devices is a few hundred meters to a few kilometers in urban areas and several kilometers in rural or open environments.
It is as well possible to place devices in buildings and have the next gateway somewhere outside.

However, the actual range can vary depending on several factors
- Distance to the Gateway
  The farther the sensor device is from the gateway, the weaker the signal, impacting communication reliability.
- Obstacles and Environment
  Physical obstacles like buildings, walls, windows and trees can weaken or block the signal, affecting signal strength and range.
- Gateway Density
  More gateways nearby improve coverage and increase the chances of successful communication.
- Transmit Power of the Device
  Higher transmit power allows for longer range but consumes more energy.
- Environmental Factors
  Weather, humidity, and atmospheric conditions can influence signal strength.
- Interference
  Electronic noise or interference from other devices can impact reception.
- Antenna Quality
  The quality of antennas on both the IoT device and gateway affects range and reception quality.
- Device Orientation
  Each antenna has a designed orientation. Refer to the device datasheets to see how you place best the devices.

## What is TTN?
`The Things Network - TTN` is a global, community-driven, open-source LoRaWAN network. It provides an infrastructure for connecting own devices free of charge. 
This public network is built on the principle of shared infrastructure, meaning that users can benefit from existing gateways deployed by others in the community.
As long as there are TTN gateways within the reception range of your device, you can utilize the network without the need to deploy your own gateway.

## Difference to other LoRaWAN networks
In Switzerland, for example, the company Swisscom offers a similar network, although there is a charge for it.
Here, Swisscom maintains the entire network infrastructure and you only have to integrate your own devices. With the TTN, every individual can contribute to this infrastructure and 
install a gateway at home, for example. This gateway then not only forwards LoRa telegrams from one's own devices, but also from other users to the Internet.

## Getting started with TTN
### Account and Console
> The `Console` is the place where you can add devices and gateways.
> First of all you need a "The Things Network" account.<br>
> The account and usage is free of charge.

1. Go to [www.thethingsnetwork.org/login/](https://www.thethingsnetwork.org/login/) and log in
	 - If you don't have an account, click `Sign up for free` to create one
   - You will receive an email to confirm your email address. You have 24 hours to do so.
1. Select [Console](https://console.thethingsnetwork.org/) from the menue on top right, that will bring you to [console.cloud.thethings.network/](https://console.cloud.thethings.network/)
1. Choose the cluster, for Europe thats `Europe 1`

### Create a new application
> Each device must be assigned to an application which you let organize and manage them.
> It is recommended to create an own application for each project and sensor model.
> You can add multiple sensor of the same type to the application.

1. Log in and open the [Console](https://console.thethingsnetwork.org/).
1. Press `Go to applications`
1. Click [(+) Create application](https://eu1.cloud.thethings.network/console/applications/add)
   - For `Application ID`, choose a unique ID of lower case, alphanumeric characters and nonconsecutive - and _.
   - For `Application Description`, enter anything you like.
<img src="https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/ttn_create_application.png" width="700"><br>
1. Click `Create Application` to finish.

You will be redirected to an application page where you can add and manage devices.

### Adding devices
Refer to the indiviual [sensor pages](https://hslu-ige-laes.github.io/lora-devices-ttn/#sensor-overview). There you'll find step by step guides on how to add and configure each device.