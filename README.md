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

### Macros

Custom defined jinja macros are stored in the hass data sub-directory `custom_templates`.

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
There is easy to find information about the installation available in the web. It is also necessary to configure OpenWebRXs MQTT settings. Please to note down the *MQTT broker address*, *port*, *username* and *password* (if any).

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
* `sensor.webrx_ft8_callsign` Latest FT-8 call sign decoded, derived from `sensor.openwebrx_ft8`
* `sensor.webrx_ft8_min_ago`  Time since last FT-8 decode in minutes, derived from `sensor.openwebrx_ft8`
* `binary_sensor.webrx_ft8_decode` State `On`, when a FT-8 signal was decoded within the last hour
* `sensor.sdr_connected`      List of connected SDRs, derived from `sensor.openwebrx_version`
#### Ping (ICMP) sensors
* `binary_sensor.webrx_lan`   OpenWebRX Online status     
The name of the Ping sensor depends on the name you gave in the ping integration.

### Homeassistant entities card
The file `card.yaml` contains - as a first example - an *entities card*, how the OpenWebRX status could be displayed. With *ADD CARD* select *MANUAL* and copy the yaml code to the card configuration.
The second example is a *markdown card*. It will show the statistics of decoded FT-8, FT-4 and WSPR signals. Add the code after selecting the YAML editor of the markdown card.

## Homeassistant Automations and Alarms with OpenWebRX
Once, the sensors are added and working, you can easily define alarms, when a certain call sign is decoded or when activities show up on a selected band *etc.*  

Use the combination of frequency `sensor.openwebrx_ft8` and elapsed time `sensor.webrx_ft8_min_ago` to trigger an alarm for band activities. Use the `locator` attribute of `sensor.openwebrx_ft8` to trigger
an alarm, when a certain locator field (or list of locator fields) was detected.   
Examples are given in `automation.yaml`. They are triggered by a template. 
* The first examples gives an alarm, when a call sign starting with `9V1` was decoded with FT-8.
* The second example gives and alarm, when an Antarctica station was decoded, based on the second letter of the locator. 
* The third example gives an alarm, when a callsign outside singapore (`9V1`) was decoded on the 6 m band.

### Alarm for a list of specific call signs
Define a **Text** helper under *Settings -> Devices and Services -> Helpers*. Give it the name `call-sign-list`. You can use the entities card to display the list.  
![entities-card-example](/assets/callsign-list.png)  
Input the call signs, you want to receive an alarm from, in the text field, separated by comma. The automation `Alert: WebRX callsign list`will send a notification, once a call sign from the list was decoded.

### Macro to spell a call sign with the phonetic alphabet
The file `macros.jinja` in the subdirectory `custom_templates` contains a macro to spell out a call sign (or any word) using the phonetic alphabet. In `scripts.yaml` you can find an example.


## Example
![entities-card-example](/assets/entities-card.png)
![markup-card-example](/assets/markup-card.png)

## Note
Work in progress.