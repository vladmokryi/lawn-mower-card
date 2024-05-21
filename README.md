# Lawn Mower Card

Based on https://github.com/denysdovhan/vacuum-card

[![npm version][npm-image]][npm-url]
[![hacs][hacs-image]][hacs-url]
[![GitHub Sponsors][gh-sponsors-image]][gh-sponsors-url]
[![Patreon][patreon-image]][patreon-url]
[![Buy Me A Coffee][buymeacoffee-image]][buymeacoffee-url]
[![Twitter][twitter-image]][twitter-url]

> Lawn Mower cleaner card for [Home Assistant][home-assistant] Lovelace UI

By default, Home Assistant does not provide any card for controlling lawn mowers. This card displays the state and allows to control your robot.

![Preview of lawn-mower-card][preview-image]

## Installing

**💡 Tip:** If you like this project, consider giving me a tip for the time I spent building this project:

<a href="https://www.buymeacoffee.com/denysdovhan" target="_blank">
  <img src="https://cdn.buymeacoffee.com/buttons/default-black.png" alt="Buy Me A Coffee" width="150px">
</a>

### HACS

This card is available in [HACS][hacs] (Home Assistant Community Store).

Just search for `Lawn Mower Card` in plugins tab.

### Manual

1. Download `lawn-mower-card.js` file from the [latest-release].
2. Put `lawn-mower-card.js` file into your `config/www` folder.
3. Add reference to `lawn-mower-card.js` in Lovelace. There's two way to do that:

   1. **Using UI:** _Configuration_ → _Lovelace Dashboards_ → _Resources Tab_ → Click Plus button → Set _Url_ as `/local/lawn-mower-card.js` → Set _Resource type_ as `JavaScript Module`.
      **Note:** If you do not see the Resources Tab, you will need to enable _Advanced Mode_ in your _User Profile_
   2. **Using YAML:** Add following code to `lovelace` section.

      ```yaml
      resources:
        - url: /local/lawn-mower-card.js
          type: module
      ```

4. Add `custom:lawn-mower-card` to Lovelace UI as any other card (using either editor or YAML configuration).

## Usage

This card can be configured using Lovelace UI editor.

1. In Lovelace UI, click 3 dots in top left corner.
2. Click _Configure UI_.
3. Click Plus button to add a new card.
4. Find _Custom: Lawn Mower Card_ in the list.
5. Choose `entity`.
6. Now you should see the preview of the card!

_Sorry, no support for `actions`, `shortcuts` and `stats` in visual config yet._

Typical example of using this card in YAML config would look like this:

```yaml
type: 'custom:lawn-mower-card'
entity: lawn-mower.lawn_mower
actions:
  start:
    service: xiaomi_miio.vacuum_clean_segment
    service_data:
      entity_id: vacuum.vacuum_cleaner
      segments: [16, 20]
stats:
  default:
    - attribute: filter_left
      unit: hours
      subtitle: Filter
    - attribute: side_brush_left
      unit: hours
      subtitle: Side brush
    - attribute: main_brush_left
      unit: hours
      subtitle: Main brush
    - attribute: sensor_dirty_left
      unit: hours
      subtitle: Sensors
  cleaning:
    - entity_id: sensor.vacuum_main_brush_left
      value_template: '{{ (value | float(0) / 3600) | round(1) }}'
      subtitle: Main brush
      unit: hours
    - attribute: cleaning_time
      unit: minutes
      subtitle: Cleaning time
shortcuts:
  - name: Clean living room
    service: script.clean_living_room
    icon: 'mdi:sofa'
  - name: Clean bedroom
    service: script.clean_bedroom
    icon: 'mdi:bed-empty'
  - name: Clean kitchen
    service: script.clean_kitchen
    icon: 'mdi:silverware-fork-knife'
```

Here is what every option means:

