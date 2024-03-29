# GGreg20_V3 Radiation Sensor Node with ThingSpeak REST-API for Home Assistant
Connecting Kyiv-based Live [GGreg20_V3 Geiger counter](https://iot-devices.com.ua/en/product/ggreg20_v3-ionizing-radiation-detector-with-geiger-tube-sbm-20/) sensor node to the [Home Assistant](https://www.home-assistant.io/) via HTTP GET API.

## General sensor information
GGreg20_V3 Geiger counter Hackaday project page: https://hackaday.io/project/183103-ggreg20v3-ionizing-radiation-detector

ThingSpeak Channel: https://thingspeak.com/channels/1749073

Datasheets: 
[EN](https://iot-devices.com.ua/wp-content/uploads/2022/02/ggreg20_v3-datasheet-eng.pdf), 
[UA](https://iot-devices.com.ua/wp-content/uploads/2022/02/ggreg20_v3-datasheet-ukr.pdf)

Order on Tindie: https://www.tindie.com/stores/iotdev/

<a href="https://www.tindie.com/stores/iotdev/?ref=offsite_badges&utm_source=sellers_iotdevices&utm_medium=badges&utm_campaign=badge_large"><img src="https://d2ss6ovg47m0r5.cloudfront.net/badges/tindie-larges.png" alt="I sell on Tindie" width="200" height="104"></a>

## About this repo
This repository is for Home Assistant server users who want to add radiation data from [the GGreg20_V3 demo module located in Kyiv, Ukraine](https://twitter.com/GGreg20_V3), to their Home Assistant sensor list.
This is a real module that continuously measures background radiation levels (excluding blackouts) and transmits them directly to [ThingSpeak](https://thingspeak.com/channels/1749073) via the HTTP API.

To add the necessary entities, you need to edit the configuration.yaml file and create widgets. And that's it.

## Step 1. Add REST sensors to Home Assistant counfiguration.yaml
Here Home Assistant API is used: https://www.home-assistant.io/integrations/sensor.rest/
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
### 2.1 Basic Widget Example
To add a simple widget to the dashboard, just create an Entity-based card with the following settings in the form of code:
![Basic Widget Code Editor](https://github.com/iotdevicesdev/GGreg20_V3-Kyiv-Radiation-Sensor-ThingSpeak-HomeAssistant/blob/main/ThingSpeak_Basic_codeEditor_2023-02-03_191905.jpg)
Basic Widget Code:
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

### 2.2 Extended Widgets

However, to have beautiful and expressive widgets, you can use the unofficial [HACS](https://hacs.xyz/docs/setup/prerequisites) extension (installed in Home Assistant additionally, according to a separate procedure) and dashboard plugins such as [custom-mini-card](https://github.com/kalkih/mini-graph-card).
![Extended Widget Code Editor](https://github.com/iotdevicesdev/GGreg20_V3-Kyiv-Radiation-Sensor-ThingSpeak-HomeAssistant/blob/main/ThingSpeak_Extended_2023-02-03_191905.jpg)
If you already have these extensions installed, here's an example of how to set up widgets for them:
#### 2.2.1 24-Hour Widget Example
![Extended Widget 24h Code Editor](https://github.com/iotdevicesdev/GGreg20_V3-Kyiv-Radiation-Sensor-ThingSpeak-HomeAssistant/blob/main/ThingSpeak_Extended-24h_2023-02-03_191905.jpg)
24-hour widget code:
```yaml
type: custom:mini-graph-card
icon: mdi:radioactive
entities:
  - entity: sensor.thingspeakapi_sensor_usvperh
    name: GGreg20_V3
name: GGreg20_V3 Sensor Node, uSv/h (24h)
hours_to_show: 24
show:
  labels: true
color_thresholds:
  - value: 0.2
    color: '#28a745'
  - value: 0.3
    color: '#c0392b'
  - value: 0.6
    color: '#dc3545'
```
#### 2.2.2 Hourly Widget Example with min / max / avg

![Extended Widget Hourly Code Editor](https://github.com/iotdevicesdev/GGreg20_V3-Kyiv-Radiation-Sensor-ThingSpeak-HomeAssistant/blob/main/ThingSpeak_Extended-Hourly_2023-02-03_191905.jpg)

Hourly Wigdet with min / max / avg code:
```yaml
type: custom:mini-graph-card
icon: mdi:radioactive
entities:
  - entity: sensor.thingspeakapi_sensor_usvperh
    aggregate_func: max
    name: Max
    color: null
  - entity: sensor.thingspeakapi_sensor_usvperh
    aggregate_func: min
    name: Min
  - entity: sensor.thingspeakapi_sensor_usvperh
    aggregate_func: avg
    name: Avg
    color: green
name: GGreg20_V3 Radiation Power (Hourly)
hours_to_show: 24
points_per_hour: 12
group_by: hour
show:
  labels: true
```

#### 2.2.3 Gauge Widget Example

![Extended Widget Gauge Visual Editor](https://github.com/iotdevicesdev/GGreg20_V3-Kyiv-Radiation-Sensor-ThingSpeak-HomeAssistant/blob/main/ThingSpeak_Extended-Gauge_2023-02-03_191905.jpg)
Gauge widget code:
```yaml
type: gauge
entity: sensor.thingspeakapi_sensor_usvperh
min: 0
max: 0.9
unit: uSv/h
name: GGreg20_V3 Sensor Node ThingSpeak
needle: true
severity:
  green: 0
  yellow: 0.3
  red: 0.6
```
