---
layout: default
title: The Things Network
nav_order: 2
has_children: true
permalink: /docs/ttn
---

# The Things Network
## Getting started with LoRaWAN
LoRaWAN (Long Range Wide Area Network) is a wireless technology that allows devices like sensors, to send data over long distances.
These devices are not directly connected to the internet. They send the data periodically over the LoRaWAN to a so called gateway antenna. This gateway then forwards the data to an internet server.
It's perfect for connecting devices that need to send small amounts of data, like temperature readings, air quality information, or water level measurements. Normally these devices are battery powered.

## The Things Network (TTN)
The `TTN` is a global, community-driven, open-source LoRaWAN network. It provides an infrastructure for connecting own devices free of charge.
The Things Network's public network is built on the principle of shared infrastructure, meaning that users can benefit from existing gateways deployed by others in the community.
As long as there are TTN gateways within the coverage area of your device, you can utilize the network without the need to deploy your own gateway.

### The Console
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

> You will be redirected to an application page where you can add and manage devices.
> In the indiviual [sensor pages](https://hslu-ige-laes.github.io/lora-devices-ttn/#sensor-overview) you'll find step by step guides on how to add and configure each device.