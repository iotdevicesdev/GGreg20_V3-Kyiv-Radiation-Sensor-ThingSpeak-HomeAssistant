# GGreg20_V3 Radiation Sensor Node with ThingSpeak REST-API for Home Assistant
Connecting Kyiv-based GGreg20_V3 Geiger counter sensor node to the Home Assistant via HTTP GET API

This repository is for Home Assistant server users who want to add radiation data from the GGreg20_V3 demo module located in Kyiv, Ukraine, to their Home Assistant sensor list.
This is a real module that continuously measures background radiation levels (excluding blackouts) and transmits them directly to ThingSpeak via the HTTP API.

>To add the necessary entities, you need to edit the configuration.yaml file and create widgets. And that's it.

## Step 1. Add REST sensors to your counfiguration.yaml
```yaml
sensor:
  - platform: rest
    resource: https://api.thingspeak.com/channels/1749073/fields/1.json?results=1
    name: ThingSpeakAPI Sensor CPM
    state_class: measurement
    value_template: "{{ int(value_json.feeds.0.field1) }}"
    
  - platform: rest
    resource: https://api.thingspeak.com/channels/1749073/fields/2.json?results=1
    name: ThingSpeakAPI Sensor uSvperH
    state_class: measurement
    value_template: "{{ float(value_json.feeds.0.field2) }}"
    
  - platform: rest
    resource: https://api.thingspeak.com/channels/1749073/fields/3.json?results=1
    name: ThingSpeakAPI Sensor dose usV
    state_class: measurement
    value_template: "{{ float(value_json.feeds.0.field3) }}"
```
After adding the sensors to the configuration and restarting the server, the following sensor entities will be available with data received from the ThingSpeak channel for the GGreg20_V3 Radiation Sensor Node.

![DevTools HA Entities](https://github.com/iotdevicesdev/GGreg20_V3-Kyiv-Radiation-Sensor-ThingSpeak-HomeAssistant/blob/main/ThingSpeak_HA_devTools_entities_2023-02-03.jpg)

## Step 2. Make widgets in Home Assistant
### Basic Widget Example
To add a simple widget to the dashboard, just create an Entity-based card with the following settings in the form of code:
```yaml
type: entities
entities:
  - entity: sensor.thingspeakapi_sensor_cpm
    icon: mdi:pulse
    name: ThingSpeakAPI Sensor CPM
  - entity: sensor.thingspeakapi_sensor_usvperh
    icon: mdi:radioactive
    name: ThingSpeakAPI Sensor uSv/h
  - entity: sensor.thingspeakapi_sensor_dose_usv
    icon: mdi:radioactive
    name: ThingSpeakAPI Sensor dose uSv
title: GGreg20_V3 Radiation (MA5)
```
### Extended Widgets
