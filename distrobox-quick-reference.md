# Distrobox Quick Reference

## Your Containers
- **arch-box** → Arch Linux (AUR packages, bleeding-edge)
- **debian-box** → Debian Stable (stable apps, conservative)  
- **fedora-box** → Fedora Latest (Red Hat ecosystem, newer packages)

## Essential Commands

### Enter Containers
```bash
distrobox enter arch-box     # Enter Arch container
distrobox enter debian-box   # Enter Debian container  
distrobox enter fedora-box   # Enter Fedora container
```

### Quick Package Management
```bash
# Arch (inside arch-box)
paru -S package-name         # Install from AUR
sudo pacman -S package-name  # Install from official repos

# Debian (inside debian-box)  
sudo apt update && sudo apt install package-name

# Fedora (inside fedora-box)
sudo dnf install package-name
```

### Export GUI Apps to Host
```bash
# Inside any container after installing a GUI app:
distrobox-export --app application-name

# Example: After installing Firefox in debian-box:
distrobox-export --app firefox
# Firefox now appears in your drun launcher!
```

### Container Management
```bash
distrobox list               # Show all containers
distrobox stop container     # Stop a container
distrobox rm container       # Delete a container (careful!)
```

## Desktop Integration
✅ Containers appear in drun launcher as "Arch-box", "Debian-box", "Fedora-box"  
✅ Exported apps appear in drun launcher automatically  
✅ GUI apps run seamlessly with Hyperland  
✅ Home directory is shared across all containers  

## Pro Tips
- First container entry takes ~30 seconds (setup)
- Subsequent entries take ~400ms (very fast!)
- Install GUI apps in containers, export them to host
- Use different containers for different purposes
- Your files in ~/Documents, ~/Downloads etc. are accessible in all containers
- Containers are like having multiple Linux distros without dual-boot

## Rollback System (NEW!)
```bash
# BEFORE installing any app, create snapshot:
distrobox-snapshot arch-box

# Install and test app...
distrobox enter arch-box
paru -S some-app

# If you don't like it, rollback:
distrobox-rollback arch-box arch-box-snapshot-20250703-021501

# List all snapshots:
distrobox-snapshots list

# Clean old snapshots (keep 5 newest):
distrobox-snapshots clean
```

## Your Clean Baseline Snapshots
✅ **arch-box-snapshot-20250703-021501** - Clean Arch container
✅ **debian-box-snapshot-20250703-021520** - Clean Debian container
✅ **fedora-box-snapshot-20250703-021532** - Clean Fedora container

## System Recovery Options

### Distrobox Container Recovery
```bash
# If something breaks, rollback to clean state:
distrobox-rollback container-name clean-snapshot-name
```

### Full System Recovery (NEW!)
```bash
# Boot into any system snapshot from GRUB menu:
# 1. Restart system
# 2. Select "CachyOS Linux snapshots" in GRUB
# 3. Choose snapshot by date/description
# 4. Boot into previous system state

# Available recovery points:
# - 51 bootable snapshots in GRUB menu
# - Important snapshots permanently preserved
# - Automatic GRUB updates when new snapshots created
```

### Dual Boot Access (NEW!)
```bash
# Boot into Windows 11:
# 1. Restart system
# 2. Select "Windows Boot Manager" in GRUB
# 3. Windows 11 boots normally

# Both Windows and Linux snapshots in same GRUB menu!
```

## Emergency
```bash
# Multiple recovery options available:
# 1. Distrobox container rollback (app-level)
# 2. System snapshot boot (system-level)
# 3. Windows 11 boot (alternative OS)
# 4. Live USB recovery (last resort)
```