| Name           |   Type    | Default      | Description                                                                                               |
| -------------- | :-------: | ------------ | --------------------------------------------------------------------------------------------------------- |
| `type`         | `string`  | **Required** | `custom:lawn-mower-card`                                                                                      |
| `entity`       | `string`  | **Required** | An entity_id within the `lawn-mover` domain.                                                                  |
| `map`          | `string`  | Optional     | An entity_id within the `camera` domain, for streaming live lawn mower map.                                   |
| `map_refresh`  | `integer` | `5`          | Update interval for map camera in seconds                                                                 |
| `image`        | `string`  | `default`    | Path to image of your lawn mower. Better to have `png` or `svg`.                                      |
| `battery`      | `string`  | `default`    | An entity_id within the `battery` of your lawn mower |
| `show_name`    | `boolean` | `true`       | Show friendly name of the lawn mower.                                                                         |
| `show_status`  | `boolean` | `true`       | Show status of the lawn mower.                                                                                |
| `show_toolbar` | `boolean` | `true`       | Show toolbar with actions.                                                                                |
| `compact_view` | `boolean` | `false`      | Compact view without image.                                                                               |
| `stats`        | `object`  | Optional     | Custom per state stats for your lawn mower                                                            |
| `actions`      | `object`  | Optional     | Override default actions behavior with service invocations.                                               |
| `shortcuts`    |  `array`  | Optional     | List of shortcuts shown at the right bottom part of the card with custom actions for your lawn mower. |

### `stats` object

You can use any attribute of lawn mower or even any entity by `entity_id` to display by stats section. You can also combine `attribute` with `entity_id` to extract an attribute value of specific entity:

| Name             |   Type   | Default  | Description                                                                                          |
| ---------------- | :------: | -------- | ---------------------------------------------------------------------------------------------------- |
| `entity_id`      | `string` | Optional | An entity_id with state, i.e. `sensor.lawn-mower`.                                                       |
| `attribute`      | `string` | Optional | Attribute name of the stat, i.e. `filter_left`.                                                      |
| `value_template` | `string` | Optional | Jinja2 template returning a value. `value` variable represents the `entity_id` or `attribute` state. |
| `unit`           | `string` | Optional | Unit of measure, i.e. `hours`.                                                                       |
| `subtitle`       | `string` | Optional | Friendly name of the stat, i.e. `Filter`.                                                            |

### `actions` object

You can defined service invocations to override default actions behavior. Available actions to override are `start`, `pause`, `resume`, `stop`, `locate` and `return_to_base`.

| Name           |   Type   | Default                           | Description                                     |
| -------------- | :------: | --------------------------------- | ----------------------------------------------- |
| `service`      | `string` | Optional                          | A service to call, i.e. `script.clean_bedroom`. |
| `service_data` | `object` | `service_data` for `service` call |

### `shortcuts` object

You can defined [custom scripts][ha-scripts] for custom actions i.e cleaning specific room and add them to this card with `shortcuts` option.

| Name           |   Type   | Default                           | Description                                        |
| -------------- | :------: | --------------------------------- | -------------------------------------------------- |
| `name`         | `string` | Optional                          | Friendly name of the action, i.e. `Clean bedroom`. |
| `service`      | `string` | Optional                          | A service to call, i.e. `script.clean_bedroom`.    |
| `icon`         | `string` | Optional                          | Any icon for action button.                        |
| `service_data` | `object` | `service_data` for `service` call |

## Theming

This card can be styled by changing the values of these CSS properties (globally or per-card via [`card-mod`][card-mod]):

| Variable                    | Default value                                                    | Description                          |
| --------------------------- | ---------------------------------------------------------------- | ------------------------------------ |
| `--vc-background`           | `var(--ha-card-background, var(--card-background-color, white))` | Background of the card               |
| `--vc-primary-text-color`   | `var(--primary-text-color)`                                      | Lawn Mower name, stats values, etc       |
| `--vc-secondary-text-color` | `var(--secondary-text-color)`                                    | Status, stats units and titles, etc  |
| `--vc-icon-color`           | `var(--secondary-text-color)`                                    | Colors of icons                      |
| `--vc-toolbar-background`   | `var(--vc-background)`                                           | Background of the toolbar            |
| `--vc-toolbar-text-color`   | `var(--secondary-text-color)`                                    | Color of the toolbar texts           |
| `--vc-toolbar-icon-color`   | `var(--secondary-text-color)`                                    | Color of the toolbar icons           |
| `--vc-divider-color`        | `var(--entities-divider-color, var(--divider-color))`            | Color of dividers                    |
| `--vc-spacing`              | `10px`                                                           | Paddings and margins inside the card |

### Styling via theme

