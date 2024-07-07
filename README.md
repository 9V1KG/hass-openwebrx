# hass-openwebrx
Integration of [OpenWebRX+](https://github.com/luarvique/ppa) under Homeassistant (hass).
Adding sensors (mqtt, rest and template sensors) into the homeassistant configuration 
in order to display the OpenWebRX+ status on an entity card, and to trigger automations, when 
specific call signs, or stations in specific locator grids or on specific frequency bands 
were decoded.

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

Follow the instructions on screen to complete the setup. Add the hostname or IP address of your OpenWebRX. Once defined, go to Entities and change the ping sensor entity id to **`binary_sensor.openwebrx`**. It will then appear as a binary sensor with status *on*, when connected, and *off*, when offline.

In the file `rest.yaml` and `templates.yaml` modify **`binary_sensor.openwebrx`** under `availability:` 
according to the name of the binary sensor for your webrx, in case you have a different entity id.

### Homeassistant and OpenWebRX+ MQTT integration

Information from the OpenWebRX is passed to Homeassistant using a MQTT broker. Therefore you need a running MQTT broker (*e.g.* Mosquitto) and you need to install the Homeassistant MQTT integration. 
There is easy to find information about the installation available in the web. It is also necessary to configure OpenWebRXs MQTT settings. Please to note down the *MQTT broker address*, *port*, *username* and *password* (if any).

## Installation procedure

### OpenWebRX settings

Go to OpenWebRX settings. Under *Settings/Spotting and Reporting* scroll down to *MQTT settings*. Tick
*Enable publishing reports to MQTT* and fill in *Broker address*, *Username* and *Password*. We leave *Client ID* and *MQTT topic* as default.

### Homeassistant configuration

Add the `rest.yaml`, `mqtt.yaml` and `template.yaml` files to your hass configuration directory, **or** modify and append your existing rest, mqtt and template yaml files with the corresponding sensor definitions from the yaml files in the repository. You then need to restart Home Assistant. Go to **Developer tools** > **YAML** > **CHECK CONFIGURATION**. Once the result is ok, click **RESTART**.  

Any further change to the Template, Rest or MQTT files only need a reload of the yaml configuration. Go to *Developer tools > YAML* and look for **REST ENTITIES AND NOTIFY SERVICES**, **MANUALLY CONFIGURED MQTT ENTITIES** and **TEMPLATE ENTITIES**. Click on them to reload. You should then be able to find the new sensors under *Developer tools > states*. The following entities should be available:

#### MQTT sensors
* `sensor.openwebrx_ft8` Frequency of the latest decoded FT8 signal in kHz.
* `sensor.openwebrx_rx`  Latest profile switched to.  

Both sensors contain more information in their attributes.

#### Rest sensors
* `sensor.openwebrx_users`   Number of connected users.
* `sensor.openwebrx_version` Version of the OpenWebRX+ software.  

As for the mqtt sensors, more information is provided in the attributes. The sensor `sensor.openwebrx_users` is useful for  statistics, `sensor.openwebrx_version` provides the list of connected SDRs and their profiles.

#### Template sensors
* `sensor.webrx_ft8_min_ago`  Time since last FT-8 decode in minutes, derived from `sensor.openwebrx_ft8`
* `sensor.sdr_connected`      List of connected SDRs, derived from `sensor.openwebrx_version`

#### Ping (ICMP) sensors
* `binary_sensor.openwebrx`   OpenWebRX Online status  
   
The name of the Ping sensor depends on the name you gave in the ping integration, see above.

### Homeassistant entities card
The file `card.yaml` contains - as a first example - an *entities card*, how the OpenWebRX status could be displayed. With *ADD CARD* select *MANUAL* and copy the yaml code to the card configuration.
The second example is a *markdown card*. It will show the statistics of decoded FT-8, FT-4 and WSPR signals. Add the code after selecting the YAML editor of the markdown card.

## Homeassistant Automations and Alarms with OpenWebRX
Once, the sensors are added and working, you can easily define alarms, when a certain call sign is decoded or when activities show up on a selected band *etc.*  

Use the combination of frequency `sensor.openwebrx_ft8` and elapsed time `sensor.webrx_ft8_min_ago` to trigger an alarm for band activities. Use the `locator` attribute of `sensor.openwebrx_ft8` to trigger
an alarm, when a certain locator field (or list of locator fields) was detected.   
The example in `automation.yaml` gives an alarm, when a call sign starting with `9V1` was decoded with FT-8.

### Macro to spell a call sign with the phonetic alphabet
The file `macros.jinja` in the subdirectory `custom_templates` contains a macro to spell out a call sign (or any word) using the phonetic alphabet. You need to copy this file into your hass data directory under the same subdirectory `custom_templates`.

## Blueprints
Some example automations are available as blueprints. You can add them via the configuration file
 `configuration.yaml ` or by importing them, using the Home assistant UI. In the blueprints you can
switch on or off the spoken announcements.  
 **In order to get spoken announcements, you need to import the blueprint `webrx-announce.yaml` first.**

### Alarm for a list of specific call signs
Define a **Text** helper under *Settings -> Devices and Services -> Helpers*. Give it the name `call-sign-list`. You can use the entities card to display the list.  
![entities-card-example](/assets/callsign-list.png)  
Input the call signs, you want to receive an alarm from, in the text field, separated by comma. The automation blueprint `callsign-list.yaml` in `/blueprints/automations` will send a notification and annouce a message, once a call sign from the call sign list was decoded.

### Alarm for activity on the 6m band
The blueprint `webrx-6m-alert.yaml` sends a notification and makes an annoucement, when any call sign on the 6 m band was decoded.

### Alarm for activity from a station in Antarctica
The blueprint `webrx-antarctica-alert.yaml` sends a notification and makes an annoucement, when a call sign in Antarctica was decoded, based on the second letter in the locator field.

## Example

### Entities Card
![entities-card-example](/assets/entities-card.png)

### Markup Card
![markup-card-example](/assets/markup-card.png)

## Note
More or less finished ...