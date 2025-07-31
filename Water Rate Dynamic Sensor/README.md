# Water Rate Dynamic Sensor

A Home Assistant template sensor that calculates dynamic water rates based on tiered pricing structures and seasonal surcharges. This sensor provides accurate water billing estimates and real-time rate calculations based on current usage levels.

## Features

- **Tiered Pricing Structure**: Automatically calculates rates across 4 usage tiers
- **Summer Surcharges**: Applies additional charges during summer months (May-September)
- **Real-time Rate Calculation**: Shows current rate per gallon based on usage tier
- **Detailed Bill Estimation**: Provides complete billing breakdown with tier analysis
- **Rich Attributes**: Includes usage details, tier information, and cost breakdowns

## Prerequisites

Before installing this sensor, ensure you have:

1. **Home Assistant** with template sensor support
2. **Water Usage Sensor**: A sensor entity named `sensor.water_usage_gallons` that provides current water usage in gallons
   - This can be created from your water meter readings
   - Must provide numeric values in gallons
   - Should update regularly for accurate rate calculations

## Installation

1. **Download the Configuration**:
   - Copy the `water_rate_sensor.yaml` file to your Home Assistant configuration directory

2. **Add to Configuration**:
   - If using packages: Place the file in your `packages` directory
   - If using single configuration: Copy the contents into your `configuration.yaml` under the `template:` section

3. **Restart Home Assistant**:
   - Restart Home Assistant to load the new sensor configuration

4. **Verify Installation**:
   - Check that the following entities are created:
     - `sensor.water_rate_dynamic`
     - `sensor.water_bill_estimate`

## Usage

### Primary Sensors

**Water Rate Dynamic** (`sensor.water_rate_dynamic`)
- Shows the current rate per gallon based on usage tier and season
- Unit: `$/gallon`
- Updates automatically as usage changes

**Water Bill Estimate** (`sensor.water_bill_estimate`)
- Shows the total estimated cost for current usage
- Unit: `$`
- Includes both base rates and summer surcharges

### Rate Structure

#### Base Tiers (Year-round)
- **Tier 1**: 0-3,000 gallons @ $0.0035/gallon
- **Tier 2**: 3,001-8,000 gallons @ $0.0045/gallon  
- **Tier 3**: 8,001-15,000 gallons @ $0.0055/gallon
- **Tier 4**: 15,001+ gallons @ $0.0075/gallon

#### Summer Surcharges (May-September)
- **Band 1**: 0-5,000 gallons + $0.001/gallon
- **Band 2**: 5,001-12,000 gallons + $0.0015/gallon
- **Band 3**: 12,001+ gallons + $0.002/gallon

### Available Attributes

Both sensors provide detailed attributes for monitoring and automation:

- `usage_gallons`: Current water usage
- `current_tier`: Current pricing tier
- `base_rate`: Base rate per gallon
- `summer_period`: Whether summer surcharges apply
- `summer_surcharge`: Additional summer rate
- `total_estimated_cost`: Complete cost estimate
- `tier_breakdown`: Detailed cost breakdown by tier

## Customization

### Modifying Rate Tiers

To adjust the pricing structure, edit the following variables in the YAML file:

```yaml
# Tier maximums (gallons)
{% set tier1_max = 3000 %}
{% set tier2_max = 8000 %}
{% set tier3_max = 15000 %}

# Tier rates ($/gallon)
{% set tier1_rate = 0.0035 %}
{% set tier2_rate = 0.0045 %}
{% set tier3_rate = 0.0055 %}
{% set tier4_rate = 0.0075 %}
```

### Adjusting Summer Period

To change the summer surcharge period, modify:

```yaml
{% set is_summer = now().month >= 5 and now().month <= 9 %}
```

Change the month numbers (5 = May, 9 = September) to match your local summer period.

### Changing Summer Surcharges

To adjust summer surcharge rates:

```yaml
# Summer surcharge bands (gallons)
{% set summer_band1_max = 5000 %}
{% set summer_band2_max = 12000 %}

# Summer surcharge rates (additional $/gallon)
{% set summer_band1_surcharge = 0.001 %}
{% set summer_band2_surcharge = 0.0015 %}
{% set summer_band3_surcharge = 0.002 %}
```

### Using Different Water Usage Sensor

If your water usage sensor has a different name, replace all instances of:
```yaml
states('sensor.water_usage_gallons')
```

With your sensor name:
```yaml
states('sensor.your_water_sensor_name')
```

## Troubleshooting

### Sensor Shows "Unknown" or "Unavailable"

**Cause**: Water usage sensor not found or not providing data
**Solution**: 
1. Verify `sensor.water_usage_gallons` exists and has a numeric value
2. Check sensor name spelling in the template
3. Ensure the water usage sensor is updating regularly

### Incorrect Rate Calculations

**Cause**: Usage sensor providing data in wrong units
**Solution**:
1. Verify usage sensor provides data in gallons
2. If using different units, convert in the template or create a conversion sensor
3. Check that usage values are reasonable (not negative or extremely large)

### Summer Surcharges Not Applied

**Cause**: Date/time issues or incorrect summer period configuration
**Solution**:
1. Verify Home Assistant system time is correct
2. Check summer period configuration (months 5-9 = May-September)
3. Restart Home Assistant if time zone was recently changed

### Sensors Not Updating

**Cause**: Template syntax errors or dependency issues
**Solution**:
1. Check Home Assistant logs for template errors
2. Verify all required sensors exist and are working
3. Test template syntax in Developer Tools > Template

### Billing Estimates Don't Match Actual Bill

**Cause**: Rate structure differences or missing fees
**Solution**:
1. Verify rate tiers match your actual utility rates
2. Check if your utility has additional fees not included in this sensor
3. Adjust rate variables to match your specific billing structure

## Support

This sensor is provided as-is with no guarantee of support. Issues or pull requests will be considered but there is no assurance they will be acted upon.

### Community Resources

- Review the Home Assistant template sensor documentation
- Check the Home Assistant community forums for template sensor help
- Verify your water meter integration is working correctly

### Contributing

If you find bugs or have improvements:
1. Fork the repository
2. Make your changes
3. Submit a pull request with detailed description

Remember that contributions are reviewed on a voluntary basis with no commitment to implementation.