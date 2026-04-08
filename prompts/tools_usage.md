# Tools Usage Guide: GCU Gimbal

## `set_gimbal_angles(yaw, pitch, roll)`
Set target angles for gimbal axes.

**Parameters**:
- `yaw`: Pan angle in radians (-3.14 to 3.14)
- `pitch`: Tilt angle in radians (-1.57 to 1.57)
- `roll`: Roll angle in radians (-1.57 to 1.57)

---

## `get_gimbal_state()`
Get current gimbal angles and status.

**Returns**: Current yaw, pitch, roll in radians.

---

## Stabilization Control

### `enable_stabilization()`
Enable automatic stabilization.

### `disable_stabilization()`
Disable automatic stabilization.
