# Water Sessions Template Sensor Package

This package tracks "high flow" water sessions in Home Assistant and records each session **only if it exceeds a minimum volume** (default: 10 gallons) and duration (default: 60 seconds).  For instance, using this I learned that my smart hose timer (Rachio) 30 minute morning session uses 110 gallons, split into 2x15 minute sessions. Also that my morning shower used just slightly over 10 gallons!

## Uses
- Track high water use sessions through HA history and logs that are driving the majority of your water bill (e.g. irrigation, pool filling, bath/shower)
- Get notifed at the end of a high water use session (e.g. how many gallons your irrigation system or shower/bath consumed)
- Create a notification that calculates how much the water cost (multiply by the output of the Dynamic water rate sensor in this same repo)
---

## Features

- Tracks water used during high-flow events (`sensor.water_high_flow_only_rate`).
- Ignores small or short water runs (see `min_session_volume`, `min_duration`).
- All logic is in a single YAML file for easy import.

## How to Use

### 1. Place the Package

Copy `water_sessions.yaml` to a folder (e.g. `packages/Water Sessions/water_sessions.yaml`).

### 2. !include in your `configuration.yaml`

```yaml
homeassistant:
  packages: !include_dir_named packages
```
or, if you want only this file:
```yaml
!include Water Sessions/water_sessions.yaml
```

### 3. Reload

- Use **Developer Tools > YAML > Reload Template Entities** or restart HA.

### 4. Configuration

Edit thresholds at the top of `water_sessions.yaml`:
- `threshold`: minimum flow (gal/min) to count as "high flow" (default: 1.0)
- `min_duration`: minimum session length in seconds (default: 60)
- `min_volume`: minimum gallons to record a session (default: 10)

### 5. Prerequisites

- You must already have a sensor called `sensor.droplet_flow_rate` (in gallons/min).

### 6. Troubleshooting

- If no sessions are recorded, lower the thresholds or check your flow sensor.
- Confirm your integration sensor uses `unit_time: min` for gallons/minute.

---

## Attribution and Support

Created for the [HA_Template_senors](https://github.com/markaggar/HA_Template_senors) repository.

For issues or suggestions, open an issue or PR.
