# What happens during the boot process in Linux?

**[Note: This is a work-in-progress draft.]**

## Answer

Linux boots in six sequential stages, taking the system from power-on to user login in typically thirty seconds to two minutes.

## Background information

### 1. **BIOS/UEFI** (2-10 seconds)

**What happens:** Firmware checks hardware and finds bootable device  

The system runs Power-On Self Test (POST) to verify components work, then searches for a bootable drive based on your boot priority settings.

### 2. **BOOTLOADER** (1-3 seconds)  

**What happens:** GRUB loads and presents boot menu  

The bootloader (usually GRUB) loads from the hard drive, shows you kernel options, then loads your selected Linux kernel and initial RAM disk into memory.

### 3. **KERNEL INITIALIZATION** (5-15 seconds)

**What happens:** Linux kernel takes control and detects hardware  

The kernel:
- Detects and configures all hardware components
- Loads essential device drivers  
- Mounts the temporary filesystem (initrd/initramfs)
- Launches the first user process

### 4. **INIT SYSTEM** (2-5 seconds)

**What happens:** systemd (or other init) becomes process manager  

The init system starts as PID 1 and reads configuration files to determine which services need to start and their proper order.

### 5. **SERVICE STARTUP** (10-60 seconds)

**What happens:** Essential system services start running  

Based on your runlevel/target, the system starts:
- Network connections
- Logging services
- Device managers
- Security services

### 6. **USER SPACE** (1-5 seconds)

**What happens:** Login screen appears  

The display manager starts (for GUI) or command prompt appears. System is now ready for user login and normal operation.

### Troubleshooting

Most boot issues occur during stages 2 (bootloader problems) or 5 (service failures).

## Quiz

### Multiple-choice questions

### Answer key
