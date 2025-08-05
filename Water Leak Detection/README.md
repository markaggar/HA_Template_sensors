# Water Leak & Leaky Toilet Detection for Home Assistant

This package provides robust automations and helper entities to detect:
- Slow water leaks after high water use
- Persistent slow leaks
- Repeated short moderate bursts typical of leaky toilet valves/flappers

You’ll receive actionable notifications (with acknowledgement buttons) and rolling-window logic to prevent notification spam.

---

## Features

- **Leak Detection After High Use:** Notifies if water flow stays low after a period of high use.
- **Slow Leak Detection:** Notifies if slow, nonzero flow persists.
- **Leaky Toilet Detection:** Uses a rolling hourly window to detect 4+ short, moderate bursts and notifies you.
- **Single Notification per Event:** Notifications use a tag to update in place instead of spamming.
- **Acknowledgement Buttons:** Clear alerts and reset detection with a tap.

---

## Installation

### 1. **Full Package Method (Recommended)**

1. Place `water_leak_detection.yaml` in your Home Assistant `config/packages` directory.
2. In your `configuration.yaml`, add:
    ```yaml
    homeassistant:
      packages: !include_dir_named packages
    ```
3. **Reload the Full YAML.**

- This method includes all sensors, helpers, and automations.  

- In Developer Tools/YAML, click 'All YAML CONFIGURATION'.  Note this can take a few minutes and cause HA to run slowly while all YAML is reloaded.

**No further setup required!**

---

### 2. **Automations Only (For Tracing or Custom Use)**

1. Copy the `automations.yaml` block into your existing `automations.yaml`.
2. Reload automations or restart Home Assistant.
3. **You must manually create the required helpers** (*see entities defined in the package*) or delete the automations from the `water_leak_detection.yaml` package before you save it in the packages folder.

**For tracing:**  
- Open "Settings → Automations & Scenes → Automations".
- Find the imported automations and click "Trace" on any of them.

---

## Customization

### Notification Service

By default, notifications are sent to `notify.all_family` (which you must have defined in your HA setup, e.g. as a mobile group or similar).

To use a different notification service, **replace all instances of**:
```yaml
service: notify.all_family
```
with your desired service, e.g. `notify.mobile_app_your_phone`.

### Notification Channel & Priority

These automations use actionable, high-priority notifications with a custom channel.  
You can change these values in the `data:` block in each notification:

```yaml
data:
  tag: leaky_toilet_alert
  priority: high
  channel: alarm
  actions:
    - action: "ACKNOWLEDGE_LEAKY_TOILET"
      title: "Acknowledge"
```

- **priority:** `high` (can be changed to `default`, `critical`, etc. depending on your mobile app)
- **channel:** `alarm` (used for Android/iOS notification channel/routing)
- **tag:** ensures only one notification is shown per event

---

## Modifying Leak Detection Sensitivity

- **Leak detection thresholds** (e.g. flow rates, durations) are set in the `binary_sensor:` and `automation:` sections.
- To adjust how sensitive the detection is, modify values in these templates, e.g. for slow flow, high use, burst detection windows, etc.

---

## Entities Created

- **input_booleans:** For detected states and acknowledgements
- **input_buttons:** For manual acknowledgement (useful for dashboards)
- **input_text:** For rolling-window burst timestamp storage
- **template binary_sensors:** For high use, slow flow, zero flow, and suspected toilet bursts

---

## Troubleshooting

- If notifications do not appear, verify your notification service is set up and working.
- If you get YAML errors, check indentation or copy-paste issues.
- If using automations only, ensure all helpers (inputs, sensors) are created manually.

---

## Support & Contributions

Feel free to open issues or pull requests for improvements, new features, or bug fixes!

---

## Credits

This package is based on real-world water monitoring and HA best practices, tailored for reliability and minimal notification fatigue.
