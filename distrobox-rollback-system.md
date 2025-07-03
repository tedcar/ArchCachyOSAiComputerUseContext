# Distrobox Individual Container Rollback System

## Overview
Each of your distrobox containers can have its own rollback system, allowing you to:
- Create snapshots before installing apps
- Rollback to any previous state if you don't like an app
- Keep multiple restore points per container
- Rollback only affects the specific container, not your host system

## Method 1: Container Snapshots (Recommended)

### Create Snapshot Before Installing Apps
```bash
# Before installing any app, create a snapshot
podman commit arch-box arch-box-clean-$(date +%Y%m%d-%H%M)
podman commit debian-box debian-box-clean-$(date +%Y%m%d-%H%M)
podman commit fedora -box fedora-box-clean-$(date +%Y%m%d-%H%M)
```

### Install App and Test
```bash
# Enter container and install app
distrobox enter arch-box
paru -S some-app
# Test the app...
```

### Rollback if You Don't Like It
```bash
# Stop the container
distrobox stop arch-box

# Remove current container
distrobox rm arch-box

# Restore from snapshot
podman tag arch-box-clean-20250703-1430 arch-box-restored:latest
distrobox create --name arch-box --image arch-box-restored:latest

# Your container is now back to the clean state!
```

## Method 2: Automated Snapshot Scripts

Let me create helper scripts for easy snapshot management:

### Pre-Install Snapshot Script
```bash
#!/bin/bash
# save as ~/bin/distrobox-snapshot
CONTAINER_NAME=$1
if [ -z "$CONTAINER_NAME" ]; then
    echo "Usage: distrobox-snapshot <container-name>"
    echo "Available containers: arch-box, debian-box, fedora-box"
    exit 1
fi

TIMESTAMP=$(date +%Y%m%d-%H%M%S)
SNAPSHOT_NAME="${CONTAINER_NAME}-snapshot-${TIMESTAMP}"

echo "Creating snapshot: $SNAPSHOT_NAME"
podman commit $CONTAINER_NAME $SNAPSHOT_NAME

echo "Snapshot created successfully!"
echo "To rollback later: distrobox-rollback $CONTAINER_NAME $SNAPSHOT_NAME"
```

### Rollback Script
```bash
#!/bin/bash
# save as ~/bin/distrobox-rollback
CONTAINER_NAME=$1
SNAPSHOT_NAME=$2

if [ -z "$CONTAINER_NAME" ] || [ -z "$SNAPSHOT_NAME" ]; then
    echo "Usage: distrobox-rollback <container-name> <snapshot-name>"
    echo "List snapshots with: podman images | grep snapshot"
    exit 1
fi

echo "Rolling back $CONTAINER_NAME to $SNAPSHOT_NAME..."

# Stop container
distrobox stop $CONTAINER_NAME

# Remove current container
distrobox rm $CONTAINER_NAME

# Recreate from snapshot
distrobox create --name $CONTAINER_NAME --image $SNAPSHOT_NAME

echo "Rollback complete! Container $CONTAINER_NAME restored."
```

## Method 3: Per-Distro Package Manager Rollbacks

### Arch Linux (arch-box)
```bash
# Install downgrade for easy package rollbacks
distrobox enter arch-box
paru -S downgrade

# Rollback specific package
sudo downgrade package-name

# Or use pacman cache
sudo pacman -U /var/cache/pacman/pkg/package-old-version.pkg.tar.xz
```

### Debian (debian-box)
```bash
# Install aptitude for better package management
distrobox enter debian-box
sudo apt install aptitude

# Hold packages to prevent updates
sudo apt-mark hold package-name

# Downgrade package
sudo aptitude install package-name=older-version
```

### Fedora (fedora-box)
```bash
# Use dnf history for rollbacks
distrobox enter fedora-box

# View transaction history
sudo dnf history

# Rollback to specific transaction
sudo dnf history undo <transaction-id>

# Downgrade specific package
sudo dnf downgrade package-name
```

## Recommended Workflow

### 1. Before Installing Any App
```bash
# Create snapshot
distrobox-snapshot arch-box
# Output: arch-box-snapshot-20250703-143022
```

### 2. Install and Test App
```bash
distrobox enter arch-box
paru -S new-application
# Test the application...
```

### 3A. If You Like the App - Create New Clean Snapshot
```bash
distrobox-snapshot arch-box  # New clean state with app
```

### 3B. If You Don't Like the App - Rollback
```bash
distrobox-rollback arch-box arch-box-snapshot-20250703-143022
# Container is now exactly as it was before installing the app
```

## Storage Management

### List All Snapshots
```bash
podman images | grep snapshot
```

### Remove Old Snapshots
```bash
podman rmi snapshot-name
```

### Automated Cleanup (keep only last 5 snapshots per container)
```bash
#!/bin/bash
# Clean old snapshots, keep only 5 most recent per container
for container in arch-box debian-box fedora-box; do
    podman images --format "{{.Repository}}:{{.Tag}} {{.CreatedAt}}" | \
    grep "$container-snapshot" | \
    sort -k2 -r | \
    tail -n +6 | \
    awk '{print $1}' | \
    xargs -r podman rmi
done
```

## Benefits of This System

✅ **Container-specific** - Rollbacks only affect the individual container  
✅ **Complete rollback** - Returns to exact state before app installation  
✅ **Multiple restore points** - Keep several snapshots per container  
✅ **Fast rollback** - Usually takes 30-60 seconds  
✅ **No host impact** - Your CachyOS system remains untouched  
✅ **Works with GUI apps** - Exported apps are also rolled back  

## Example Usage Scenario

```bash
# 1. Clean arch-box, create snapshot
distrobox-snapshot arch-box

# 2. Install some AUR package you're unsure about
distrobox enter arch-box
paru -S suspicious-aur-package
distrobox-export --app suspicious-app

# 3. Test it... don't like it? Rollback!
distrobox-rollback arch-box arch-box-snapshot-20250703-143022

# 4. Container is now clean, suspicious app is gone from drun launcher too!
```

This gives you the ultimate safety net for experimenting with applications in your distrobox containers!
