# avlite-controller-joystick

Xbox-style gamepad controller for AVLite. Registers `JoystickController` as a control strategy.

**Plugin name:** `avlite-controller-joystick`

## Install

```bash
git clone https://github.com/AV-Lab/avlite-controller-joystick \
  ~/.local/share/avlite/plugins/avlite-controller-joystick
```

Requires [AVLite](https://github.com/AV-Lab/avlite) core installed.

Monorepo path: `related-repos/avlite-controller-joystick`

## Configuration

```yaml
c40_community_plugins:
  avlite-controller-joystick: avlite-controller-joystick   # or related-repos/avlite-controller-joystick
c40_controller: JoystickController
```

Plugin settings: `~/.config/avlite/plugin_avlite-controller-joystick.yaml` (defaults in `config/default.yaml`).

## Requirements

```bash
pip install -r requirements.txt
```

Connect a gamepad before starting the stack. Use the **joystick** execution profile in AVLite for a ready-made setup.
