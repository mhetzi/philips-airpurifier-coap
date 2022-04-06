[![hacs_badge](https://img.shields.io/badge/HACS-Custom-orange.svg?style=for-the-badge)](https://github.com/custom-components/hacs)

This is a `Local Push` integration for Philips airpurifiers.
Currently only encrypted-CoAP is implemented.


## BREAKING CHANGE:

I've switched to the branch of Denaun. This brings a number of breaking changes:
 - In your configuration, you no longer configure a `fan` with platform `philips_airpurifier_coap` but you configure `philips_airpurifier_coap` directly. See below for an example.
 - The long list of attributes is now split out to a number of sensors. If you rely on them, you need to change your frontend, scripts and automations.
 - The speed setting is now seperated from the preset modes, more inline with how fans are treated inside HA.
 
 There might be some more changes and I'll update the documentation as I come across this. The documentation below is somewhat out-of-date. I'll fix this over time.
 
 
## Word of Caution

Due to a bug in the Philips devices, this integration is rather instable. It might or might not work. Even if it seems ok at first, it might stop working after a while. Sometimes, a power cycle of the Philips device helps. Do not report this as an issue here. Nobody can help right now.

It all goes back to some reverse engineering by @rgerganov and you can read about it here: https://xakcop.com/post/ctrl-air-purifier/

Philips has recently introduced a proper API to remote control the devices. However, this only works with the Philips cloud and it is not public (yet?) but only integrates Google Home, Alexa and IFTTT. Using IFTTT with HA might be a more stable choice: https://ifttt.com/Philips_air_purifer


## Credits

 - Original reverse engineering done by @rgerganov at https://github.com/rgerganov/py-air-control
 - The base of the current integration has been done by @betaboon at https://github.com/betaboon/philips-airpurifier-coap but apparently this is not maintained anymore.
 - The rework has been done by @Denaun at https://github.com/Denaun/philips-airpurifier-coap
 - Obviously, many other people contributed, notably @Kraineff and @shexbeer


## Install

* Go to HACS -> Integrations
* Click the three dots on the top right and select `Custom Repositories`
* Enter `https://github.com/kongo09/philips-airpurifier-coap.git` as repository, select the category `Integration` and click Add
* A new custom integration shows up for installation (Philips AirPurifier CoAP) - install it
* Restart Home Assistant


## Configuration

* Go to Configuration -> Devices & Services
* Click `Add Integration`
* Search for `Philips AirPurifier` and select it
* Enter the hostname / IP address of your device
* The model type is detected automatically. You get a warning in the log, if it is not supported.

Note: `configuration.yaml` is no longer supported and your configuration is not automatically migrated. You have to start fresh.


## Supported models

- AC1214
- AC2729
- AC2889
- AC2939
- AC2958
- AC3033
- AC3059
- AC3829
- AC3858
- AC4236


## Is your model not supported yet?

You can help to get us there.

Please open an issue and provide the raw status-data for each combination of modes and speeds for your model.

To aquire those information please follow these steps:

### Prepare the environment

```sh
git clone https://github.com/kongo09/philips-airpurifier.git
cd philips-airpurifier
source aioairctrl-shell.sh
```

### Aquire raw status-data

- Use the philips-app to activate a mode or speed
- run the following command to aquire the raw data (still in the venv)

```sh
aioairctrl --host $DEVICE_IP status --json
```

## Debugging

To aquire debug-logs, add the following to your `configuration.yaml`:

```yaml
logger:
  logs:
    custom_components.philips_airpurifier_coap: debug
    coap: debug
    aioairctrl: debug
```

logs should now be available in `home-assistant.log`


## Usage

The integration provides `fan` entities for your devices which are [documented here](https://www.home-assistant.io/integrations/fan/).

It also provides a number of `sensor` entities for the air quality and other data measured by the device, as well as some diagnostic `sensor` entities with information about the filter or water fill level for humidifiers. A `switch` entity allows you to control the child lock function, should your device have one. Finally, there are some `light` entities to control the display backlight and the brightness of the air quality display and some `select` entities to set the humidification function on the devices that have that.

### Services

Unlike the original `philips_airpurifier_coap` integration, this version does not provide any additional services anymore. Everything can be controlled through the entities provided.


### Attributes

The `fan` entity has some additional attributes not captured with sensors. Specifcs depend on the model. The following list gives an overview:

| attribute |content | example |
|---|---|---|
| name: | Name of the device | bedroom |
| type: | Configured model | AC2729 |
| model_id: | Philips model ID | AC2729/10 |
| product_id: | Philips product ID | 85bc26fae62611e8a1e3061302926720 |
| device_id: | Philips device ID | 3c84c6c8123311ebb1ae8e3584d00715 |
| software_version: | Installed software version on device | 0.2.1 |
| wifi_version: | Installed WIFI version on device | AWS_Philips_AIR\@62.1 |
| error_code: | Philips error code | 49408 |
| error: | Error in clear text | no water |
| preferred_index: | State of preferred air quality index | `PM2.5`, `IAI` |
| runtime: | Time the device is running in readable text | 9 days, 10:44:41 |
