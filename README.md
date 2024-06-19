# hass-openwebrx
Integration of OpenWebRX+ under Homeassistant (hass).
Adding two mqtt sensors and one rest sensor into the homeassistant configuration in order to display the OpenWebRX+ status on an entity card.
In your configuration file add 
`rest: !include rest.yaml`
and
`mqtt: !include mqtt.yaml`
