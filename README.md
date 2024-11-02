# Space Station ZMK Module

## Instructions

Add this repository to your existing `config/west.yml` as shown below. (No idea what that means? [Make a new repo from this template first](https://github.com/zmkfirmware/unified-zmk-config-template), and then you'll have something to edit.)

```yaml
manifest:
  remotes:
    - name: zmkfirmware
      url-base: https://github.com/zmkfirmware
    - name: upset           # You can name this whatever you like; just make sure the "remote" below matches.
      url-base: https://github.com/jeremyisyes
  projects:
    - name: zmk
      remote: zmkfirmware
      revision: main
      import: app/west.yml
    - name: space-station-zmk-module  # Add the name of the repository as a project.
      remote: upset
      revision: main                  # This is the name of the branch you want to use.
  self:
    path: config
```

After updating your `west` manifest, copy [`boards/shields/space_station/space_station.keymap`](boards/shields/space_station/space_station.keymap) into your `config` folder for later modification. Don't forget to [edit your `build.yaml`](https://zmk.dev/docs/customization#building-additional-keyboards) to include the new shield(s):

```yaml
---
include:
  - board: seeeduino_xiao_ble
    shield: space_station
  # Including the settings_reset shield is optional, but recommended.
  - board: seeeduino_xiao_ble
    shield: settings_reset
```

### Optional

- Copy [`config/space_station.json`](config/space_station.json) into your `config/` folder in order to use [nickcoutsous's Keymap Editor](https://nickcoutsous.github.io/keymap-editor).
- Add a `space_station.conf` to your `config/` folder for [any Kconfig customizations](https://zmk.dev/docs/config#kconfig-files)
---

### Layout Options and Your Keymap

Since Space Station has multiple layout options, you can switch to one of them in your keymap to facilitate editing. There are two mutually-exclusive ways to configure this:

- A **`matrix-transform`**. The selected layout will be shown in [Keymap Editor](https://nickcoutsos.github.io/keymap-editor/).
- A **`physical-layout`** will be used by [ZMK Studio](https://github.com/zmkfirmware/zmk-studio). The layout can be changed from the UI.

Check [`boards/shields/space_station/space_station.overlay`](boards/shields/space_station/space_station.overlay) for the list of options. Select the desired layout by modifying the `chosen` node; this can be done at the top of your keymap.

```dts
chosen {
    zmk,physical-layout = &three_key_layout; /* 7u spacebar */
};
```

This repository currently defaults to `physical-layout`. To use `matrix-transform` instead, be sure to delete the `physical-layout` property from the `chosen` node:

```dts
chosen {
    /delete-property/ zmk,physical-layout;
    zmk,matrix-transform = &three_key_transform;
};
```

## Common Questions/Problems

### There was an error and the build didn't finish.

#### There is probably an error in your keymap.

[Clicking into the details of the action](https://docs.github.com/en/actions/quickstart#viewing-your-workflow-results) will show the error log. While very long, this log will often show the exact line number causing the problem in your keymap so it is worth reading closely. Check this against the last changes you made.

Keep in mind that each ZMK "keycode" is made up of a [behavior](https://zmk.dev/docs/features/keymaps#behaviors) which always has an `&` in front (e.g. `&kp` for a **k**ey **p**ress), followed by any details for that behavior.
Resetting to bootloader is just [`&bootloader`](https://zmk.dev/docs/behaviors/reset) while the letter `Z` is [`&kp Z`](https://zmk.dev/docs/behaviors/key-press). And "select Bluetooth profile #1" is [`&bt BT_SEL 1`](https://zmk.dev/docs/behaviors/bluetooth)). Read [the docs](https://zmk.dev/docs/) closely.

### After disconnecting the keyboard from Bluetooth, it won't reconnect.

#### Forget the Bluetooth connection on both the keyboard and the host, then try re-pairing.

If you don't remember which profile on the keyboard was used to connect to a specific host, you may have to clear them all. [See ZMK's documentation for more information](https://zmk.dev/docs/behaviors/bluetooth#bluetooth-pairing-and-profiles).

As a last resort, try flashing the Xiao BLE's `settings_reset` firmware; this will force-clear everything.

## Further Troubleshooting Resources

- [ZMK's troubleshooting page](https://zmk.dev/docs/troubleshooting)
- Ask the ZMK experts in [ZMK's Discord](https://zmk.dev/community/discord/invite)
- Search for help in [the 40% Discord](https://discord.gg/40percent)
