# homebridge-dyson-pure-cool

## IMPORTANT: Breaking Changes in version 2.0.0

Dyson has introduced two factor authentication for Dyson accounts. Due to the complexity of the authentication flow, you **MUST** configure credentials for each device in the config.
To obtain the credentials, please follow the instructions below.

## Supported Devices and Features

This project is a homebridge plugin for the Dyson air purifiers. Supported devices are:

- Dyson Pure Humidify+Cool (PH01)
- Dyson Pure Humidify+Cool Cryptomic (PH02)
- Dyson Pure Humidify+Cool Formaldehyde (PH04)
- Dyson Pure Cool Tower (TP04, TP07)
- Dyson Pure Cool Tower Cryptomic (TP06)
- Dyson Pure Cool Desk (DP04)
- Dyson Pure Hot+Cool (HP04)
- Dyson Pure Hot+Cool Cryptomic (HP06)
- Dyson Pure Hot+Cool Formaldehyde (HP09)
- Dyson Pure Cool Link Tower (TP02)
- Dyson Pure Cool Link Desk (DP01)
- Dyson Pure Hot+Cool Link (HP02)

All your devices are exposed as air purifiers in HomeKit, with support (also in Apple Home app) for:
- On/off
- Auto/manual
- Fan speed
- Oscillation on/off
- Relative humidity
- Current temperature (in Apple Home app only supported as separate sensor)
- Air quality (incl. PM2.5, PM10, VOC and NO2 data for devices other than DP01, TP02 and HP02)

For heating devices, a thermostat is also exposes to HomeKit with support for:
- On/Off
- Target temperature

For humidifier devices, a humidifier is also exposes to HomeKit with support for:
- On/Off
- Auto/manual
- Target relative humidity

Optionally, the following switches are exposed:
- Night mode (on/off)
- Jet Focus (on/off; DP01, TP02 and HP02 are not supported)
- Continuous Monitoring (on/off)

The plugin is optimized for usage of the Home app in iOS 13, e.g. the night mode and jet focus switches are combined in a separate settings accessory. This can be changed in the config.

## Installation

**Option 1:** Install the plugin via [config-ui-x](https://github.com/oznu/homebridge-config-ui-x):
- Search for Dyson on config-ui-x plugin screen
- Click Install on homebridge Dyson Pure Cool plugin
- Once installed you will be prompted to set the config
- Restart homebridge service and plugin should be loaded with accessories

**Option 2:** Install the plugin via npm:

```bash
npm install homebridge-dyson-pure-cool -g
```

## Retrieve Credentials

For each Dyson device that you want to use with this plugin, credentials have to be retrieved from the Dyson API.

**Step 1:** Configure the plugin (don't add devices to the devices array if you don't already have the credentials for them). If you're setting up the plugin for the first time, you can simply use the following configuration:

```json
{
    "platforms": [
        {
            "platform": "DysonPureCoolPlatform",
            "devices": [],
            "updateInterval": 60000,
            "credentialsGeneratorPort": 48000
        }
    ]
}
```

**Step 2:** Start homebridge

**Step 3:** Open a browser and navigate to `http://<IP-ADDRESS-OF-HOMEBRIDGE-HOST>:48000/` (where `<IP-ADDRESS-OF-HOMEBRIDGE-HOST>` is the IP address of the host your homebridge instance is running on).

**Step 4:** Follow the steps on the website to retrieve the credentials for all of the devices that are registered in your Dyson account.

**Step 5:** Now you can add the devices to the configuration (see below) and restart homebridge.

## Configuration

```json
{
    "platforms": [
        {
            "platform": "DysonPureCoolPlatform",
            "devices": [
                {
                    "ipAddress": "XXX.XXX.XXX.XXX",
                    "serialNumber": "XXX-EU-XXXXXXXX",
                    "credentials": "xxx...xxx",
                    "enableAutoModeWhenActivating": false,
                    "enableOscillationWhenActivating": false,
                    "isNightModeEnabled": false,
                    "isJetFocusEnabled": false,
                    "isContinuousMonitoringEnabled": false,
                    "isTemperatureSensorEnabled": false,
                    "isHumiditySensorEnabled": false,
                    "isAirQualitySensorEnabled": false,
                    "isSingleAccessoryModeEnabled": false,
                    "isFullRangeHumidity": false,
                    "isHeatingDisabled": false
                }
            ],
            "updateInterval": 60000,
            "credentialsGeneratorPort": 48000
        }
    ]
}
```

**devices**: Array of all your Dyson devices.

**ipAddress**: Local IP address of the device.

**serialNumber**: Serial number of the device.

**credentials**: The credentials for connecting to the device. They can be retrieved via the credentials generator (website), see instructions above.

**enableAutoModeWhenActivating**: If set to `true`, the Auto mode is enabled when you activate the device in the Home app. Defaults to `false`.

**enableOscillationWhenActivating**: If set to `true`, oscillation is enabled when you active the device. Defaults to `false`.

**isNightModeEnabled**: If set to `true`, a switch is exposed for the night mode. Defaults to `false`.

**isJetFocusEnabled**: If set to `true`, a switch is exposed for the jet focus. DP01, TP02 and HP02 are not supported. Defaults to `false`.

**isContinuousMonitoringEnabled**: If set to `true`, a switch is exposed for the continuous monitoring. Defaults to `false`.

**isTemperatureSensorEnabled**: If set to `true`, a separate temperature sensor is exposed. Only used for non-heating devices. If set to `false`, the temperature is added as characteristic to the air purifier (does not show up in the Apple Home app). Defaults to `false`.

**isHumiditySensorEnabled**: If set to `true`, a separate humidity sensor is exposed. Only used for non-humidifier devices. If set to `false`, the humidity is added as characteristic to the air purifier (supported in the Apple Home app). Defaults to `false`.

**isAirQualitySensorEnabled**: If set to `true`, a separate air quality sensor is exposed. If set to `false`, the air quality is added as characteristic to the air purifier (supported in the Apple Home app). Defaults to `false`.

**isSingleSensorAccessoryModeEnabled**: If set to `true`, all sensors are exposed to HomeKit in a single accessory instead of multiple accessories. Only has an effect if the previous settings for enabling sensors are set to `true`. Defaults to `false`.

**isSingleAccessoryModeEnabled**: If set to `true`, all services are exposed to HomeKit in a single accessory instead of multiple accessories. If set to `true`, the single sensor accessory mode has no effect. Use this mode if you are using a third-party HomeKit app and want all services grouped into a single accessory. Defaults to `false`.

**isFullRangeHumidity**: Only for PH01/PH02. If set to `true`, the range of the target humidity control will be from 0% to 100% instead of translating it to the allowed range (30% to 70%) of the Dyson. Defaults to `false`.

**isHeatingDisabled**: Only for HP02/HP04/HP06. If set to `true`, the heating controls are not exposed to HomeKit. Defaults to `false`.

**updateInterval** (optional): The interval (in milliseconds) at which updates of the sensors are requested from the Dyson devices. Defaults to 60 seconds.

**credentialsGeneratorPort** (optional): The port number for the (credentials generator) website. Only change this setting in case of a port collision.
