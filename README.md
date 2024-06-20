# hass-openwebrx
Integration of [OpenWebRX+](https://github.com/luarvique/ppa) under Homeassistant (hass).
Adding a ping sensor, two mqtt sensors and one rest sensor into the homeassistant configuration 
in order to display the OpenWebRX+ status on an entity card.


## Rest and Mqtt Sensors

If not already done, in your home assistant configuration file `configuration.yaml` add:
```
rest: !include rest.yaml
mqtt: !include mqtt.yaml
```
Add the `rest.yaml` and `mqtt.yaml` files to your hass configuration directory or modify your existing rest and mqtt yaml files
with the additional sensor definitions.

## Additional Template Sensors

Add all lines from the file
```
template-add.yaml
```
to your hass template file `template.yaml`.

To check the online status of your OpenWebRX, you also need to add the **Ping (ICMP)** Integration. 
* Go to *Settings > Devices & Services*. 
* In the bottom right corner, select the *Add Integration* button.
* From the list, select **Ping (ICMP)**.
Follow the instructions on screen to complete the setup. Add the hostname or IP address of your OpenWebRX. 
It will then appear as a binary sensor with status *on*, when connected, and *off*, when offline.
In the file `rest.yaml` modify `binary_sensor.webrx_lan` under `availability:` 
according to the name of the binary sensor for your webrx.
## Homeassistant
You need to reload the yaml configuration. Go to *Developer tools > YAML* and look for **REST ENTITIES AND NOTIFY SERVICES** 
and **MANUALLY CONFIGURED MQTT ENTITIES**. Click on them to reload. You should then be able to find the new sensors 
under *Developer tools > states*.
## Note
Under development.
