# lora-devices-ttn
> LoRaWAN device integration into <a href="https://www.thethingsnetwork.org/">ttn (the things network)</a>

## Intro
- Before a device can communicate via The Things Network you need to register it with an application. The general workflow for doing that can be found <a href="https://www.thethingsnetwork.org/docs/devices/registration.html">here</a>.
- Each device requires a specific code part to parse the sensor data information out of the whole message. This function is called "payload decoder".
- In most cases the manufacturer provides this information. Nevertheless we share in this repository the payload decoder code for the devices we're working with.

## Device Overview
| File | Manufacturer | Product | Price [CHF] | Description |
| --- | --- | --- | --- | --- |
|[avelon-wisely-carbonsense](https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/avelon-wisely-carbonsense.md)| Avelon | [Wisely Carbon Sense](https://avelon.com/en/wisely/) | 263.- (2020-07) | Indoor sensor for Temperature, Humidity and CO2 |

<!-- | Manufacturer | Product Name | Product-Link | Price [CHF] | Description |-->

## Create account
To register your device you’ll need a The Things Network account.
1. Go to [account.thethingsnetwork.org](https://account.thethingsnetwork.org/) and [click create an account](https://account.thethingsnetwork.org/register)
   - You will receive an email to confirm your email address. You have 24 hours to do so.
2. Select [Console](https://console.thethingsnetwork.org/) from the menue on top right
3. From the top right menu, select your name and then Settings. Then change the default Handler if the one currently selected is not where you’ll be deploying most of your devices.

