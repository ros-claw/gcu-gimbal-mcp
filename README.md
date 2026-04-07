# gcu-gimbal-mcp

ROSClaw MCP Server for **Xianfei GCU Series Camera Gimbal** (先飞技术 GCU 双光吊舱云台) via Serial protocol.

Part of the [ROSClaw](https://github.com/ros-claw) Embodied Intelligence Operating System.

## Overview

This MCP server enables LLM agents to control **Xianfei Technology (南京先飞技术)** GCU series camera gimbals through the Model Context Protocol. The GCU (Gimbal Control Unit) series includes the **Z-2Mini** and **A5** dual-camera gimbal models featuring visible light + thermal imaging cameras.

The server communicates via RS-232/USB serial using the proprietary GCU binary protocol with CRC-16 error checking.

```
LLM Agent  ──MCP──►  gcu-gimbal-mcp  ──Serial──►  GCU Gimbal (Z-2Mini/A5)
```

### Manufacturer Information

| Item | Details |
|------|---------|
| **Manufacturer** | Xianfei Technology (先飞技术) |
| **Location** | Nanjing, China (中国南京) |
| **Product Series** | GCU (Gimbal Control Unit) |
| **Supported Models** | Z-2Mini, A5 Gimbal |
| **Product Type** | Dual-camera payload (可见光+热成像双光吊舱) |

## SDK Information

| Property | Value |
|----------|-------|
| **SDK Name** | GCU Gimbal SDK (先飞GCU云台SDK) |
| **SDK Version** | V2.0.6 |
| **Protocol** | Binary Serial (Proprietary) |
| **Protocol Document** | [GCU私有通信协议-XF(A5)V2.0.6.pdf](./docs/GCU私有通信协议-XF(A5)V2.0.6.pdf) |
| **Manufacturer** | Xianfei Technology (先飞技术) |
| **Product Series** | GCU (Gimbal Control Unit) |
| **Source** | Proprietary |
| **License** | Proprietary |
| **Generated** | 2026-04-07 |

## Hardware Specification

### Product Information

| Specification | Value |
|--------------|-------|
| **Product Name** | GCU Dual-Camera Gimbal (GCU双光吊舱云台) |
| **Manufacturer** | Xianfei Technology (先飞技术), Nanjing, China |
| **Supported Models** | Z-2Mini, A5 |
| **Camera Type** | Visible Light + Thermal Imaging (可见光+热成像) |
| **Application** | UAV Payload, Robotics, Surveillance |

### Mechanical Specifications

| Specification | Value |
|--------------|-------|
| **Axes** | 3-axis (Yaw, Pitch, Roll) |
| **Rotation Range** | Yaw: ±170°, Pitch: -90° to +30°, Roll: ±45° |
| **Max Speed** | ±150°/s (pitch/yaw) |
| **Stabilization** | 3-axis mechanical stabilization |

### Communication Interface

| Specification | Value |
|--------------|-------|
| **Protocol** | GCU Binary Serial Protocol |
| **Serial Config** | 115200 bps, 8 data bits, 1 stop bit, No parity |
| **Frame Header** | 0xA8 0xE5 (send) / 0x8A 0x5E (receive) |
| **Checksum** | CRC-16 |
| **Command Frequency** | Up to 50Hz |

### Supported Models

| Model | Description | Weight | Application |
|-------|-------------|--------|-------------|
| **Z-2Mini** | Compact dual-camera gimbal | ~300g | Small UAVs, robotics |
| **A5** | Advanced thermal+visible camera system | ~500g | Professional UAVs |

### Camera Specifications

| Feature | Visible Camera | Thermal Camera |
|---------|---------------|----------------|
| Resolution | 1920x1080 | 640x512 |
| Zoom | Optical + Digital | Fixed |
| Night Vision | No | Yes (IR mode) |
| Recording | Yes | Yes |

## Features

- **3-axis control**: Yaw, pitch, roll with degree precision
- **Multiple modes**: Stabilization, follow, FPV, lock
- **Camera control**: Photo capture, video record, zoom, focus
- **Night vision**: IR mode with multiple sensitivity levels
- **Real-time feedback**: Position, temperature, velocity at 50Hz
- **CRC-16 checksum**: Reliable serial communication
- **Auto-reconnect**: Handles serial disconnections gracefully

## Installation

```bash
# Clone
git clone https://github.com/ros-claw/gcu-gimbal-mcp.git
cd gcu-gimbal-mcp

# Install with uv (recommended)
uv venv --python python3.10
source .venv/bin/activate
uv pip install -e .

# Or with pip
pip install -e .
```

### Serial Port Permissions (Linux)

```bash
sudo usermod -a -G dialout $USER
# Log out and back in
```

## Quick Start

### Run as MCP Server

```bash
# stdio transport
python src/gimbal_mcp_server.py
```

### Claude Desktop Configuration

```json
{
  "mcpServers": {
    "rosclaw-gimbal": {
      "command": "python",
      "args": ["/path/to/rosclaw-gimbal-mcp/src/gimbal_mcp_server.py"],
      "transportType": "stdio",
      "description": "GCU Gimbal V2.0.6",
      "sdk_version": "V2.0.6",
      "sdk_source": "Proprietary"
    }
  }
}
```

### MCP Inspector (Testing)

```bash
mcp dev src/gimbal_mcp_server.py
```

## Available Tools

| Tool | Description |
|------|-------------|
| `connect_gimbal` | Connect to gimbal on serial port |
| `disconnect_gimbal` | Disconnect from gimbal |
| `set_mode` | Set control mode (stabilize/follow/fpv/lock) |
| `rotate` | Rotate with angular velocity |
| `set_euler_angles` | Go to absolute yaw/pitch/roll |
| `reset_gimbal` | Return to center position |
| `calibrate` | Calibrate gimbal IMU |
| `take_photo` | Trigger camera shutter |
| `toggle_record` | Toggle video recording |
| `zoom` | Control zoom (in/out/stop) |
| `set_zoom_level` | Set specific zoom level |
| `focus` | Trigger autofocus |
| `set_night_vision` | Enable IR/night vision mode |
| `set_osd` | Enable/disable on-screen display |
| `set_illumination` | Set LED illumination brightness |
| `set_ranging` | Enable laser rangefinder |
| `stop_rotation` | Emergency stop rotation |
| `demo_scan` | Perform scan demonstration |

## Available Resources

| Resource | Description |
|----------|-------------|
| `gimbal://status` | Current angles, mode, temperature |
| `gimbal://connection` | Serial connection status |
| `gimbal://sdk_info` | SDK metadata and version |

## Work Modes

| Mode | Code | Description |
|------|------|-------------|
| `angle_lock` | 0x11 | Pointing lock mode (角速度控制) |
| `follow` | 0x12 | Pointing follow mode |
| `euler` | 0x14 | Euler angle control mode |
| `fpv` | 0x1C | First-person view mode |
| `top_down` | 0x13 | Top-down shooting mode |

## Angle Limits

| Axis | Range | Safety Level |
|------|-------|--------------|
| Yaw | -170° to +170° | 🟠 HIGH |
| Pitch | -90° to +30° | 🟠 HIGH |
| Roll | -45° to +45° | 🟡 MEDIUM |

### Speed Limits

| Axis | Max Speed | Safety Level |
|------|-----------|--------------|
| Pitch | ±150°/s | 🔴 CRITICAL |
| Yaw | ±150°/s | 🔴 CRITICAL |
| Roll | ±150°/s | 🔴 CRITICAL |

## Serial Protocol

The GCU protocol uses binary frames:

```
[0xA8][0xE5][LEN_L][LEN_H][VER][DATA...][CRC16_L][CRC16_H]
```

### Frame Structure

| Field | Size | Description |
|-------|------|-------------|
| Header | 2 bytes | 0xA8 0xE5 (send) / 0x8A 0x5E (receive) |
| Length | 2 bytes | Packet length (little-endian) |
| Version | 1 byte | Protocol version (0x01) |
| Data | Variable | Command + parameters |
| CRC16 | 2 bytes | CRC-16 checksum (big-endian) |

### Command Codes

| Command | Code | Description |
|---------|------|-------------|
| `CMD_CALIBRATION` | 0x01 | IMU calibration |
| `CMD_RESET` | 0x03 | Reset to center |
| `CMD_PHOTO` | 0x20 | Take photo |
| `CMD_RECORD` | 0x21 | Toggle recording |
| `CMD_ZOOM_IN` | 0x22 | Zoom in |
| `CMD_ZOOM_OUT` | 0x23 | Zoom out |
| `CMD_ZOOM_STOP` | 0x24 | Stop zoom |
| `CMD_ZOOM_SET` | 0x25 | Set zoom level |
| `CMD_FOCUS` | 0x26 | Autofocus |
| `CMD_NIGHT_VISION` | 0x2B | IR/night vision |
| `CMD_OSD` | 0x73 | On-screen display |
| `CMD_ILLUMINATION` | 0x80 | LED brightness |
| `CMD_RANGING` | 0x81 | Laser rangefinder |

## Safety Information

**WARNING:** This MCP server controls a motorized gimbal. Improper use can cause:
- Equipment damage
- Motor overheating
- Mechanical wear

### Safety Features

| Feature | Description |
|---------|-------------|
| **Speed Limits** | ±150°/s enforced in software |
| **Angle Limits** | Mechanical limits enforced |
| **CRC Checksum** | All packets validated |
| **Emergency Stop** | `stop_rotation()` immediate halt |

### Safety Levels

| Level | Color | Description | Example |
|-------|-------|-------------|---------|
| **CRITICAL** | 🔴 | Immediate danger | Speed > 150°/s |
| **HIGH** | 🟠 | Potential damage | Angle near limit |
| **MEDIUM** | 🟡 | Caution needed | Extended operation |
| **LOW** | 🟢 | Informational | Status check |

### Emergency Procedures

1. **Immediate Stop**: Use `stop_rotation()` tool
2. **Power Off**: Disconnect power if mechanical issue
3. **Reset**: Use `reset_gimbal()` to return to center

## Error Handling

### Error Codes

| Code | Name | Severity | Description |
|------|------|----------|-------------|
| -1 | CONNECTION_FAILED | 🟠 error | Serial port not accessible |
| -2 | TIMEOUT | 🟠 error | Command response timeout |
| -3 | INVALID_PARAMETER | 🟠 error | Invalid angle or speed |
| -4 | SAFETY_VIOLATION | 🔴 critical | Exceeds speed/angle limits |
| -5 | NOT_INITIALIZED | 🟠 error | Not connected |
| -6 | CRC_ERROR | 🟠 error | Checksum mismatch |

### Troubleshooting

| Issue | Possible Cause | Solution |
|-------|---------------|----------|
| Connection failed | Port not found | Check COM port in Device Manager |
| Connection failed | Permission denied | Add user to `dialout` group |
| Command no response | Wrong baudrate | Verify 115200 setting |
| Jerky movement | EMI interference | Use shielded cable |
| CRC errors | Cable too long | Max 3m for USB-serial |

## Finding Your Serial Port

```bash
# Linux
ls /dev/ttyUSB* /dev/ttyACM*

# Windows
# Use Device Manager → Ports (COM & LPT)
# Common: COM3, COM4, COM8

# macOS
ls /dev/tty.usbserial-*
```

## Dependencies

- Python 3.10+
- `mcp[fastmcp]` — MCP framework
- `pyserial` — Serial communication

## Architecture

```
gimbal_mcp_server.py
├── SDK_METADATA          — SDK version and protocol info
├── GimbalState          — Gimbal state dataclass
├── GimbalSerialBridge   — Serial communication bridge
│   ├── connect()        — Open serial port
│   ├── _calculate_crc16() — CRC-16 implementation
│   ├── _build_control_packet() — Build binary frame
│   ├── send_packet()    — Thread-safe send
│   └── _recv_loop()     — Background 50Hz reader
└── MCP Tools           — FastMCP tool definitions
```

## References

- **GCU Protocol**: [GCU私有通信协议-XF(A5)V2.0.6.pdf](./docs/GCU私有通信协议-XF(A5)V2.0.6.pdf)
- **User Manual**: [Z-2Mini用户手册-XF(A5)V1.4.pdf](./docs/Z-2Mini用户手册-XF(A5)V1.4.pdf)
- **MCP Protocol**: https://modelcontextprotocol.io/
- **PySerial**: https://pyserial.readthedocs.io/

## License

MIT License — See [LICENSE](LICENSE)

**Note**: The GCU protocol is proprietary to Xianfei Technology. This MCP server is an independent implementation based on the published protocol specification.

## Part of ROSClaw

- [rosclaw-g1-dds-mcp](https://github.com/ros-claw/rosclaw-g1-dds-mcp) — Unitree G1 (DDS)
- [rosclaw-ur-ros2-mcp](https://github.com/ros-claw/rosclaw-ur-ros2-mcp) — UR5 arm (ROS2)
- [rosclaw-gimbal-mcp](https://github.com/ros-claw/rosclaw-gimbal-mcp) — GCU Gimbal (Serial)
- [rosclaw-ur-rtde-mcp](https://github.com/ros-claw/rosclaw-ur-rtde-mcp) — UR5 via RTDE

---

**Generated by ROSClaw SDK-to-MCP Transformer**

*SDK Version: GCU V2.0.6 | Protocol: Binary Serial | CRC-16*
