# gcu-gimbal-mcp

ROSClaw MCP Server for **Xianfei GCU Series Camera Gimbal** (先飞技术 GCU 双光吊舱云台) via Serial protocol.

Part of the [ROSClaw](https://github.com/ros-claw) Embodied Intelligence Operating System.

## 概述

This MCP server enables LLM agents to control **Xianfei Technology (南京先飞技术)** GCU series camera gimbals through the Model Context Protocol. The GCU (Gimbal Control Unit) series includes the **Z-2Mini** and **A5** dual-camera gimbal models featuring visible light + thermal imaging cameras.

The server communicates via RS-232/USB serial using the proprietary GCU binary protocol with CRC-16 error checking.

```
LLM Agent  ──MCP──►  gcu-gimbal-mcp  ──Serial──►  GCU Gimbal (Z-2Mini/A5)
```

### 制造商信息

| 项目 | 详情 |
|------|---------|
| **制造商** | Xianfei Technology (先飞技术) |
| **位置** | Nanjing, China (中国南京) |
| **产品系列** | GCU (Gimbal Control Unit) |
| **支持型号** | Z-2Mini, A5 Gimbal |
| **产品类型** | Dual-camera payload (可见光+热成像双光吊舱) |

## SDK 信息

| 属性 | 值 |
|----------|-------|
| **SDK 名称** | GCU Gimbal SDK (先飞GCU云台SDK) |
| **SDK 版本** | V2.0.6 |
| **协议** | Binary Serial (Proprietary) |
| **协议文档** | [GCU私有通信协议-XF(A5)V2.0.6.pdf](./docs/GCU私有通信协议-XF(A5)V2.0.6.pdf) |
| **制造商** | Xianfei Technology (先飞技术) |
| **产品系列** | GCU (Gimbal Control Unit) |
| **来源** | Proprietary |
| **许可证** | Proprietary |
| **生成日期** | 2026-04-07 |

## 硬件规格

### 产品信息

| 规格 | 值 |
|--------------|-------|
| **产品名称** | GCU Dual-Camera Gimbal (GCU双光吊舱云台) |
| **制造商** | Xianfei Technology (先飞技术), Nanjing, China |
| **支持型号** | Z-2Mini, A5 |
| **相机类型** | Visible Light + Thermal Imaging (可见光+热成像) |
| **应用** | UAV Payload, Robotics, Surveillance |

### 机械规格

| 规格 | 值 |
|--------------|-------|
| **轴数** | 3-axis (Yaw, Pitch, Roll) |
| **旋转范围** | Yaw: ±170°, Pitch: -90° to +30°, Roll: ±45° |
| **最大速度** | ±150°/s (pitch/yaw) |
| **稳定** | 3-axis mechanical stabilization |

### 通信接口

| 规格 | 值 |
|--------------|-------|
| **协议** | GCU Binary Serial Protocol |
| **串口配置** | 115200 bps, 8 data bits, 1 stop bit, No parity |
| **帧头** | 0xA8 0xE5 (send) / 0x8A 0x5E (receive) |
| **校验** | CRC-16 |
| **命令频率** | Up to 50Hz |

### 支持型号

| 型号 | 描述 | 重量 | 应用 |
|-------|-------------|--------|----------|
| **Z-2Mini** | Compact dual-camera gimbal | ~300g | Small UAVs, robotics |
| **A5** | Advanced thermal+visible camera system | ~500g | Professional UAVs |

### 相机规格

| 特性 | 可见光相机 | 热成像相机 |
|---------|---------------|----------------|
| 分辨率 | 1920x1080 | 640x512 |
| 变焦 | Optical + Digital | Fixed |
| 夜视 | No | Yes (IR mode) |
| 录制 | Yes | Yes |

## 功能特性

- **3-axis control**: Yaw, pitch, roll with degree precision
- **Multiple modes**: Stabilization, follow, FPV, lock
- **Camera control**: Photo capture, video record, zoom, focus
- **Night vision**: IR mode with multiple sensitivity levels
- **Real-time feedback**: Position, temperature, velocity at 50Hz
- **CRC-16 checksum**: Reliable serial communication
- **Auto-reconnect**: Handles serial disconnections gracefully

## 安装

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

### 串口权限 (Linux)

```bash
sudo usermod -a -G dialout $USER
# Log out and back in
```

## 快速开始

### 作为 MCP Server 运行

```bash
# stdio transport
python src/gimbal_mcp_server.py
```

### Claude Desktop 配置

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

### MCP Inspector (测试)

```bash
mcp dev src/gimbal_mcp_server.py
```

## 可用工具

| 工具 | 描述 |
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

## 可用资源

| 资源 | 描述 |
|----------|-------------|
| `gimbal://status` | Current angles, mode, temperature |
| `gimbal://connection` | Serial connection status |
| `gimbal://sdk_info` | SDK metadata and version |

## 工作模式

| 模式 | 代码 | 描述 |
|------|------|-------------|
| `angle_lock` | 0x11 | Pointing lock mode (角速度控制) |
| `follow` | 0x12 | Pointing follow mode |
| `euler` | 0x14 | Euler angle control mode |
| `fpv` | 0x1C | First-person view mode |
| `top_down` | 0x13 | Top-down shooting mode |

## 角度限制

| 轴 | 范围 | 安全级别 |
|------|-------|--------------|
| Yaw | -170° to +170° | 🟠 HIGH |
| Pitch | -90° to +30° | 🟠 HIGH |
| Roll | -45° to +45° | 🟡 MEDIUM |

### 速度限制

| 轴 | 最大速度 | 安全级别 |
|------|-----------|--------------|
| Pitch | ±150°/s | 🔴 CRITICAL |
| Yaw | ±150°/s | 🔴 CRITICAL |
| Roll | ±150°/s | 🔴 CRITICAL |

## 串口协议

The GCU protocol uses binary frames:

```
[0xA8][0xE5][LEN_L][LEN_H][VER][DATA...][CRC16_L][CRC16_H]
```

### 帧结构

| 字段 | 大小 | 描述 |
|-------|------|-------------|
| Header | 2 bytes | 0xA8 0xE5 (send) / 0x8A 0x5E (receive) |
| Length | 2 bytes | Packet length (little-endian) |
| Version | 1 byte | Protocol version (0x01) |
| Data | Variable | Command + parameters |
| CRC16 | 2 bytes | CRC-16 checksum (big-endian) |

### 命令代码

| 命令 | 代码 | 描述 |
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

## 安全信息

**警告:** This MCP server controls a motorized gimbal. Improper use can cause:
- Equipment damage
- Motor overheating
- Mechanical wear

### 安全特性

| 特性 | 描述 |
|---------|-------------|
| **速度限制** | ±150°/s enforced in software |
| **角度限制** | Mechanical limits enforced |
| **CRC 校验** | All packets validated |
| **紧急停止** | `stop_rotation()` immediate halt |

### 安全级别

| 级别 | 颜色 | 描述 | 示例 |
|-------|-------|-------------|---------|
| **CRITICAL** | 🔴 | Immediate danger | Speed > 150°/s |
| **HIGH** | 🟠 | Potential damage | Angle near limit |
| **MEDIUM** | 🟡 | Caution needed | Extended operation |
| **LOW** | 🟢 | Informational | Status check |

### 紧急程序

1. **立即停止**: Use `stop_rotation()` tool
2. **断电**: Disconnect power if mechanical issue
3. **复位**: Use `reset_gimbal()` to return to center

## 错误处理

### 错误代码

| 代码 | 名称 | 严重级别 | 描述 |
|------|------|----------|-------------|
| -1 | CONNECTION_FAILED | 🟠 error | Serial port not accessible |
| -2 | TIMEOUT | 🟠 error | Command response timeout |
| -3 | INVALID_PARAMETER | 🟠 error | Invalid angle or speed |
| -4 | SAFETY_VIOLATION | 🔴 critical | Exceeds speed/angle limits |
| -5 | NOT_INITIALIZED | 🟠 error | Not connected |
| -6 | CRC_ERROR | 🟠 error | Checksum mismatch |

### 故障排除

| 问题 | 可能原因 | 解决方案 |
|-------|---------------|----------|
| Connection failed | Port not found | Check COM port in Device Manager |
| Connection failed | Permission denied | Add user to `dialout` group |
| Command no response | Wrong baudrate | Verify 115200 setting |
| Jerky movement | EMI interference | Use shielded cable |
| CRC errors | Cable too long | Max 3m for USB-serial |

## 查找串口

```bash
# Linux
ls /dev/ttyUSB* /dev/ttyACM*

# Windows
# Use Device Manager → Ports (COM & LPT)
# Common: COM3, COM4, COM8

# macOS
ls /dev/tty.usbserial-*
```

## 依赖

- Python 3.10+
- `mcp[fastmcp]` — MCP framework
- `pyserial` — Serial communication

## 架构

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

## 参考

- **GCU 协议**: [GCU私有通信协议-XF(A5)V2.0.6.pdf](./docs/GCU私有通信协议-XF(A5)V2.0.6.pdf)
- **用户手册**: [Z-2Mini用户手册-XF(A5)V1.4.pdf](./docs/Z-2Mini用户手册-XF(A5)V1.4.pdf)
- **MCP 协议**: https://modelcontextprotocol.io/
- **PySerial**: https://pyserial.readthedocs.io/

## 许可证

MIT License — See [LICENSE](LICENSE)

**注意**: The GCU protocol is proprietary to Xianfei Technology. This MCP server is an independent implementation based on the published protocol specification.

## Part of ROSClaw

- [rosclaw-g1-dds-mcp](https://github.com/ros-claw/rosclaw-g1-dds-mcp) — Unitree G1 (DDS)
- [rosclaw-ur-ros2-mcp](https://github.com/ros-claw/rosclaw-ur-ros2-mcp) — UR5 arm (ROS2)
- [rosclaw-gimbal-mcp](https://github.com/ros-claw/rosclaw-gimbal-mcp) — GCU Gimbal (Serial)
- [rosclaw-ur-rtde-mcp](https://github.com/ros-claw/rosclaw-ur-rtde-mcp) — UR5 via RTDE

---

**Generated by ROSClaw SDK-to-MCP Transformer**

*SDK Version: GCU V2.0.6 | Protocol: Binary Serial | CRC-16*
