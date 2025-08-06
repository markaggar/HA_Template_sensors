# Leak Detection for Home Assistant

This sensor package + automations provides smart water leak detection using flow sensors and burst pattern recognition, including:
- **Low Flow Leak detection**
- **Leaky Toilet detection with burst session analysis**

## Features

- **Highly robust template sensors** with safe fallback defaults
- **Runtime adjustable parameters**: Easily tune burst detection settings without editing YAML!
- **Direct automation triggers**: Clean architecture without unnecessary binary sensor layers

---

## How to Adjust Toilet Burst Detection Parameters

**You can override burst detection parameters directly from Developer Tools or automations, without editing YAML!**

### Parameters

The following attributes can be updated for the `sensor.toilet_burst_session` entity:

- `burst_min_flow` (default: 0.5 GPM)
- `burst_max_flow` (default: 2.5 GPM)
- `burst_min_duration` (default: 10 seconds)
- `burst_max_duration` (default: 60 seconds)
- `burst_window` (default: 3600 seconds / 1 hour)
- `burst_min_group` (default: 4 bursts)
- `burst_group_threshold` (default: 0.3 gallons)

### Understanding burst_group_threshold

The `burst_group_threshold` parameter determines how similar burst volumes must be to group together for leak detection. This is one of the most important tuning parameters.

**Testing insights:**
- **0.1 gallons**: Too strict - normal toilet variations won't group properly
- **0.2 gallons**: Better, but may still miss some legitimate leaky toilet patterns
- **0.3 gallons**: Optimal setting - groups similar bursts while avoiding false positives
- **0.4+ gallons**: Too loose - may group unrelated bursts and cause false alarms

**Example with 0.3 threshold:**
Burst volumes: [1.35, 1.85, 1.91, 1.88, 2.12, 1.52, 1.76, 1.07, 1.21, 2.09, 1.14]

Groups found:
- **Main group**: 1.85, 1.91, 1.88, 2.12, 2.09 (5 bursts) ✅ Triggers alert
- **Secondary group**: 1.07, 1.21, 1.14 (3 bursts) - Below threshold

**Recommendation**: Start with 0.3 and adjust based on your toilet's behavior patterns.

#### Example: Override Using Developer Tools

1. Go to **Developer Tools → Services** in Home Assistant.
2. Call service: `homeassistant.update_entity`
3. Service data:
   ```yaml
   entity_id: sensor.toilet_burst_session
   ```
4. To override an attribute, use the `developer tools → states` panel:
    - Find `sensor.toilet_burst_session`
    - Click on it, and enter new values for any of the above attributes, e.g.:
      - `burst_min_flow: 0.7`
      - `burst_max_duration: 45`
      - `burst_group_threshold: 0.25`
    - Click "SET STATE" (for temporary override, will persist until next restart or state reset).

> **Tip:** For persistent changes, you can also edit the YAML, but this runtime override is usually sufficient for testing optimal values.

---

## Monitoring and Debugging

### Enhanced State Display
The `sensor.toilet_burst_session` shows the **count of bursts in the largest similar group**, making monitoring intuitive:

- **State**: Shows count (e.g., "5" means 5 similar bursts found)
- **Unit**: "count" instead of "gallons"

### Helpful Debug Attributes
- `largest_group_count`: Number of bursts in the largest similar group
- `largest_group_volumes`: List of volumes in that group (e.g., [1.85, 1.91, 1.88, 2.12, 2.09])
- `burst_min_group`: Threshold needed to trigger (default: 4)

### Understanding the Display
```yaml
State: 5                          # 5 similar bursts found
largest_group_count: 5           # Same as state
largest_group_volumes: [1.85, 1.91, 1.88, 2.12, 2.09]
burst_min_group: 4              # Threshold for alerts
```

This means: **"Found 5 similar bursts, alert will trigger!"**

---

## How It Works

### Low Flow Detection
- Filters water flow to only track rates between 0-1 GPM
- Uses statistics sensor to find minimum flow over 5 minutes
- Triggers when minimum stays above 0 (indicating persistent small leak)

### Toilet Leak Detection
- Monitors burst patterns in the 0.5-2.5 GPM range
- Groups bursts with similar volumes within configurable threshold
- Alerts when 4+ similar bursts occur within 1 hour window
- Indicates toilet valve/flapper issues causing repeated refills

### Architecture
- **Input helpers**: Track acknowledgment state
- **Template sensors**: Process burst detection and counting
- **Statistics sensor**: Analyze low flow patterns
- **Automations**: Direct triggers on sensor values (no binary sensor middleman)

---

## Installation

1. Copy the sensor package YAML to your Home Assistant configuration
2. Copy the automations YAML to your automations file
3. Update your water flow and water volume sensor. If you only have a water flow sensor, you'll need to create a helper using an Integral sensor that uses your flow sensor as a source- use "Left Reimann sum" method, precision 3, Max sub-interval of 5 seconds.
4. Update your parameters. You should flush all your toilets to see how much water they use to refill the tank and use the min and max of these measurements.
5. Go to Developer Tools/YAML and reload all YAML config
6. Test with Developer Tools to fine-tune parameters. However, you'll need to update the sensors in the package to persist after HA restarts or YAML reloads.

---

## Attribution

Please credit Mark Aggar (HassMan) with any deriviate work. Developed for robust water leak detection with real-world testing and optimization.

---
