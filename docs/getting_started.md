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

## Account and Console

- The `Console` is the place where you can add sensor devices and gateways.
- First of all you need a "The Things Network" account.<br>
- The account and usage is free of charge.

1. Go to [www.thethingsnetwork.org/login/](https://www.thethingsnetwork.org/login/) and log in
	 - If you don't have an account, click `Sign up for free` to create one
   - You will receive an email to confirm your email address. You have 24 hours to do so.
1. Select [Console](https://console.thethingsnetwork.org/) from the menue on top right, that will bring you to [console.cloud.thethings.network/](https://console.cloud.thethings.network/)
1. Choose the cluster, for Europe thats `Europe 1`

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

## Adding Sensor Devices
Refer to the indiviual [sensor pages](https://hslu-ige-laes.github.io/lora-devices-ttn/#sensor-overview). There you'll find step by step guides on how to add and configure each device.