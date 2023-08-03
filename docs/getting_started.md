---
layout: default
title: Getting started with TTN
nav_order: 3
has_children: false
permalink: /docs/getting_started
---

# Getting started with TTN
{: .no_toc }

---
## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Account
- First of all you need a "The Things Network" account<br>
- The account and usage is free of charge

1. Go to [www.thethingsnetwork.org/login/](https://www.thethingsnetwork.org/login/) and log in
	 - If you don't have an account, click `Sign up for free` to create one
   - You will receive an email to confirm your email address. You have 24 hours to do so.
	 
---

## Console
- The `Console` is the place where you can add sensor devices and gateways.
 
1. Once logged in, select [Console](https://console.thethingsnetwork.org/) from the menue on top right, that will bring you to [console.cloud.thethings.network/](https://console.cloud.thethings.network/)
1. Choose the cluster, for Europe thats `Europe 1`

---

## Create a new Application
- Each device must be assigned to an application which you let organize and manage them.
- It is recommended to create an own application for each project and sensor model.
- You can add multiple sensor of the same type to the application.

1. Log in and open the [Console](https://console.thethingsnetwork.org/).
1. Press `Go to applications`
1. Click [(+) Create application](https://eu1.cloud.thethings.network/console/applications/add)
   - For `Application ID`, choose a unique ID of lower case, alphanumeric characters and nonconsecutive - and _.
   - For `Application Description`, enter anything you like.
<img src="https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/docs/ttn_create_application.png" width="700"><br>
1. Click `Create Application` to finish.

You will be redirected to an application page where you can add and manage devices.

---

## Adding Sensor Devices
Refer to the indiviual [sensor pages](https://hslu-ige-laes.github.io/lora-devices-ttn/#sensor-overview). There you'll find step by step guides on how to add and configure each device.

---

## Fetching the Data via MQTT
- MQTT (Message Queuing Telemetry Transport) is a lightweight and efficient messaging protocol commonly used in IoT.
- The Things Network exposes the data via such an [MQTT Server](https://www.thethingsindustries.com/docs/integrations/mqtt/). Your End User application simply can subscribe to it and receives all sensor data updates in real time.
- For that its required to create an API key, refer to this [documentation](https://www.thethingsindustries.com/docs/integrations/mqtt/) for details.
- Be aware of, that this is a fire and forget principle. That means, in The Things Network Community you are responsible for saving the data.
- If your end user application or database is down e.g. because of maintenance, the data gets per default not saved. Refer to the [Add Storage](https://hslu-ige-laes.github.io/lora-devices-ttn/docs/getting_started#add-storage) section below for a short time storage option.

---

## Add Storage
- The Storage Integration allows storing received upstream messages in a persistent database, and retrieving them at a later time.
- Normally incoming data in the network only gets forwarded to the end user applications via the [MQTT Server](https://www.thethingsindustries.com/docs/integrations/mqtt/) and does not get saved. Fire and forget.
- The Things Network Community offers a [24h storage](https://www.thethingsindustries.com/docs/integrations/storage/) which is accessible through an API.
- Use case: If the end user application or database is down for maintenance reasons, its possible to fetch the missed data from this storage.
- To activate it, follow these steps.

1. [Log in](https://eu1.cloud.thethings.network/console/applications) and go to `applications`
1. Select the desired application
1. Select the tab `Integrations > Storage Integration`
1. Press `Activate Storage Integration`
