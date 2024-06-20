# hass-openwebrx
Integration of OpenWebRX+ under Homeassistant (hass).
Adding two mqtt sensors and one rest sensor into the homeassistant configuration in order to display the OpenWebRX+ status on an entity card.
If not already done, in your configuration file add:
```rest: !include rest.yaml
   mqtt: !include mqtt.yaml```
To check the online status of your OpenWebRX, you also need to add the **Ping (ICMP)** Integration. 
* Go to *Settings > Devices & Services*. 
* In the bottom right corner, select the *Add Integration* button.
* From the list, select **Ping (ICMP)**.

Follow the instructions on screen to complete the setup. Add the hostname or IP address of your OpenWebRX. 
It will then appear as a binary sensor with status *on*, when connected, and *off*, when offline.

