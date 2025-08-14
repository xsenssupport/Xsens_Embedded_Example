# Xsens Embedded Examples

This repository contains embedded examples for controlling the MTi1-s module from an embedded system using SPI or I2C bus. The examples are written in C/C++ and can be built using either IAR or GCC. All examples run on a NUCLEO-F401RE board but are easily portable to other boards.

## 📋 Examples Overview

| Example | Directory | Purpose |
|---------|-----------|---------|
| **Measurement Data** | `example_mti1_i2c_spi_receive_measurement_data` | Shows typical program flow for reading measurement data using MTSSP protocol |
| **Protocol Explorer** | `example_mti1_i2c_spi_mtssp_protocol_explorer` | Interactive low-level command interface for MTSSP protocol analysis |
| **Firmware Update** | `example_mti1_i2c_spi_firmware_updater` | Demonstrates bootloader mode and firmware update process |

### 🎯 Getting Started Recommendation
Start with the **Measurement Data Example** as it demonstrates the typical workflow for reading data from the device.

## 🛠️ Requirements

### Hardware
- Xsens MTi3 or MTi7 module
- Xsens development kit shield board (MTi 1-s DEV board Rev 2.1)
- STM32 NUCLEO-F401RE development board

### Software
- **Compiler**: IAR Embedded Workbench for ARM version 7.80.3+ **OR** arm-none-eabi GCC toolchain
- **Terminal Emulator**: PuTTY, Tera Term, or similar

### Optional
- Oscilloscope or logic analyzer with SPI/I2C decoding capabilities

## 🔧 Hardware Setup

1. Insert the MTi1 module into the dev board socket
2. Plug the dev board onto the Arduino connector of the NUCLEO board
3. Connect the NUCLEO board to your PC via USB cable
4. Configure terminal emulator:
   - **Baud Rate**: 921,600
   - **Data Bits**: 8
   - **Parity**: None
   - **Stop Bits**: 1

> 📖 For detailed hardware setup information, consult the [development kit manual](https://mtidocs.movella.com/use-manual).

## 🚀 Example Usage

### 1. Measurement Data Example

**Build and Run:**
```bash
# Build the project (creates example_mti1_i2c_spi_receive_measurement_data.bin)
# Program to NUCLEO board via debugger or copy to virtual drive
```

**Expected Output:**
```
----------------------------------------------------------------
Example MTi I2C/SPI: Receive Measurement data
Enter bus mode:
Press 's' for SPI mode
Press 'i' for I2C mode
```

**Available Commands:**
- `(h)` help - Print help text
- `(R)` reset - Reset the device
- `(c)` config - Go to config mode
- `(m)` measure - Go to measurement mode
- `(d)` deviceid - Request device ID

**Sample Measurement Output:**
```
XMID_MtData2: roll = 0.54, pitch = 0.75, yaw = -56.53
XMID_MtData2: roll = 0.64, pitch = 0.55, yaw = -56.71
XMID_MtData2: roll = 0.73, pitch = 0.34, yaw = -56.90
```

### 2. MTSSP Protocol Explorer

This tool provides interactive access to low-level MTSSP protocol commands.

**Available Commands:**
- `(h)` help - Print help text
- `(R)` reset - Reset the device
- `(i)` info - Request protocol information (Opcode 0x01)
- `(o)` options - Configure protocol options (Opcode 0x02)
- `(c)` config - Go to config mode (Opcode 0x03)
- `(m)` measure - Go to measurement mode (Opcode 0x03)
- `(d)` deviceid - Request device ID (Opcode 0x03)
- `(f)` fwrev - Request firmware revision (Opcode 0x03)
- `(s)` status - Request pipe status (Opcode 0x04)
- `(r)` read - Read data from notification or measurement pipe (Opcode 0x05/0x06)

**Example Protocol Information Response:**
```
Protocol version: 2
DataReady config: 12 (0x0C)
        Polarity: Idle low
        Output type: Push-pull
        DRDY on notification pipe is enabled
        DRDY on measurement pipe is enabled
```

**SPI Communication Example:**
```
MOSI: 01 00 00 00 XX XX
MISO: FA FF FF FF 02 0C
```

### 3. Firmware Update Example

**Available Commands:**
- `(h)` help - Print help text
- `(R)` Reset - Hard reset the device
- `(r)` reset - Soft reset the device
- `(v)` version - Request firmware version
- `(b)` bootloader - Go to bootloader mode
- `(u)` update - Start firmware update

**Update Process:**
1. Reset device: `(r)`
2. Check version: `(v)` 
3. Enter bootloader: `(b)` (version shows 255.x.y)
4. Start update: `(u)` (takes ~20 seconds)

> ⚠️ **Important**: For MTi7 devices, link `src/xffdata_mti7.c` instead of `src/xffdata_mti3.c`

## ⚙️ Configuration Notes

### Auto-GotoMeasurement Setting
By default, the device automatically enters measurement mode after power-up, which can cause the measurement pipe to overflow. To disable this behavior, send the following Xbus message:

```
FA FF 48 08 00000002 00000000
```

This sets the `XDOF_DisableAutoMeasurement` flag in non-volatile memory.

### Protocol Explorer Usage Notes
- Commands `(c)`, `(m)`, `(d)`, and `(f)` provide no direct feedback
- Use `(s)` Pipe Status and `(r)` read commands to check device responses  
- Commands `(d)` ReqDID and `(f)` ReqFWRev only work in Config Mode

## 📚 References

1. [MTi-1 Series Development Kit User Manual](https://mtidocs.movella.com/use-manual)
2. [MTi-1 Series Datasheet](https://mtidocs.movella.com/datasheet)
3. [MT Low Level Communication Protocol Documentation](https://mtidocs.movella.com/mt-low-level-communication-protocol-documentation)
4. [Best Practices I2C and SPI for MTi 1-series](https://base.movella.com/s/article/article/Best-practices-I2C-and-SPI-for-MTi-1-series-1605869706124)
5. [MT Manager User Manual](https://mtidocs.movella.com/mt-manager)

## 🆘 Support

For firmware versions other than 1.4.0 or technical support, please contact Xsens support.

---

> 💡 **Note**: The firmware update example is for demonstration purposes only. For critical applications, properly adapt and test the code for your specific use case.