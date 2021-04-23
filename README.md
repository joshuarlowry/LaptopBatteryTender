# LaptopBatteryTender
A method for leaving your laptop on the charger full-time without destroying your battery. 

## Overview
This solution uses a smart switch (WEMO) with a laptop charge cable plugged into it. A chron job runs a script (sample below) and passes in the battery capacity. Based on the set point in the script, call out to the webhook that is configured to toggle the smart switch. For this case I was running until getting below 20 and then charging for a set amount of time (from homeassistant) before turning off again. 

## Setup
Configure a webhook in your tool of choice, I used homeassistant.

Create a cron job to call the script template below
`<path to script>/toggle.sh "$(cat /sys/class/power_supply/BAT0/capacity)"`

Create the script called: toggle.sh

```
#!/bin/bash

DATE=$(date +"%D %T")
LOG="${DATE}, $1"
echo "${LOG}" >> log.txt
#If the value passed in is less than 20 then call the webhook
if [ $1 -lt 20 ]
then
	#Webhook on homeassistant to toggle power switch on
	curl -X POST http://<homeassistant server address>:<homeassistant port>/api/webhook/lowPower
fi
```
