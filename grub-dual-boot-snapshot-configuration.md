# GRUB Dual Boot & Snapshot Configuration Guide
**Date:** July 3, 2025  
**System:** CachyOS with Btrfs filesystem  
**Configuration:** Windows 11 + CachyOS dual boot with Snapper snapshot boot integration  

## Overview

This guide documents the successful configuration of two critical boot features:
1. **Windows 11 Dual Boot Detection** - Windows 11 appears in GRUB boot menu
2. **Snapper Snapshot Boot Integration** - Root snapshots are bootable from GRUB menu

## Part 1: Windows 11 Dual Boot Configuration

### System Analysis Results
- **EFI System Partition:** `/dev/nvme0n1p3` mounted at `/boot/efi`
- **Windows Boot Manager:** Located at `/EFI/Microsoft/Boot/bootmgfw.efi`
- **os-prober Detection:** Successfully detects Windows 11
- **GRUB Platform:** UEFI mode

### Configuration Steps Performed

#### 1. Enable os-prober in GRUB
**File Modified:** `/etc/default/grub`
```bash
# Changed from:
#GRUB_DISABLE_OS_PROBER=false
# To:
GRUB_DISABLE_OS_PROBER=false
```

#### 2. Regenerate GRUB Configuration
```bash
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

**Result:** Windows Boot Manager successfully detected and added to GRUB menu

### Verification
- **GRUB Entry Created:** `Windows Boot Manager (on /dev/nvme0n1p3)`
- **Boot Method:** Chainloading to `/EFI/Microsoft/Boot/bootmgfw.efi`
- **Menu Location:** Appears in main GRUB boot menu

## Part 2: Snapper Snapshot Boot Integration

### Packages Installed
```bash
sudo pacman -S grub-btrfs inotify-tools
```

### Configuration Steps Performed

#### 1. Enable grub-btrfsd Daemon
```bash
sudo systemctl enable grub-btrfsd
sudo systemctl start grub-btrfsd
```

**Purpose:** Automatically regenerates GRUB snapshot entries when new snapshots are created

#### 2. Generate Initial Snapshot Menu
```bash
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

**Result:** 50 snapshots detected and integrated into GRUB

### Snapshot Integration Results

#### Snapshots Detected
- **Total Snapshots:** 50 root snapshots
- **Snapshot Types:** pre, post, single (manual)
- **Date Range:** 2025-06-30 to 2025-07-03
- **Important Snapshots Included:** Yes (marked snapshots preserved)

#### GRUB Menu Structure
```
Main GRUB Menu
├── CachyOS Linux (current)
├── CachyOS Linux snapshots (submenu)
│   ├── 2025-07-03 02:34:34 | post | grub-btrfs inotify-tools
│   ├── 2025-07-03 02:34:26 | pre  | pacman -S grub-btrfs inotify-tools
│   ├── 2025-07-03 02:19:01 | single | IMPORTANT: Distrobox setup
│   └── [47 more snapshots...]
├── Windows Boot Manager (on /dev/nvme0n1p3)
└── UEFI Firmware Settings
```

#### Snapshot Boot Configuration
- **Submenu Name:** "CachyOS Linux snapshots"
- **Configuration File:** `/boot/grub/grub-btrfs.cfg`
- **Boot Parameters:** Properly configured with rootflags for Btrfs subvolumes
- **Kernel/Initramfs:** Uses snapshot-specific boot files

## Technical Details

### Windows 11 Boot Entry
```grub
menuentry 'Windows Boot Manager (on /dev/nvme0n1p3)' --class windows --class os {
    insmod part_gpt
    insmod fat
    search --no-floppy --fs-uuid --set=root 4AD2-BB59
    chainloader /EFI/Microsoft/Boot/bootmgfw.efi
}
```

### Snapshot Boot Entry Example
```grub
menuentry 'vmlinuz-linux-cachyos & initramfs-linux-cachyos.img & amd-ucode.img' {
    insmod btrfs
    search --no-floppy --fs-uuid --set=root 7fe56155-3285-4140-bc66-31b327591ddc
    linux "/@/.snapshots/59/snapshot/boot/vmlinuz-linux-cachyos" \
          root=UUID=7fe56155-3285-4140-bc66-31b327591ddc \
          rootflags=defaults,noatime,compress=zstd,commit=120,subvol="@/.snapshots/59/snapshot"
    initrd "/@/.snapshots/59/snapshot/boot/amd-ucode.img" \
           "/@/.snapshots/59/snapshot/boot/initramfs-linux-cachyos.img"
}
```

## Automatic Updates

### grub-btrfsd Daemon
- **Status:** Active and enabled
- **Function:** Monitors `/.snapshots` for changes
- **Trigger:** Automatically regenerates snapshot menu when new snapshots are created
- **Configuration:** No additional configuration required

### Integration with Snapper
- **snap-pac Integration:** Pre/post snapshots automatically appear in GRUB
- **Manual Snapshots:** Important snapshots are preserved and bootable
- **Cleanup Compatibility:** Respects Snapper cleanup policies

## Usage Instructions

### Booting into Windows 11
1. Restart system
2. Select "Windows Boot Manager (on /dev/nvme0n1p3)" from GRUB menu
3. Windows 11 boots normally

### Booting into Snapshots
1. Restart system
2. Select "CachyOS Linux snapshots" from GRUB menu
3. Choose desired snapshot by date/description
4. Select kernel/initramfs combination
5. System boots into selected snapshot state

### Recovery Scenarios
- **System Corruption:** Boot into recent working snapshot
- **Failed Updates:** Boot into pre-update snapshot
- **Configuration Issues:** Boot into known-good configuration snapshot

## Maintenance

### Regular Tasks
- **No manual intervention required** - grub-btrfsd handles updates automatically
- **Snapshot cleanup** - Handled by existing Snapper configuration
- **GRUB updates** - Automatically include both Windows and snapshots

### Troubleshooting
- **Windows not detected:** Verify ESP mount and run `sudo os-prober`
- **Snapshots not appearing:** Check grub-btrfsd service status
- **Boot issues:** Use GRUB rescue mode or live USB

## Security Considerations

- **os-prober enabled:** Required for Windows detection but has security implications
- **Snapshot access:** All snapshots are bootable without authentication
- **EFI partition:** Shared between Windows and Linux (standard configuration)

## Backup Information

- **Original GRUB config:** Backed up to `/etc/default/grub.backup`
- **Snapshot protection:** Important snapshots marked and preserved
- **System snapshots:** Automatic snapshots created during configuration

This configuration provides a robust dual-boot environment with comprehensive recovery options through bootable snapshots.
