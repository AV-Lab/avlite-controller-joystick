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

Plugin settings: `~/.config/avlite/plugin_avlite-controller-joystick.yaml`. Shipped defaults (monorepo / AVLite clone): `configs/plugin_avlite-controller-joystick.yaml`.

## Requirements

**Python:**

```bash
pip install -r requirements.txt
```

**Linux system tools** (recommended for setup and debugging):

```bash
# Debian / Ubuntu
sudo apt install evtest joystick

# Fedora
sudo dnf install evtest joystick

# Arch
sudo pacman -S evtest joystick
```

Connect the gamepad before starting AVLite. Use the **joystick** execution profile for a ready-made stack setup (`c40_controller: JoystickController`).

## Gamepad setup (Linux)

### 1. Check the device is visible

```bash
ls /dev/input/js*          # legacy joystick nodes (may be empty on some setups)
ls /dev/input/event*       # evdev nodes used by evtest
```

If nothing appears, pair or plug in the controller and check `dmesg` / your desktop gamepad settings.

### 2. Inspect axes with `evtest`

List devices and pick the gamepad (often named *Xbox*, *Wireless Controller*, *Logitech*, etc.):

```bash
sudo evtest
```

Select the event device number, then move sticks and triggers. Note which **ABS_*** codes change:

| Xbox-style input | Typical evdev code | Plugin setting |
|------------------|-------------------|----------------|
| Left stick X (steer) | `ABS_X` | `steer_axis` (default `0` in pygame) |
| Right trigger RT | `ABS_RZ` or vendor-specific | `accel_axis` (default `4`) |
| Left trigger LT | `ABS_Z` or vendor-specific | `brake_axis` (default `5`) |

Release both triggers when starting AVLite so rest positions can be auto-calibrated.

Alternative quick check:

```bash
jstest /dev/input/js0
```

### 3. Map to `device_index` and axes

AVLite uses **pygame** indices, not evtest event numbers directly. With one gamepad connected, `device_index: 0` is usually correct.

To list pygame devices:

```python
import pygame
pygame.init()
pygame.joystick.init()
for i in range(pygame.joystick.get_count()):
    j = pygame.joystick.Joystick(i)
    j.init()
    print(i, j.get_name(), "axes:", j.get_numaxes())
```

If steering or triggers are wrong, edit `steer_axis`, `accel_axis`, and `brake_axis` in `plugin_avlite-controller-joystick.yaml` (see defaults in `configs/plugin_avlite-controller-joystick.yaml`).

### 4. Permissions

If pygame sees zero devices but `evtest` works, add your user to the `input` group and re-login:

```bash
sudo usermod -aG input $USER
```

### 5. Troubleshooting

| Symptom | Things to try |
|---------|----------------|
| `no gamepad at device_index=0` | Re-run the pygame snippet above; set `device_index` to the listed index |
| Triggers always on / never reach full | Release triggers at startup; tune `accel_deadzone` / `brake_deadzone` |
| Steer reversed | Toggle `steer_invert` in plugin settings |
| Init should fail loudly | Set `fail_if_missing: true` in plugin YAML |

Default Xbox mapping (pygame): left stick X = steer (`0`), RT = accel (`4`), LT = brake (`5`). Other pads may need different axis numbers—use `evtest` / the pygame snippet to confirm.