Here is an example of customization via theme. Read more in the [Frontend documentation](https://www.home-assistant.io/integrations/frontend/).

```yaml
my-custom-theme:
  vc-background: '#17A8F4'
  vc-spacing: 5px
```

### Styling via card-mod

You can use [`card-mod`][card-mod] to customize the card on per-card basis, like this:

```yaml
type: 'custom:lawn-mower-card'
style: |
  ha-card {
    --vc-background: #17A8F4;
    --vc-spacing: 5px;
  }
  ...
```

## Animations

I've added some animations for this card to make it alive. Animations are applied only for `image` property. Here's how they look like:

|              Cleaning               |                Docking                |
| :---------------------------------: | :-----------------------------------: |
| ![Cleaning anumation][cleaning-gif] | ![Returning anumation][returning-gif] |

## Supported languages

This card supports translations. Please, help to add more translations and improve existing ones. Here's a list of supported languages:

- English
- Українська (Ukrainian)
- Deutsch (German)
- Français (French)
- Italiano (Italian)
- Nederlands (Dutch)
- Polski (Polish)
- Русский (Russian)
- Español (Spanish)
- Čeština (Czech)
- Magyar (Hungarian)
- עִבְרִית (Hebrew)
- Português (Portuguese)
- Português Brasileiro (Brazilian Portuguese)
- Svenska (Swedish)
- Norsk bokmål (Norwegian)
- Norsk nynorsk (Norwegian)
- Dansk (Danish)
- 한국어 (Korean)
- Suomi (Finnish)
- Català (Catalan)
- 正體中文 (Traditional Chinese)
- Việt Nam (Vietnamese)
- Lietuvių (Lithuanian)
- Română (Romanian)
- 简体中文 (Simplified Chinese)
- 日本語 (Japanese)
- [_Your language?_][add-translation]

## Supported models

This card relies on basic lawn-mower services, like `pause`, `start`, `stop`, `return_to_base`, etc. It should work with any robot lawn mover, however I can physically test it only with my own robot lawn mower.

If this card works with your lawn mower, please open a PR and your model to the list.

- **EcoVacs** GOAT G1, GOAT G1-800, GOAT G1-2000, GOAT GX-600

- [_Your lawn mower?_][edit-readme]

## Development

Want to contribute to the project?

First of all, thanks! Check [contributing guideline](./CONTRIBUTING.md) for more information.

## Inspiration

This project is heavily inspired by:

- [MacBury Smart House][macbury-smart-house] — basically, this project is a refinement of MacBury's custom card.

Huge thanks for their ideas and efforts 👍

## License

MIT © [Denys Dovhan][denysdovhan]

<!-- Badges -->

[npm-url]: https://npmjs.org/package/lawn-mower-card
[npm-image]: https://img.shields.io/npm/v/lawn-mower-card.svg?style=flat-square
[hacs-url]: https://github.com/hacs/integration
[hacs-image]: https://img.shields.io/badge/hacs-default-orange.svg?style=flat-square
[gh-sponsors-url]: https://github.com/sponsors/denysdovhan
[gh-sponsors-image]: https://img.shields.io/github/sponsors/denysdovhan?style=flat-square
[patreon-url]: https://patreon.com/denysdovhan
[patreon-image]: https://img.shields.io/badge/support-patreon-F96854.svg?style=flat-square
[buymeacoffee-url]: https://patreon.com/denysdovhan
[buymeacoffee-image]: https://img.shields.io/badge/support-buymeacoffee-222222.svg?style=flat-square
[twitter-url]: https://twitter.com/denysdovhan
[twitter-image]: https://img.shields.io/badge/twitter-%40denysdovhan-00ACEE.svg?style=flat-square

<!-- References -->

[home-assistant]: https://www.home-assistant.io/
[hacs]: https://hacs.xyz
[preview-image]: https://github.com/denysdovhan/lawn-mower-card/assets/3459374/43808d3d-65a4-4e65-9531-4f248fa8861c
![returning](https://github.com/bhuebschen/lawn-mower-card/assets/1864448/51fbd7b7-3811-4b66-9873-852250a32efc)
![mowing](https://github.com/bhuebschen/lawn-mower-card/assets/1864448/a5b0a42d-ff87-46db-9b50-54a4f71d9107)
[latest-release]: https://github.com/denysdovhan/lawn-mower-card/releases/latest
[ha-scripts]: https://www.home-assistant.io/docs/scripts/
[edit-readme]: https://github.com/denysdovhan/lawn-mower-card/edit/master/README.md
[card-mod]: https://github.com/thomasloven/lovelace-card-mod
[add-translation]: https://github.com/denysdovhan/lawn-mower-card/blob/master/CONTRIBUTING.md#how-to-add-translation
[macbury-smart-house]: https://macbury.github.io/SmartHouse/HomeAssistant/Vacuum/
[bbbenji-card]: https://gist.github.com/bbbenji/24372e423f8669b2e6713638d8f8ceb2
[denysdovhan]: https://denysdovhan.com
