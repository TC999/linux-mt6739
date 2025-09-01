# MT6739 Linux Tablet Porting Guide

This repository provides native Linux kernel support for MediaTek MT6739-based Android tablets.

## What was added

### Core Support Files
- `include/dt-bindings/clock/mt6739-clk.h` - Clock definitions for MT6739
- `include/dt-bindings/pinctrl/mediatek,mt6739-pinfunc.h` - Pin function definitions  
- `arch/arm64/boot/dts/mediatek/mt6739-tablet.dts` - Generic tablet device tree

### Enhanced Device Tree
- Updated `arch/arm64/boot/dts/mediatek/mt6739.dtsi` with essential peripherals:
  - UART0/UART1 for console and debugging
  - MMC0/MMC1 controllers for eMMC and SD card
  - USB2 controller and PHY
  - Clock controllers (topckgen, infracfg, pericfg)

## Boot Requirements

### Hardware Requirements
- MediaTek MT6739 SoC (quad-core Cortex-A53)
- At least 1GB RAM
- eMMC or NAND storage
- UART access for debugging (recommended)

### Bootloader
You need a bootloader that can:
1. Load device tree blob (dtb)
2. Load kernel image (Image)
3. Set up proper memory layout
4. Boot in AArch64 mode

## Building

### Configure Kernel
```bash
make ARCH=arm64 defconfig
# Enable MediaTek platform support
make ARCH=arm64 menuconfig
# Navigate to System Type -> Enable MediaTek MT6739 support
```

### Build Kernel and Device Tree
```bash
make ARCH=arm64 -j$(nproc) Image dtbs
```

### Output Files
- `arch/arm64/boot/Image` - Kernel image
- `arch/arm64/boot/dts/mediatek/mt6739-tablet.dtb` - Device tree blob

## Installation

### Method 1: Fastboot (if available)
```bash
fastboot boot arch/arm64/boot/Image arch/arm64/boot/dts/mediatek/mt6739-tablet.dtb
```

### Method 2: Direct Flash
Flash the kernel and dtb to appropriate partitions based on your tablet's partition layout.

## Console Access

The device tree is configured to use UART0 as the primary console:
- Baud rate: 115200
- Data bits: 8  
- Parity: None
- Stop bits: 1
- Flow control: None

Connect to GPIO pins 20 (TX) and 21 (RX) for serial console access.

## Storage Support

### eMMC (mmc0)
- 8-bit bus width
- HS200 mode support  
- Hardware reset capability
- Connected to pins GPIO38-GPIO49

### SD Card (mmc1)  
- 4-bit bus width
- UHS-I SDR104 mode
- Card detection on GPIO5
- Connected to pins GPIO71-GPIO76

## USB Support

USB 2.0 OTG controller is enabled for:
- USB storage devices
- USB keyboard/mouse
- USB networking (if configured)

## Current Limitations

1. **Display**: No display controller support yet
2. **Audio**: No audio support yet  
3. **WiFi/BT**: No wireless connectivity yet
4. **Power Management**: Basic power management only
5. **Sensors**: No sensor support yet

## Next Steps

To get a fully functional tablet, additional drivers need to be added for:
- Display (MIPI-DSI/HDMI)
- GPU (Mali graphics)
- Audio (I2S/PCM)
- WiFi/Bluetooth
- Power management (PMIC)
- Touch screen
- Camera
- Sensors (accelerometer, gyroscope, etc.)

## Troubleshooting

### Boot Issues
1. Check UART console for error messages
2. Verify bootloader is loading correct dtb
3. Ensure memory layout matches hardware

### Storage Issues
1. Check if eMMC/SD card is properly connected
2. Verify pin configuration matches hardware
3. Check power supplies if using regulators

## Contributing

This is a basic port focusing on essential boot functionality. Contributions are welcome for:
- Additional peripheral support
- Power management improvements  
- Performance optimizations
- Hardware-specific device trees