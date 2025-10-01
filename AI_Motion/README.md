# Home Assistant AI Motion Template Sensor
### Update
You're in the right place.  I moved this code to a new general purpose repo for Home Assistant Template Sensors.

If you've visited this page previously, you might have noticed that there are a far fewer attributes in this sensor than before.  The old version was not completely reliable and unnecessarily complex.  This version is much simpler and initializes quickly. Most critically, it requires both the person and motion sensor to be on at the same time for AI motion to be triggered on.  

Also, I learned that when developing template sensors, it's important to always start with a new sensor name/unique id whenever you do anything to the sensor that might cause it to not initialize properly.  This ensures that as you are developing the sensor you aren't introducing bugs into the template that you won't detect until you make a copy of the sensor to use with different underlying sensors.  This is because Home Assistant helpfully (or not) re-initializes a template sensor with the old attribute values when you reload the template (using Developer Tools/YAML/Template Entities).  This reload of old attribute values can mask issues that you won't see until you are starting out with a new sensor, resulting in your newly initialized sensor stuck in an 'unavailable' state, despite the old sensor appearing to work completely fine!

--------------
I was tired of getting woken up in the middle of the night by Home Assistant when my Reolink cameras would detect a person outside due to false positives.  What I noticed is that these AI person detections would rarely if ever coincide with a motion event.
Also, I wanted a way for my indoor cameras to be used as motion sensors for lighting control.  However, I didn't want the motion of my dog to set them off, so person detection was important. However, unless the person is fully visible and moving like a person (vs just sitting there), the person detection wouldn't trigger.  However, motion would frequently trigger. This meant that a person coming into the room would trigger the lights to be turned on, but it wouldn't be sustained, and I'd have to rely on motion events to keep the lights on.

As such I built this template sensor which is easy to adjust for your specific AI and motion sensors.

<img width="508" height="689" alt="image" src="https://github.com/user-attachments/assets/9f469a42-2cc0-49dd-9227-888992b9fd02" />

As you can see there are a few sensor attributes which power the sensor.  In particular, I developed a technique to store the previous time the motion sensor was on such that I can calculate the time delta between 'motion on' events to determine whether the sensor should stay on if there are a succession of motion events after the AI person event is triggered.  If there is a motion event within the delta time, the AI motion sensor will stay on even if there hasn't been another person event in hours.  

**Warning** Obviously if your motion sensor is set to highly sensitive and there is a wind storm or something that causes a lot of motion to be detected, a person detection event could leave the sensor on for a long time erroneously.

## Configuration
There are three configuration options which you edit directly in the each template sensor.  These are in the attributes section near the top of the sensor template.

### Sensor names
The first two are for the pair of sensors/binary_sensors (AI and motion) you want to use.

       attributes:
         motion: > # Name of the motion sensor
           {{states("binary_sensor.family_room_motion")}}  
         ai: > # Name of the AI sensor (could be person, pet etc)
           {{states("binary_sensor.family_room_person")}}  

### Delta time
The third is the delta time in seconds between motion events.  This can be configurable in that it can have some logic to determine what the timeout should be in certain circumstances.

The simple version is just to have a fixed value

         delta: 300 # seconds

This is a more complex setting which sets the timeout based on whether the TV in the room is on or off.  If the TV is on, we typically aren't moving much 
         
         delta: > # Maximum time in seconds between motion events before the sensor turns off
          {% if states("media_player.tv_family_room") != "off" and states("media_player.tv_family_room") != "unavailable" %}
             900
          {% else %}
             300
          {% endif %}

### Avoiding template loops
Given Home Assistant's propensity to complain about template loops, the sensor is configured as a trigger sensor.  You should include all of the sensors/binary sensors for all of your template sensors here.  The sensor is also updated every 15 seconds.

        - trigger:
          - platform: time_pattern
            seconds: "/15"
          - platform: state
            entity_id:
              - binary_sensor.family_room_motion # name of your motion, AI sensors and any other sensors you are using to determine delta timeouts**
              - binary_sensor.family_room_person
              - binary_sensor.kitchen_motion
              - binary_sensor.kitchen_person
              - media_player.tv_family_room

## Install
1. **Download the package file**: Download `AI_Motion.yaml` from this repository
2. **Create packages directory**: If you don't already have one, create a `packages` directory in your Home Assistant configuration directory
3. **Copy the package**: Place `AI_Motion.yaml` in your `packages` directory
4. **Enable packages in configuration.yaml**: Add the following to your `configuration.yaml` if not already present:
   ```yaml
   homeassistant:
     packages: !include_dir_named packages
   ```
5. **Update the sensor names** and the binary_sensor names that the package will create to match your camera entities and any other sensors you will use to trigger
6. **Copy the sensors** as many times as you need to for each of the cameras you want this capability for   
7. **Reload YAML**: Go to Developer Tools/YAML and reload 'Template entities'
8. Play around with the sensitivity of your Person and Motion sensors so they trigger frequently but you don't have both of them falsely triggering at the same time (and again, watch out for stormy nights!).

Enjoy!


