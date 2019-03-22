# Lovelace simple thermostat card

A different take on the thermostat card for Home Assistant Lovelace UI.
The aim is to provide a card with simpler interactions that are easier to use and take up less space, as well as provide more modularity to tweak the card. For example the abiltity to embed sensor values that are relevant to your thermostat (like humidity, energy usage, hours on +++).

![Example thermostat](https://github.com/nervetattoo/simple-thermostat/raw/master/thermostat-card.png)

## Requirements

Home Assistant 0.84 or higher

## Installation

### Installation _(Manual)_

1. Download the `simple-thermostat.js` from the [latest release](https://github.com/nervetattoo/simple-thermostat/releases/latest) and store it in your `configuration/www` folder.
   _Previously you could download the source file from Github but starting from the 0.14 release that is no longer possible. If you try to do so it will crash_
2. Configure Lovelace to load the card:

```yaml
resources:
  - url: /local/simple-thermostat.js?v=1
    type: module
```

### Installation and tracking with `custom updater` _(Recommended)_

1. Make sure you've the [custom_updater](https://github.com/custom-components/custom_updater) component installed and working.
2. Configure Lovelace to load the card:.

```yaml
resources:
  - url: /customcards/github/nervetattoo/simple-thermostat.js?track=true
    type: module
```

3. Run the service `custom_updater.check_all` or click the "CHECK" button if you use the tracker-card.
4. Refresh the website.

## Available configuration options:

- `entity` _string_: The thermostat entity id **required**
- `name` _string|false_: Override the card name, or disable showing a name at all. Default is to use the friendly_name of the thermostat provided
- `icon` _string|object_: Show an icon next to the card name. You can also pass an object to specify state-specific icons. Defaults state-specific icons radiator/radiator-disabled/snowflake
  - `idle`: _string_: Use this icon for state idle
  - `heat`: _string_ Use this icon for state heat
  - `cool`: _string_ Use this icon for state cool
- `step_size` _number_: Override the default 0.5 step size for increasing/decreasing the temperature
- `hide` _object_: Control specifically information fields to show. Defaults to showing everything
  - `temperature`: _bool_ (Default to `false`)
  - `state`: _bool_ (Default to `false`)
  - ~`mode`: _bool_ (Default to `false`)~ **DEPRECATED IN 0.19**
  - `away`: _bool_ (Default to `true`)
- `modes` _object|bool_ (From 0.19)
  - `{mode_key}` _object|bool_: The key of the mode to define
    - `include` _bool_: Whether to include this mode in the list or not
    - `name` _string|bool_: Specify a custom name or set to `false` to show only the icon
    - `icon` _string|bool_: Specify a custom icon or set to `false` to not show icon
- `sensors` _array_
  - `entity` _string_: A sensor value entity id
  - `name` _string_: Specify a sensor name to use instead of the default friendly_name
  - `attribute` _string_: The key for an attribute provided by the main entity (for example `min_temp`)
  - `unit` _string_: When specifying an attribute you can manually set the unit to display

## A note on modes

Parsing of YAML will read modes named `on` and `off` to `true` and `false` unless you wrap them in ", probably breaking what you wanted to do. If you need to tweak a mode with these names you need to do it like this:

```yaml
modes:
  off: will not work
  "off": works
```

## Example usage:

```yaml
cards:
  - type: custom:simple-thermostat
    entity: climate.my_room
    step_size: 1
    sensors:
      - entity: sensor.fibaro_system_fgwpef_wall_plug_gen5_energy
      - entity: sensor.fibaro_system_fgwpef_wall_plug_gen5_power
        name: Energy today
      - attribute: min_temp
        name: Min temp
    modes:
      some_mode: false
      another_mode:
        include: false
      'off':
        name: Make it cold
        icon: false
      'on':
        name: false
        icon: mdi:whitewalker
```
