# hass-openwebrx
Integration of [OpenWebRX+](https://github.com/luarvique/ppa) under Homeassistant (hass).
Adding sensors (mqtt, rest and template sensors) into the homeassistant configuration 
in order to display the OpenWebRX+ status on an entity card.

## Prerequisites


### Rest, Mqtt and Template Sensor yaml files

Check that in your home assistant configuration file `configuration.yaml` the following lines are included, if not add them:
```
rest: !include rest.yaml
mqtt: !include mqtt.yaml
template: !include templates.yaml
```
### Homeassistant Ping (ICMP) integration

To check the online status of your OpenWebRX, you also need to add the **Ping (ICMP)** Integration. 
* Go to *Settings > Devices & Services*. 
* In the bottom right corner, select the *Add Integration* button.
* From the list, select **Ping (ICMP)**.   
Follow the instructions on screen to complete the setup. Add the hostname or IP address of your OpenWebRX. 
It will then appear as a binary sensor with status *on*, when connected, and *off*, when offline.
In the file `rest.yaml` modify `binary_sensor.webrx_lan` under `availability:` 
according to the name of the binary sensor for your webrx.

### Homeassistant and OpenWebRX+ MQTT integration

Information from the OpenWebRX is passed to Homeassistant using a MQTT broker. Therefore you need a running MQTT broker (*e.g.* Mosquitto) and you need to install the Homeassistant MQTT integration. 
There is easy to find information about the installation available in the web. It is also necessary to configure OpenWebRXs MQTT settings. You need to note down the MQTT broker address, port, username and password (if any).

## Installation procedure

### OpenWebRX settings

Go to OpenWebRX settings. Under *Settings/Spotting and Reporting* scroll down to *MQTT settings*. Tick
*Enable publishing reports to MQTT* and fill in *Broker address*, *Username* and *Password*. We leave *Client ID* and *MQTT topic* as default.

### Homeassistant configuration

Add the `rest.yaml`, `mqtt.yaml` and `template.yaml` files to your hass configuration directory, **or** modify and append your existing rest, mqtt and template yaml files with the corresponding sensor definitions from the yaml files in the repository.

You then need to reload the yaml configuration. Go to *Developer tools > YAML* and look for **REST ENTITIES AND NOTIFY SERVICES**, **MANUALLY CONFIGURED MQTT ENTITIES** and **TEMPLATE ENTITIES**. Click on them to reload. You should then be able to find the new sensors under *Developer tools > states*. The following entities should be available:
#### MQTT sensors
* `sensor.openwebrx_ft8` Frequency of the latest decoded FT8 signal in kHz.
* `sensor.openwebrx_rx`  Latest profile switched to.
#### Rest sensors
* `sensor.openwebrx_users`   Number of connected users.
* `sensor.openwebrx_version` Version of the OpenWebRX+ software.
#### Template sensors
* `sensor.webrx_ft8_callsign` Latest FT-8 call sign decoded.
* `sensor.webrx_ft8_min_ago`  Time since last FT-8 decode in minutes.
* `sensor.webrx_rx_source`    Last SDR switched.
* `sensor.sdr_connected`      List of connected SDRs.
#### Ping (ICMP) sensors
* `binary_sensor.webrx_lan`   OpenWebRX Online status     
The name of the Ping sensor depends on the name you gave in the ping integration.

### Homeassistant entities card
The file `card.yaml` contains as a first example of an *entities card*, how the OpenWebRX status could be displayed. With *ADD CARD* select *MANUAL* and copy the yaml code to the card configuration.
The second example is a *markdown card*. It will show the statistics of decoded FT-8, FT-4 and WSPR signals. Add the code after selecting the YAML editor of the markdown card.

## Note
Still under development.
