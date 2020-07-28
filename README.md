# Special thanks to jshridha for making this: https://github.com/jshridha/inkbird

Code is altered by me (used code snippet from different forks) to use degree celcius and the ability to use username and password in mqtt


# Inkbird to Homeassistant
## The purpose of this project is to get popular multi-probe inkbird thermometers into homeassistant for automations, temperature logging, and general awesomeness.

## Requirements

This is designed to run on a raspberry pi, but it should work fine on any linux system with a BLE adapter. It should even run on a raspberry pi zero w.


## Quick Start
The easiest way to run this probject is to run the image from docker hub using docker-compose

wget https://raw.githubusercontent.com/dennisd80/inkbird/master/docker-compose.yaml

You will need to modify the environmental variables
* `INKBIRD_MQTT_HOST` is the MQTT server that home assistant uses
* `INKBIRD_ADDRESS` is the bluetooth address for the inkbird
* `INKBIRD_MQTT_USER` is the username you use for MQTT broker
* `INKBIRD_MQTT_PASSWORD` is the password for your MQTT broker

Then just run `docker-compose up -d`

I use the following code in my sensor.yaml, based on the igrill automation:
```yaml
######### iGRILL PROBE SENSORS C ##############
- platform: mqtt
  state_topic: "inkbird/2cab33243bcc/Probe1"
  name: "Probe 1"
  qos: 0
  unit_of_measurement: '°C'
  value_template: "{{ value_json.temperature }}"
- platform: mqtt
  state_topic: "inkbird/2cab33243bcc/Probe2"
  name: "Probe 2"
  qos: 0
  unit_of_measurement: '°C'
  value_template: "{{ value_json.temperature }}"
- platform: mqtt
  state_topic: "inkbird/2cab33243bcc/Probe3"
  name: "Probe 3"
  qos: 0
  unit_of_measurement: '°C'
  value_template: "{{ value_json.temperature }}"
- platform: mqtt
  state_topic: "inkbird/2cab33243bcc/Probe4"
  name: "Probe 4"
  qos: 0
  unit_of_measurement: '°C'
  value_template: "{{ value_json.temperature }}"
- platform: mqtt
  state_topic: "inkbird/2cab33243bcc/Probe1"
  name: "inkbird battery"
  qos: 0
  unit_of_measurement: "%"
  value_template: "{{ value_json.bat }}"

############# iGRILL PROBE SENSORS F ###########
- platform: template
  sensors:
    probe_1_f:
     friendly_name: "Probe 1 F"
     unit_of_measurement: 'ºF'
     value_template: "{{ (((states('sensor.probe_1') | float) * 9/5)+ 32)|round(1)}}"
- platform: template
  sensors:
    probe_2_f:
     friendly_name: "Probe 2 F"
     unit_of_measurement: 'ºF'
     value_template: "{{ (((states('sensor.probe_2') | float) * 9/5)+ 32)|round(1)}}"
- platform: template
  sensors:
    probe_3_f:
     friendly_name: "Probe 3 F"
     unit_of_measurement: 'ºF'
     value_template: "{{ (((states('sensor.probe_3') | float) * 9/5)+ 32)|round(1)}}"
- platform: template
  sensors:
    probe_4_f:
     friendly_name: "Probe 4 F"
     unit_of_measurement: 'ºF'
     value_template: "{{ (((states('sensor.probe_4') | float) * 9/5)+ 32)|round(1)}}"

########### iGRILL ALERT SENSORS ############
- platform: template
  sensors:
    grill_alert_temp:
     friendly_name: "Grill Temp Alert"
     value_template: >-
      {% if (states.sensor.probe_4.state | int) < (states.input_number.grill_alert_low.state | int) or (states.sensor.probe_4.state | int)  > (states.input_number.grill_alert_high.state | int) %}
       Alert
      {% else %}
       Normal
      {% endif %}
- platform: template
  sensors:
    target_alert_temp_probe_1:
     friendly_name: "Probe 1 Target Alert"
     value_template: >-
      {% if (states.sensor.probe_1.state | int) >= (states.input_number.grill_probe_1_target.state | int) %}
       Alert
      {% else %}
       Normal
      {% endif %}  
- platform: template
  sensors:
    target_alert_temp_probe_2:
     friendly_name: "Probe 2 Target Alert"
     value_template: >-
      {% if (states.sensor.probe_2.state | int) >= (states.input_number.grill_probe_2_target.state | int) %}
       Alert
      {% else %}
       Normal
      {% endif %}
- platform: template
  sensors:
    target_alert_temp_probe_3:
     friendly_name: "Probe 3 Target Alert"
     value_template: >-
      {% if (states.sensor.probe_3.state | int) >= (states.input_number.grill_probe_3_target.state | int) %}
       Alert
      {% else %}
       Normal
      {% endif %}

############# iGRILL INPUT SENSOR F ############
- platform: template
  sensors:
    grill_alert_low_f:
     friendly_name: "Grill Low Temp F"
     unit_of_measurement: 'ºF'
     value_template: "{{ (((states('input_number.grill_alert_low') | float) * 9/5)+ 32)|round(1)}}"
- platform: template
  sensors:
    grill_alert_high_f:
     friendly_name: "Grill High Temp F"
     unit_of_measurement: 'ºF'
     value_template: "{{ (((states('input_number.grill_alert_high') | float) * 9/5)+ 32)|round(1)}}"
- platform: template
  sensors:
    grill_probe_1_target_f:
     friendly_name: "Probe 1 Target Temp F"
     unit_of_measurement: 'ºF'
     value_template: "{{ (((states('input_number.grill_probe_1_target') | float) * 9/5)+ 32)|round(1)}}"
- platform: template
  sensors:
    grill_probe_2_target_f:
     friendly_name: "Probe 2 Target Temp F"
     unit_of_measurement: 'ºF'
     value_template: "{{ (((states('input_number.grill_probe_2_target') | float) * 9/5)+ 32)|round(1)}}"
- platform: template
  sensors:
    grill_probe_3_target_f:
     friendly_name: "Probe 3 Target Temp F"
     unit_of_measurement: 'ºF'
     value_template: "{{ (((states('input_number.grill_probe_3_target') | float) * 9/5)+ 32)|round(1)}}"
