# Profile library

Powercalc ships with a huge library of predefined power profiles for different devices.
You can find the list of supported devices on the dedicated [library viewer](https://library.powercalc.nl).
This library will keep extending by the effort of community users.

At startup Powercalc will scan your HA installation and tries to match if any of them are in the library,
when found it will provide a discovery flow for you to setup. More info about the discovery process can be found [here](#discovery).
You can also setup Powercalc sensors for a entity manually, see [Virtual power library](../sensor-types/virtual-power-library.md).

Starting from version 1.12.0 all the power profiles are moved out of the component and are downloaded from the internet on demand.
This way we can roll out updates to the library without the need to update the component.
Also you only need to download the profiles you actually use, saving bandwidth and storage.

!!! note

    The library is hosted on GitHub, so you need an internet connection to download the profiles.
    When you prefer to have full local control, you can disable the library in the configuration. See [Disable download feature].

For more information about the library structure, See [structure](structure.md).

To contribute new profiles see the [measure](../contributing/measure.md) section.

More information about how to setup specific device types can be found in the [device types](device-types/index.md) section.

## Custom models

If you have a device that is not in the library, you can create a custom model.
When it can be useful to share this model with the community, please consider submitting it to the library.

Custom models are stored in the `config/powercalc/profiles` directory.

You'll need to apply the same structure as the core library, with a subdirectory for the manufacturer and model id.

For example:

```text
config/
├-powercalc/
│ └-profiles/
│   ├-tp-link/
│   │ ├─HS100/
│   │ │ ├─model.json
│   │ └─...
```

!!! note

    Custom models are loaded before the built-in library models, so you can override the library models by creating a custom model with the same manufacturer and model id.

## Disable download feature

If you want to disable the download feature, you can set the use the `disable_library_download` option in the configuration.

```yaml
powercalc:
  disable_library_download: true
```

This will prevent the component from downloading the library profiles from the internet.

You will have to manage the library profiles yourself, by downloading them from the GitHub repository and placing them in the `config/powercalc/profiles` directory.

## Discovery

During startup, Powercalc will scan your Home Assistant installation for entities and devices that match the library profiles.
Only entities which have a device attached will be considered for discovery.
Device information can be viewed at the top left corner of the device page in the Home Assistant UI, or in `config/.storage/core.device_registry`.

Each device in HA has the following properties:

- manufacturer
- model
- model_id (optional)

This information is tried to match again the built-in library and your custom model directory.
When a match in custom models is found, the built-in library loading is skipped.

starting with the manufacturer. Both manufacturer name and aliases are matched.
If a match is found, the model id is matched. Both the directory_name (model id) and additional aliases (from model.json) are matched.

You can enable [debug logging](../troubleshooting/debug-logging.md) to debug the matching process.

## Updating the library

The library is updated automatically in the background under the following conditions.
Once the update process is complete, a discovery routine will be initiated to potentially discover new supported entities on your system.

- *At Each Startup*: The library checks for updates whenever the system is started.
- *Every Two Hours*: Updates are scheduled to run automatically every two hours.
- *Manual Trigger*: Updates can also be initiated manually by invoking the action Powercalc: update library.

This ensures that you always have the latest power profiles available, without the need to restart HA.
