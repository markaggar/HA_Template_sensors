# Water Rate Dynamic Sensor

A sophisticated Home Assistant template sensor that calculates dynamic water rates based on tiered pricing structures and seasonal surcharges. This sensor provides real-time water billing calculations to help you monitor and manage your water usage costs.

## Features

- **Tiered Pricing Structure**: Supports 4-tier water pricing with increasing rates
- **Seasonal Surcharges**: Automatic summer surcharges (May-September)
- **Real-time Calculations**: Dynamic rate calculations based on current usage
- **Detailed Billing Breakdown**: Comprehensive cost analysis and tier information
- **Multiple Sensors**: Main rate sensor plus detailed bill estimate sensor

## Installation

### Prerequisites

- Home Assistant with template sensor support
- A water usage sensor that provides gallon measurements (e.g., `sensor.water_usage_gallons`)

### Setup Steps

1. **Copy the Configuration**
   ```yaml
   # Copy the contents of water_rate_sensor.yaml to your Home Assistant configuration.yaml
   # Or include it as a separate package in your packages directory
   ```

2. **Verify Dependencies**
   - Ensure you have a sensor named `sensor.water_usage_gallons` that reports water usage in gallons
   - If your sensor has a different name, update all references in the template

3. **Restart Home Assistant**
   - Restart Home Assistant to load the new sensors
   - Check the logs for any configuration errors

4. **Verify Installation**
   - Navigate to Developer Tools > States
   - Look for the new entities:
     - `sensor.water_rate_dynamic`
     - `sensor.water_bill_estimate`

## Usage

### Available Sensors

#### Water Rate Dynamic (`sensor.water_rate_dynamic`)
- **Unit**: $/gallon
- **Purpose**: Shows the current rate per gallon based on usage tier and season
- **Updates**: Real-time based on current usage and date

#### Water Bill Estimate (`sensor.water_bill_estimate`)
- **Unit**: $
- **Purpose**: Provides total estimated water bill based on current usage
- **Updates**: Real-time calculation of total costs

### Sensor Attributes

Both sensors provide detailed attributes:

- `usage_gallons`: Current water usage in gallons
- `current_tier`: Which pricing tier applies to current usage
- `base_rate`: Base rate per gallon for current tier
- `summer_period`: Boolean indicating if summer surcharges apply
- `summer_surcharge`: Additional summer surcharge rate
- `total_estimated_cost`: Total estimated cost for current usage
- `tier_breakdown`: Detailed breakdown of costs per tier

### Example Lovelace Card

```yaml
type: entities
title: Water Usage & Billing
entities:
  - entity: sensor.water_usage_gallons
    name: Water Usage
  - entity: sensor.water_rate_dynamic
    name: Current Rate
  - entity: sensor.water_bill_estimate
    name: Estimated Bill
  - type: attribute
    entity: sensor.water_rate_dynamic
    attribute: current_tier
    name: Current Tier
  - type: attribute
    entity: sensor.water_rate_dynamic
    attribute: summer_period
    name: Summer Period
```

## Customization

### Modifying Rate Structure

The sensor uses the following default rate structure:

#### Base Tiers
- **Tier 1**: 0-3,000 gallons @ $0.0035/gallon
- **Tier 2**: 3,001-8,000 gallons @ $0.0045/gallon
- **Tier 3**: 8,001-15,000 gallons @ $0.0055/gallon
- **Tier 4**: 15,001+ gallons @ $0.0075/gallon

#### Summer Surcharges (May-September)
- **Band 1**: 0-5,000 gallons @ +$0.001/gallon
- **Band 2**: 5,001-12,000 gallons @ +$0.0015/gallon
- **Band 3**: 12,001+ gallons @ +$0.002/gallon

### Customization Steps

1. **Update Tier Limits**
   ```yaml
   {% set tier1_max = 3000 %}  # Change to your utility's tier 1 limit
   {% set tier2_max = 8000 %}  # Change to your utility's tier 2 limit
   {% set tier3_max = 15000 %} # Change to your utility's tier 3 limit
   ```

2. **Update Rate Prices**
   ```yaml
   {% set tier1_rate = 0.0035 %} # Change to your utility's tier 1 rate
   {% set tier2_rate = 0.0045 %} # Change to your utility's tier 2 rate
   {% set tier3_rate = 0.0055 %} # Change to your utility's tier 3 rate
   {% set tier4_rate = 0.0075 %} # Change to your utility's tier 4 rate
   ```

3. **Update Summer Period**
   ```yaml
   {% set is_summer = now().month >= 5 and now().month <= 9 %}
   # Change months to match your utility's summer period
   ```

4. **Update Summer Surcharges**
   ```yaml
   {% set summer_band1_surcharge = 0.001 %}  # Adjust surcharge rates
   {% set summer_band2_surcharge = 0.0015 %} # to match your utility
   {% set summer_band3_surcharge = 0.002 %}  # billing structure
   ```

### Changing Source Sensor

If your water usage sensor has a different name:

1. Find all instances of `sensor.water_usage_gallons` in the configuration
2. Replace with your actual sensor entity ID
3. Ensure the sensor reports usage in gallons (convert if necessary)

## Troubleshooting

### Common Issues

#### Sensor Shows "Unknown" or "Unavailable"
- **Cause**: Source water usage sensor is not available
- **Solution**: 
  - Verify `sensor.water_usage_gallons` exists and has a valid state
  - Check that the source sensor is not in an error state
  - Ensure the source sensor reports numeric values

#### Incorrect Rate Calculations
- **Cause**: Mismatched rate structure or usage units
- **Solution**:
  - Verify your utility's actual rate structure matches the configuration
  - Ensure usage sensor reports in gallons (not cubic feet or liters)
  - Check that seasonal periods match your utility's billing cycle

#### Sensor Values Don't Update
- **Cause**: Template sensor caching or source sensor issues
- **Solution**:
  - Restart Home Assistant
  - Check Developer Tools > States for sensor updates
  - Verify the source sensor is updating regularly

#### Summer Surcharges Not Applied
- **Cause**: Incorrect month range or logic
- **Solution**:
  - Verify the summer period months match your utility
  - Check the current date/month in Developer Tools
  - Review the `summer_period` attribute for correct boolean value

### Debugging Steps

1. **Check Source Sensor**
   ```
   Developer Tools > States > sensor.water_usage_gallons
   ```

2. **Verify Template Logic**
   ```
   Developer Tools > Template > Test template expressions
   ```

3. **Review Sensor Attributes**
   ```
   Check all attributes of sensor.water_rate_dynamic for expected values
   ```

4. **Check Logs**
   ```
   Settings > System > Logs
   Filter for template sensor errors
   ```

## Support

This sensor is provided as-is with no guarantee of support. Issues or pull requests will be considered but there is no assurance they will be acted upon.

### Community Resources

- Home Assistant Community Forum
- Home Assistant Documentation
- Template Sensor Documentation

### Contributing

If you find bugs or have improvements:
1. Create an issue describing the problem or enhancement
2. Include your Home Assistant version and configuration details
3. Provide logs if reporting errors

### License

This template sensor configuration is provided under the same license as the parent repository.

---

**Note**: Always verify calculated rates against your actual utility bills. Rate structures vary by location and utility company. Adjust the configuration to match your specific utility's billing structure.