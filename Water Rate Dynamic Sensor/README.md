# Dynamic Water Rate Sensor for Home Assistant

This template sensor calculates your water usage cost with support for tiered rates and summer surcharges. It is designed for easy integration with Home Assistant and can be customized to match your local utility's pricing structure.

## Features

- Tiered water pricing (with unlimited top tier)
- Automatic summer surcharge calculation
- Easy YAML integration—no custom components required

## Installation

1. **Copy the YAML File**
   - Download or copy `water_rate_sensor.yaml` from this folder to your Home Assistant configuration directory (e.g., `/config/`).

2. **Include the Template**
   - In your `configuration.yaml` or under your `template:` section, use `!include` to add the sensor:
     ```yaml
     template: !include Water Rate Dynamic Sensor/water_rate_sensor.yaml
     ```
   - Adjust the path if you place the file elsewhere.

3. **Restart Home Assistant**
   - After saving your changes, restart Home Assistant for the sensor to become available.

## Usage

- The sensor calculates the water cost based on your usage sensor (e.g., `sensor.daily_water_usage`).
- It outputs the calculated cost as another sensor (e.g., `sensor.water_rate_cost`).
- You may need to edit the YAML to match your usage sensor's entity ID or local rates.

### Example Customization

- Change the `usage_sensor` variable in the YAML to your water usage sensor’s entity ID.
- Adjust the rate tiers and surcharge values as needed for your utility’s pricing.

## Troubleshooting

- If the sensor doesn’t appear, check for YAML syntax errors and confirm that the file is included in your configuration.
- Ensure your water usage sensor is updating and available in Home Assistant.

## Support

This sensor is provided **as-is** with no guarantee of support. Issues or pull requests will be considered but there is no assurance they will be acted upon.