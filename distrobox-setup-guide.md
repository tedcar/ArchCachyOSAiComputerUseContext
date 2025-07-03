# Distrobox Setup Guide for CachyOS
**Installation Date:** July 3, 2025  
**System:** CachyOS with Hyperland window manager  
**User:** carnateo  

## Overview

Distrobox has been successfully installed and configured with three container distributions:

### Available Containers

1. **arch-box** - Arch Linux (latest)
   - **Use case:** AUR packages, bleeding-edge software, Arch-specific tools
   - **Image:** docker.io/library/archlinux:latest
   - **Status:** ✅ Ready

2. **debian-box** - Debian (stable)
   - **Use case:** Stable applications, Debian-specific packages, conservative environment
   - **Image:** docker.io/library/debian:stable
   - **Status:** ✅ Ready

3. **fedora-box** - Fedora (latest)
   - **Use case:** Red Hat ecosystem, newer packages, RPM-based software
   - **Image:** registry.fedoraproject.org/fedora:latest
   - **Status:** ✅ Ready

## Desktop Integration

✅ **Desktop entries created** - All containers appear in drun launcher  
✅ **Icons installed** - Proper distribution icons in ~/.local/share/icons/distrobox/  
✅ **Hyperland compatibility** - Applications will integrate seamlessly  

## Basic Usage Commands

### Container Management
```bash
# List all containers
distrobox list

# Enter a container
distrobox enter arch-box
distrobox enter debian-box
distrobox enter fedora-box

# Stop a container
distrobox stop arch-box

# Remove a container (careful!)
distrobox rm arch-box

# Create new container
distrobox create --name my-container --image ubuntu:latest
```

### Application Export (for GUI apps)
```bash
# Inside a container, export an app to host
distrobox-export --app firefox

# Export with custom name
distrobox-export --app firefox --extra-flags "--name Firefox-Container"

# Remove exported app
distrobox-export --app firefox --delete
```

### Quick Access from Host
```bash
# Run single command in container
distrobox enter arch-box -- pacman -Syu
distrobox enter debian-box -- apt update && apt upgrade
distrobox enter fedora-box -- dnf update

# Install packages from host
distrobox enter arch-box -- paru -S some-aur-package
distrobox enter debian-box -- sudo apt install some-package
distrobox enter fedora-box -- sudo dnf install some-package
```

## Use Case Examples

### Temporary Testing
```bash
# Test a risky package
distrobox enter arch-box
# Install and test package
# If it breaks something, just recreate the container
```

### Long-term Application Usage
```bash
# Install a GUI application you'll use regularly
distrobox enter debian-box
sudo apt install gimp
distrobox-export --app gimp
# Now GIMP appears in your drun launcher
```

### Development Environment
```bash
# Set up isolated development environment
distrobox enter fedora-box
sudo dnf install nodejs npm python3-pip
# Your development tools are isolated but integrated
```

## Performance Tips

- **First run** of each container takes longer (setup process)
- **Subsequent entries** are very fast (~400ms)
- **GUI applications** run at near-native performance
- **File access** to your home directory is seamless

## Backup and Maintenance

### Container Snapshots
```bash
# Create snapshot of configured container
podman commit arch-box arch-box-backup
podman save arch-box-backup > arch-box-backup.tar

# Restore from snapshot
podman load < arch-box-backup.tar
distrobox create --name arch-box-restored --image arch-box-backup
```

### System Integration
- Containers are protected by your existing Snapper snapshot system
- Container data is stored in ~/.local/share/containers/
- Desktop entries are in ~/.local/share/applications/

## Troubleshooting

### If GUI apps don't work
```bash
# Ensure X11 forwarding is working
xhost +si:localuser:$USER
```

### If container won't start
```bash
# Check container status
podman ps -a
# Restart container
distrobox stop container-name
distrobox enter container-name
```

### Reset a container
```bash
# Complete reset (removes all data!)
distrobox rm container-name
distrobox create --name container-name --image original-image
```

## Next Steps

1. **Test the setup** - Try launching containers from drun launcher
2. **Install your first GUI app** - Pick a container and install something
3. **Export the app** - Make it appear in your launcher
4. **Enjoy seamless multi-distro experience!**

---

**Note:** This setup integrates perfectly with your existing CachyOS snapshot system. Any issues can be rolled back using your Snapper snapshots.
