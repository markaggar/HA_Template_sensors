# Dynamic Water Rate Sensor for Home Assistant

This template sensor calculates your water usage cost with support for tiered rates and summer surcharges. It is designed for easy integration with Home Assistant and can be customized to match your local utility's pricing structure.

## Features

- Tiered water pricing support
- Automatic summer surcharge and variable maintenance fee calculations included in the rate (if applicable to your utility) 
- Simple template sensor
- Does not include fixed costs that will be charged no matter what the usage.

## Uses
- Create a simple automation that notifies you when the water rate changes based on your monthly water usage.
- Calculate how much your (kid's) long shower actually cost. 
- Use the sensor in the Home Assistant energy dashboard (note that is unclear if the energy dashboard calculates the cost over time with the various rates, or it does a simple calculation based on the current period usage and the current rate, which would obviously inflate the cost).

# Configuration

You will need your water utility rate plan to plug in the various tiers and rates. Typically this can be found online or you can look at your bill (better if you get the full rate card)

1. Edit the template with the name of the utility sensor that is measuring usage into your house and that resets at the beginning of each billing cycle. 

   ```
   {%- set raw_value = states('sensor.[your_utility sensor that resets monthly]') | float(0) -%}
   ```

   Note that this sensor has to be in volume (not flow) and also needs to reset at the beginning of the billing month.  If you just have a flow meter, you can use an integral helper to convert the flow (e.g. gal/min) into volume (e.g. gal). To reset at the beginning of the month, create a utility sensor helper based on the volume sensor and configure it to reset to zero periodically.

2. Specify the unit type that the Home Assistant utilty sensor represents (that resets to zero at the begining of the billing cycle).

   ```
   {%- set usage_unit = 'gallons' -%}    {# e.g., 'gallons', 'litres', 'cubic_feet', 'cubic_meters', 'CCF', 'CM' #}
   ```
3. Configure the unit type that the rate plan represents
   ```
   {%- set rate_unit = 'CCF' -%}         {# 'CCF' (Centum Cubic Feet) or 'CM' (Cubic Meter) #}
   ```
4. Set the tiers and rates for each tier of usage (how much water costs based on consumption in a given month)

   ```
   {%- set tiers = [
   {'min': 0,   'max': 10,   'rate': 6.11},
   {'min': 10,  'max': 20,   'rate': 10.34},
   {'min': 20,  'max': 30,   'rate': 12.41},
   {'min': 30,  'max': 9999, 'rate': 16.69}
   ] -%}
   ```
5. If your utility has a surcharge for summer months, specify the months your utility considers summer months.
   ```
   {%- set summer_months = [6,7,8,9] -%} {# June=6 ... September=9 #}
   ```
   5a. And set the surcharge rates
   
       {%- set summer_surcharge = [
         {'min': 20, 'max': 30, 'rate': 0.10},
         {'min': 30, 'max': 9999, 'rate': 0.30}
       ] -%}
       
7. If your utility has a maintenance fee that increases if the usage exceeds a certain amount, you can set that
   ```
   {%- set maintenance_base = 6 -%}      {# in CCF or CM, above which the maintenance fee will be charged per #}
   {%- set maintenance_rate = 9.97 -%}     {# charge in your currency for each CCF or CM above the base amount #}
   ```
## Installation

1. **Create a new template sensor helper in Settings/Devices**
   - Copy the first section of the template from after "state:>" up to and including the last "{%endif%}" before the unit_of_measurement section.
   - Set the unit of measurement e.g. USD/gal or [whatever your currency]/litre (you will need to type this, it probably won't be available for selection).

  OR (if you want all of the attributes, which I don't need). 

1. **Copy the edited YAML File**
   - Download or copy `water_rate_sensor.yaml` from this folder to your Home Assistant configuration directory (e.g., `/config/`).

2. **Include the Template**
   - In your `configuration.yaml` or under your `template:` section, use `!include` to add the sensor:
     ```yaml
     template: !include Water Rate Dynamic Sensor/water_rate_sensor.yaml
     ```
   - Adjust the path if you place the file elsewhere.

3. **Reload YAML**
   - After saving your changes, reload the Template Sensors in Developer Tools/YAML.
  

## Usage

- The sensor calculates the water cost based on your usage sensor (e.g., `sensor.daily_water_usage`).
- It outputs the calculated cost as another sensor (e.g., `sensor.water_rate_cost`).

## Support

This sensor is provided **as-is** with no guarantee of support. Issues or pull requests will be considered but there is no assurance they will be acted upon.
