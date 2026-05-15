# Nuki BT

[![GitHub Release][releases-shield]][releases]
[![GitHub Activity][commits-shield]][commits]
[![License][license-shield]](LICENSE)

[![hacs][hacsbadge]][hacs]
![Project Maintenance][maintenance-shield]

[![Community Forum][forum-shield]][forum]

Nuki lock integration for Home Assistant.
This integration communicates directly with Nuki over Bluetooth. No need for a bridge.


## Background
- This project is based on [RaspiNukiBridge](https://github.com/regevbr/RaspiNukiBridge) by [dauden1184](https://github.com/dauden1184/) and [regevbr](https://github.com/regevbr)
- This project is heavily inspired by [kvj](https://github.com/kvj)'s [hass_nuki_ng](https://github.com/kvj/hass_nuki_ng) and [technyon](https://github.com/technyon)'s [nuki_hub](https://github.com/technyon/nuki_hub)

## Setup

{% if not installed %}

### Installation:
* Go to HACS -> Integrations
* Click the three dots on the top right and select `Custom Repositories`
* Enter `https://github.com/ronengr/hass_nuki_bt` as repository, select the category `Integration` and click Add
* A new custom integration shows up for installation (Nuki BT) - install it
* Restart Home Assistant

{% endif %}

### Configuration:
* Go to Settings -> Devices & Services
* The integration should automatically discover your Nuki lock. You Should see a new Discovered Device, just click on "Configure" to configure it.
  * If no look was discovered, and you know the Nuki's BT address, you can try to add it manually by clicking on "Add Integration"
* Select a Device Name and Client Type
* Enable pairing mode on the Nuki lock by holding down the button on the Nuki Smart Lock for 5 seconds until the LED ring is permanently glowing.
* Select "Pair device automatically"
  * It is possible to configure the device manually, if you have the pairing-information from an already paired device.
    Use this option only if you know what you are doing. This is mostly meant for development.

#### Client Type:
hass_nuki_bt can connect to the Nuki lock in 2 ways:
  * "Bridge" is the recommended way. This will cause the current Bridge to be unregistered when pairing.
  * "App" will allow you to run hass_nuki_bt alongside a Nuki Bridge, but can lead to either device missing updates.


## Compatibility

### Nuki Smart Lock Generation 1 (firmware 1.x)

Gen 1 Nuki Smart Locks running firmware 1.x are supported without needing a firmware upgrade.

The upstream `pyNukiBT` library assumes all devices return a `timezone_id` field in their config response, but Gen 1 firmware predates that field. This fork points to [`pitou3/pyNukiBT`](https://github.com/pitou3/pyNukiBT) which marks `timezone_id` as optional, fixing the crash. The `timezone_id` value will be `None` on Gen 1 devices, which has no impact on any functionality.

If you see this error in your Home Assistant logs, you are hitting this issue:

```
construct.core.StreamError: Error in path (parsing) -> payload -> timezone_id
stream read less than specified amount, expected 2, found 0
```

## Changes from upstream

This fork ([`pitou3/hass_nuki_bt`](https://github.com/pitou3/hass_nuki_bt)) includes the following fixes on top of [`ronengr/hass_nuki_bt`](https://github.com/ronengr/hass_nuki_bt):

### Gen 1 firmware support (`pyNukiBT`)
- Switched the `pyNukiBT` dependency from the PyPI release (`0.0.19`) to [`pitou3/pyNukiBT`](https://github.com/pitou3/pyNukiBT), which makes `timezone_id` optional in the Config struct so that Gen 1 locks (firmware 1.x) no longer cause a parse crash on setup.

### Improved error handling (`coordinator.py`)
- `async_wait_ready` now catches `ConstructError` (from the `construct` library) in addition to `BleakError`, so firmware-level parse failures produce a clear, single-line log message instead of a 50-line unhandled exception traceback.

### HACS direct install (`hacs.json`)
- Removed `zip_release: true` and the associated `filename` field. The upstream repo requires a GitHub Release with an attached zip artifact for HACS installation, which does not exist in this fork. Without this change HACS cannot download the integration. Removing these fields lets HACS install directly from the repository.

### Translation file fix (`translations/en.json`)
- Replaced the `en.json` symlink (which pointed to `../strings.json`) with the actual JSON content. Git symlinks become plain text files when downloaded by HACS, causing Home Assistant to fail loading the config flow with `unexpected character: line 1 column 1 (char 0)`.

## Contributions are welcome!

If you want to contribute to this please read the [Contribution guidelines](CONTRIBUTING.md)

Latest Nuki Bluetooth API spec: https://developer.nuki.io/t/bluetooth-api/27

***

[hass_nuki_bt]: https://github.com/ronengr/hass_nuki_bt
[commits-shield]: https://img.shields.io/github/commit-activity/y/ronengr/hass_nuki_bt.svg?style=for-the-badge
[commits]: https://github.com/ronengr/hass_nuki_bt/commits/main
[hacs]: https://github.com/hacs/integration
[hacsbadge]: https://img.shields.io/badge/HACS-Custom-orange.svg?style=for-the-badge
[exampleimg]: example.png
[forum-shield]: https://img.shields.io/badge/community-forum-brightgreen.svg?style=for-the-badge
[forum]: https://community.home-assistant.io/
[license-shield]: https://img.shields.io/github/license/ronengr/hass_nuki_bt.svg?style=for-the-badge
[maintenance-shield]: https://img.shields.io/badge/maintainer-%20%40ronengr-blue.svg?style=for-the-badge
[releases-shield]: https://img.shields.io/github/release/ronengr/hass_nuki_bt.svg?style=for-the-badge
[releases]: https://github.com/ronengr/hass_nuki_bt/releases
