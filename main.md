# Your Ultimate System Management Guide

**Welcome to Your New Superpower!** 🚀

This guide explains everything that's been set up on your CachyOS system to make it virtually unbreakable. You now have a two-layer safety net that lets you experiment fearlessly with your computer.

## What You Now Have (The Big Picture)

Think of your system like a building with two floors, each with its own backup system:

### 🏢 **Floor 1: Your Operating System (Root)**
- **What it protects:** Core system files, drivers, kernels
- **How it works:** Automatically creates snapshots before/after system updates
- **Your benefit:** If a system update breaks something, you can boot from GRUB and select a working snapshot

### 🏠 **Floor 2: Your Personal Files (Home)**
- **What it protects:** Your configs, themes, dotfiles, personal settings
- **How it works:** Creates snapshots every hour, keeps 3 daily + 4 weekly + monthly archives
- **Your benefit:** Mess up your Hyprland config? Restore it in seconds

### 📦 **Your Software Management**
- **What you use:** Standard pacman/AUR packages + isolation when needed
- **How it works:** Apps install to standard locations, better desktop integration
- **Your benefit:** Simpler management, better compatibility, easier troubleshooting
- **When you need isolation:** Use Docker, Python venvs, or AppImages

## Daily Usage - What You Need to Know

### Installing Software

**For system packages (drivers, kernels):**
```bash
sudo pacman -S packagename
# Snapshots are created automatically - no action needed!
```

**For applications:**
```bash
# Standard installation:
paru packagename  # or sudo pacman -S packagename

# For development environments:
docker run -it --rm python:3.11  # temporary container
python -m venv myproject  # virtual environment

# For portable apps:
# Download AppImage or install via Flatpak
```

### Before Making Big Changes

**Before modifying configs (Hyprland, themes, etc):**
```bash
snapper -c home create --description "pre-hyprland-experiment"
```

**Before major system changes:**
```bash
sudo snapper -c root create --description "pre-major-change"
```

### When Things Go Wrong

**If you broke your Hyprland config:**
```bash
# See your snapshots:
snapper -c home list

# Restore specific file:
sudo cp /home/.snapshots/[NUMBER]/snapshot/carnateo/.config/hypr/hyprland.conf ~/.config/hypr/

# Or restore entire config directory:
sudo cp -r /home/.snapshots/[NUMBER]/snapshot/carnateo/.config/hypr ~/.config/
```

**If a package is broken:**
```bash
# Remove problematic package:
sudo pacman -R packagename

# Or restore system from snapshot if it's a system issue:
# Boot from GRUB snapshot menu

# For development environments:
docker rm container_name  # remove broken container
rm -rf venv_directory     # remove broken virtual environment
```

**If your system won't boot:**
1. Reboot and hold SHIFT
2. Select "Advanced options"
3. Choose a snapshot from before the problem
4. Boot from that snapshot

## What Runs Automatically

You don't need to do anything for these - they just work:

### ✅ **Every Hour**
- Timeline snapshots of your home directory
- Old snapshots get cleaned up automatically

### ✅ **Every Day**  
- System cleanup of old snapshots
- Performance optimization

### ✅ **Every Month (1st of month)**
- Important archive snapshot that never gets deleted
- Perfect for recovering from issues you discover months later

### ✅ **Every System Update**
- Pre/post snapshots automatically created
- You can always go back if updates break something

## Key Locations You Should Know

### Your Command References
- `~/commands/` - All your cheat sheets and references
- `~/commands/README.txt` - Start here if you forget something

### Your Logs and Tools
- `~/system-config-log/` - Monitoring scripts and logs
- `/usr/local/bin/monthly-home-archive.sh` - Monthly backup script

### Important Directories
- `/home/.snapshots/` - Your personal file snapshots
- `/.snapshots/` - Your system snapshots

## Quick Status Checks

**See what's running:**
```bash
systemctl list-timers | grep -E "(snapper|monthly)"
```

**Check your snapshots:**
```bash
snapper -c home list    # Your personal files
sudo snapper -c root list    # System files
```

**Check disk space:**
```bash
df -h
```

## Emergency Commands

**If something is seriously broken:**
```bash
# From live USB or rescue mode, mount and restore:
sudo mount /dev/nvme0n1p5 /mnt
sudo cp -r /mnt/.snapshots/[GOOD_NUMBER]/snapshot/* /mnt/
```

**If you need help:**
- Check `~/commands/emergency-recovery-commands.txt`
- All recovery procedures are documented there

## What NOT to Touch

🚫 **Never delete these:**
- Anything in `/.snapshots/` or `/home/.snapshots/`
- Scripts in `/usr/local/bin/`
- Configurations in `/etc/snapper/`
- Files marked as "important" in snapshots

🚫 **Never run these commands without understanding:**
- `sudo btrfs subvolume delete`
- `sudo snapper delete` (unless you know what you're doing)
- Anything that modifies system directories directly

## Maintenance (Monthly)

**Once a month, run this:**
```bash
# Clean up package cache:
sudo pacman -Sc

# Check system health:
~/system-config-log/snapshot-monitor.sh

# Clean up Docker if you use it:
docker system prune
```

That's it! The system is designed to be self-maintaining.

## Your Superpowers Summary

✅ **Fearless Experimentation:** Change anything, break anything, restore in minutes  
✅ **Time Travel:** Go back to any point in time for your files  
✅ **Package Simplicity:** Standard package management with better compatibility
✅ **Automatic Protection:** System updates can't permanently break your computer  
✅ **Multiple Recovery Options:** Boot-time rollbacks, file-level restores, or full system recovery  

## Need More Details?

- **Basic operations:** Check `~/commands/README.txt`
- **Advanced recovery:** Check `~/commands/emergency-recovery-commands.txt`  
- **Package management:** Use standard pacman/AUR commands
- **System monitoring:** Run `~/system-config-log/snapshot-monitor.sh`

**Remember:** This system is designed so you can't permanently break your computer. Experiment freely! 🎉