# MMM-OnScreenMenu - Simple On-Screen Menu for controlling the MagicMirror²

This is a module for the [MagicMirror²](https://github.com/MichMich/MagicMirror/).

The MMM-OnScreenMenu Module provides a small, customizable, material-style floating action button menu for controlling the MagicMirror².

View the live demo/mockup here: https://codepen.io/shbatm/pen/OggMbr/

Basic Functions:

1. Control the power to the screen (On/Off/Toggle).
2. Manage the Mirror (Shutdown/Reboot/Restart MM²/Refresh Window).
3. Module Visibility (Show/Hide/Toggle).
4. Send Notifications to Other Modules:
    * Switch Profiles in [MMM-ProfileSwitcher](https://github.com/tosti007/MMM-ProfileSwitcher)
    * Perform other module functions from a button in the menu.

The menu is designed to be controllable from multiple devices:

* Mouse Control
* Touchscreen Control
* Keyboard Control
* Bluetooth Remote via [MMM-KeyBindings](https://github.com/shbatm/MMM-KeyBindings)
* Module Notifications from other modules

## Screenshots

![](img/screenshot.jpg) ![](img/screenshot2.jpg) ![](img/demo_small.gif)
<br>*Left: Touch Mode; Middle: Hidden/Mouseover Mode; Right: Demo*


## Installation

```shell
cd ~/MagicMirror/modules
git clone https://github.com/shbatm/MMM-KeyBindings
```

## Using the module

To use this module, add the following configuration block to the modules array in the `config/config.js` file:
```js
var config = {
    modules: [
        {
            module: 'MMM-OnScreenMenu',
            position: 'bottom_right',
            /* Valid positions: 'top_right', 'top_left', 'bottom_right', 'bottom_left' */
            config: {
                touchMode: true,
                enableKeyboard: true,
                // ... see more options below
            }
        }
    ]
}
```

## Configuration options

| Option                | Description
|-----------------------|-----------
| `touchMode` | *Optional* - Enable Touch Mode for the menu.<br>When enabled, the menu button will always be visible and all tooltips will be shown when the menu is open.<br>When disabled, the menu will only appear when hovered over, clicked, or opened with a menu key.<br>*Default:* `true`
| `enableKeyboard` | *Optional* - Enable basic keyboard control.<br>Menu can be controlled with the `ContextMenu` key (usually next to `Right-Alt`), Arrow Up/Down, and Enter.<br>*Default:* `true`<br><br>*To customize keys:* manually edit the `setupMousetrap` function in `MMM-OnScreenMenu.js`.
| `enableKeyBindings` | *Optional* - Enable integration with [MMM-KeyBindings](https://github.com/shbatm/MMM-KeyBindings) for bluetooth remote and better keyboard control.  See [KeyBindings Config](#keybindings-config) below.
| `menuItems` | See [Menu Items](#menu-items) below.


### Menu Items <a name="menu-items"></a>

#### Default Menu:
To customize, add the whole block to the module config and customize. 

```js
menuItems: {
    monitorOff: { title: "Turn Off Monitor", icon: "television", source: "SERVER" },
    refresh: { title: "Refresh", icon: "recycle", source: "LOCAL" },
    restart: { title: "Restart MagicMirror", icon: "refresh", source: "ALL" },
    reboot: { title: "Reboot", icon: "spinner" },
    shutdown: { title: "Shutdown", icon: "power-off" }
},
```

#### Menu Item Configurations:

| Option | Description
| ------ | -----------
| `title` | The title to show when hovered over.
| `icon` | The [Font Awesome](http://fontawesome.io/icons/) icon to use.
| `source` | *Optional* - When to show the menu item:<br>&nbsp;&nbsp;&bull;&nbsp;&nbsp;`"ALL"` or omitted: on any instance of the MM²<br>&nbsp;&nbsp;&bull;&nbsp;&nbsp;`"SERVER"`: only the main MM² screen<br>&nbsp;&nbsp;&bull;&nbsp;&nbsp;`"LOCAL"`: only when viewing the mirror from a remote browser window.
| `name` | *Module Menu Items Only* - The module name to control
| `notification` | *Notify Menu Items Only* - The notification title to send.
| `payload` | *Notify Menu Items Only* - The notification payload to send.

#### Available Menu Items:

+ Monitor/Screen Power Control:
    * Turn Screen On - `monitorOn`
    * Turn Screen Off - `monitorOff`
    * Toggle Screen Power - `monitorToggle`
+ System Control:
    * Refresh the browser page - `refresh`
    * Reload the MM² script (if using `pm2`) - `restart`
    * Reboot the server - `reboot`
    * Shutdown the server - `shutdown`
    * *Default commands are for Debian Linux (RaspPi Jessie). Customize `reboot`, `shutdown`, `restart` commands as needed in `node_helper.js`*
+ Module Control:
    * Hide a module - `moduleHideX` 
    * Show a module - `moduleShowX`
    * Toggle module visibility - `moduleToggleX`
    * *`X` is a unique number for each item, e.g. `moduleHide1` for the first hide module item*
    * *Add module's name to the menu item definition (see Menu Item Configurations above)*
    * Examples:
        ```js
        moduleHide1: { title: "Hide Clock", icon: "minus-square", name: "clock" },
        moduleShow1: { title: "Show Clock", icon: "plus-square", name: "clock" },
        ```
+ Notifications:
    * Send a Module Notification to another module - `notifyX`
    * *`X` is a unique number for each item, e.g. `notify1` for the first notification item*
    * Example - Change profile on MMM-ProfileSwitcher:
        ```js
        notify1: {  title: "Switch to Profile", 
                    icon: "eye", 
                    notification: "CURRENT_PROFILE", 
                    payload: "DESIRED_PROFILE_NAME_HERE" },
        ```
+ Change Menu Visibitliy/Mode:
    * Toggle Touch Mode: `toggleTouchMode`
    * Move the Menu: `changeMenuPosition_<NEW_POSITION>`
    * Examples:
        ```js
        toggleTouchMode: { title: "Toggle Touch Mode", icon: "eye-slash" }
        changeMenuPosition_top_right: { title: "Move Menu to Top Right", icon: "arrows" }
        ```

#### Calling actions from another module

Any valid menu item can be called from another module by sending a notification similar to the one below. *Note:* With the exception of module actions, the item passed does NOT actually need to be in the menu; it just needs to be valid.

```js
this.sendNotification('ONSCREENMENU_PROCESS_ACTION', 'menuItemName');
```

#### KeyBindings Config <a name="keybindings-config"></a>

The default configuration for [MMM-KeyBindings](https://github.com/shbatm/MMM-KeyBindings) is below. This can be customized by adding to the normal module config.

```js
        // MMM-KeyBindings Settings
        enableKeyBindings: false,
        keyBindingsMode: "OSM",
        keyBindings: {
            Up: "ArrowUp",
            Down: "ArrowDown",
            Select: "Enter",
            Close: "Return",
            Menu: "Menu"
        },
        kbMultiInstance: true,
        keyBindingsTakeFocus: "Menu"
```

## Development Path

This module is an ever-continuing work in progress, if you would like to request a feature or report a bug, please see the [Issues](https://github.com/shbatm/MMM-OnScreenMenu/issues).