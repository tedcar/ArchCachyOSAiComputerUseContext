# System Management Do's and Don'ts Guide
**Project Bedrock Resilience - Safe System Operation Guidelines**

## 🎯 Overview

This guide provides essential do's and don'ts for safely managing your CachyOS system with the three-layer backup protection. Following these guidelines ensures maximum system reliability and prevents accidental damage to your backup infrastructure.

---

## ✅ DO'S - What You SHOULD Do

### 📸 Snapshot Management

**✅ DO create snapshots before major changes:**
```bash
# Before experimenting with configs:
snapper -c home create --description "pre-hyprland-experiment"

# Before system modifications:
sudo snapper -c root create --description "pre-major-change"

# Before Nix experiments:
sudo snapper -c nix create --description "pre-package-testing"
```

**✅ DO use descriptive snapshot names:**
- Good: `"pre-hyprland-theme-nord-setup"`
- Good: `"working-config-before-wayland-switch"`
- Bad: `"test"` or `"backup"`

**✅ DO check your snapshots regularly:**
```bash
# Check home snapshots weekly:
snapper -c home list

# Check system snapshots after updates:
sudo snapper -c root list
```

**✅ DO clean up test snapshots:**
```bash
# Remove temporary test snapshots:
sudo snapper -c home delete [SNAPSHOT_NUMBERS]
```

### 📦 Package Management

**✅ DO test packages temporarily first:**
```bash
# Test before installing permanently:
nix-shell -p packagename

# Only install permanently if you like it:
nix-env -iA nixpkgs.packagename
```

**✅ DO use proper package managers:**
- **System packages:** `sudo pacman -S packagename`
- **Applications:** `nix-env -iA nixpkgs.packagename`
- **Development tools:** `nix-shell -p tool1 tool2`

**✅ DO clean up old Nix generations monthly:**
```bash
# Clean up old packages and generations:
nix-collect-garbage -d
```

### 🔧 System Maintenance

**✅ DO run system monitoring regularly:**
```bash
# Weekly health check:
~/system-config-log/snapshot-monitor.sh
```

**✅ DO check system status after updates:**
```bash
# After system updates, verify services:
systemctl list-timers | grep snapper
systemctl status snapper-timeline.timer
```

**✅ DO monitor disk space:**
```bash
# Check available space monthly:
df -h
```

**✅ DO keep important snapshots:**
```bash
# Mark critical snapshots as important:
sudo snapper -c home modify -u "important=yes" [SNAPSHOT_NUMBER]
```

### 🛡️ Safety Practices

**✅ DO verify backups before major changes:**
```bash
# Ensure recent snapshots exist:
snapper -c home list | tail -5
sudo snapper -c root list | tail -5
```

**✅ DO document your changes:**
- Keep notes of what you modify
- Include dates and reasons for changes
- Document working configurations

**✅ DO use the command references:**
- Check `~/commands/README.txt` when unsure
- Use emergency procedures in `~/commands/emergency-recovery-commands.txt`

---

## ❌ DON'TS - What You Should NEVER Do

### 🚫 Critical System Files

**❌ NEVER delete these directories:**
- `/.snapshots/` (system snapshots)
- `/home/.snapshots/` (home snapshots)
- `/nix/.snapshots/` (Nix snapshots)
- `/usr/local/bin/` (system scripts)
- `~/system-config-log/` (monitoring scripts)
- `~/commands/` (reference documentation)

**❌ NEVER modify these files directly:**
- `/etc/snapper/configs/*` (without understanding)
- `/etc/fstab` (without snapshots first)
- `/nix/store/*` (let Nix manage this)

**❌ NEVER run these commands carelessly:**
```bash
# DANGEROUS - Don't run without understanding:
sudo btrfs subvolume delete
sudo snapper delete [multiple snapshots]
sudo rm -rf /.snapshots
sudo rm -rf /nix/store
```

### 📦 Package Management Mistakes

**❌ NEVER install random packages system-wide:**
```bash
# Bad - pollutes system:
sudo pacman -S random-aur-package

# Good - test first:
nix-shell -p random-package
```

**❌ NEVER mix package managers incorrectly:**
- Don't install the same package with both pacman and Nix
- Don't install system-critical packages with Nix
- Don't install development tools system-wide

**❌ NEVER ignore Nix environment:**
```bash
# Don't forget to source Nix environment:
export PATH="$HOME/.nix-profile/bin:$PATH"
```

### 🔧 Configuration Mistakes

**❌ NEVER edit configs without snapshots:**
```bash
# WRONG - No safety net:
vim ~/.config/hypr/hyprland.conf

# RIGHT - Create snapshot first:
snapper -c home create --description "pre-hyprland-edit"
vim ~/.config/hypr/hyprland.conf
```

**❌ NEVER disable snapshot services:**
```bash
# Don't disable these:
sudo systemctl disable snapper-timeline.timer
sudo systemctl disable snapper-cleanup.timer
sudo systemctl disable monthly-home-archive.timer
```

**❌ NEVER ignore disk space warnings:**
- Don't let disk usage exceed 90%
- Don't ignore snapshot cleanup recommendations
- Don't accumulate hundreds of snapshots

### 🛡️ Safety Violations

**❌ NEVER work on live system without backups:**
- Always verify recent snapshots exist
- Never skip pre-change snapshots for "quick" edits
- Don't assume "it's just a small change"

**❌ NEVER delete "important" snapshots:**
```bash
# Don't delete monthly archives or important snapshots:
sudo snapper -c home list | grep important  # These are protected
```

**❌ NEVER force operations you don't understand:**
```bash
# Dangerous force operations:
sudo btrfs subvolume delete --commit-each
sudo snapper delete --sync
rm -rf ~/.nix-profile  # Let Nix manage this
```

---

## ⚠️ Special Warnings

### 🔥 High-Risk Operations

**Before doing these, ALWAYS create snapshots:**
- Kernel updates
- Graphics driver changes...
- Desktop environment switches
- Major configuration overhauls
- System-wide package removals

**Example safe procedure:**
```bash
# 1. Create snapshots
sudo snapper -c root create --description "pre-kernel-update"
snapper -c home create --description "pre-kernel-update"

# 2. Perform operation
sudo pacman -S linux linux-headers

# 3. Test and verify
reboot
# Test system functionality

# 4. If problems occur, rollback from GRUB
```

### 🧪 Experimental Changes

**For major experiments:**
1. Create comprehensive snapshots of all layers
2. Document what you're changing and why
3. Have rollback plan ready
4. Test in non-critical hours
5. Verify system functionality after changes

---

## 📋 Monthly Maintenance Checklist

### ✅ Things to DO monthly:

**Snapshot Management:**
- [ ] Review snapshot counts: `snapper -c home list | wc -l`
- [ ] Clean up old test snapshots
- [ ] Verify monthly archives were created
- [ ] Check disk usage: `df -h`

**System Health:**
- [ ] Run comprehensive monitoring: `~/system-config-log/snapshot-monitor.sh`
- [ ] Check service status: `systemctl list-timers | grep snapper`
- [ ] Review system logs for errors

**Package Management:**
- [ ] Clean Nix generations: `nix-collect-garbage -d`
- [ ] Update Nix channels: `nix-channel --update`
- [ ] Review installed packages: `nix-env -q`

**Performance:**
- [ ] Run optimization: `~/system-config-log/weekly-optimization.sh`
- [ ] Check Btrfs health: `sudo btrfs scrub status /`

### ❌ Things to AVOID monthly:

- Don't delete important snapshots
- Don't disable automation services
- Don't ignore disk space warnings
- Don't skip backup verification

---

## 🚨 Emergency Procedures

### If You Accidentally Delete Something Important:

**1. Stop and assess:**
- Don't panic or make more changes
- Identify what was deleted and when

**2. Check snapshots immediately:**
```bash
snapper -c home list  # For personal files
sudo snapper -c root list  # For system files
```

**3. Restore from appropriate snapshot:**
```bash
# For single files:
sudo cp /home/.snapshots/[NUMBER]/snapshot/carnateo/path/to/file ~/path/to/file

# For directories:
sudo cp -r /home/.snapshots/[NUMBER]/snapshot/carnateo/.config/app ~/.config/
```

### If System Won't Boot:

**1. Use GRUB snapshot menu:**
- Reboot and hold SHIFT
- Select "Advanced options"
- Choose snapshot from before problem
- Boot and investigate

**2. If GRUB doesn't work:**
- Boot from CachyOS live USB
- Mount filesystems and restore from snapshots
- Refer to `~/commands/emergency-recovery-commands.txt`

---

## 🎓 Best Practices Summary

### 🏆 Golden Rules:

1. **Snapshot Before, Experiment After** - Always create snapshots before changes
2. **Test Before Commit** - Use nix-shell for testing, install permanently only if satisfied
3. **Monitor Regularly** - Check system health weekly with monitoring scripts
4. **Clean Periodically** - Remove old snapshots and Nix generations monthly
5. **Document Everything** - Keep notes of changes and working configurations
6. **Verify Constantly** - Ensure automation is working and snapshots are being created
7. **Learn Gradually** - Understand one system at a time before complex operations

### 🎯 Success Metrics:

- **Zero permanent data loss** through proper snapshot usage
- **Quick recovery times** (<5 minutes for file restoration)
- **System stability** maintained across all experiments
- **Automated protection** working without manual intervention
- **Clean system state** with regular maintenance

---

## 📞 When You Need Help

**1. Check documentation first:**
- `~/commands/README.txt` - Quick reference
- `~/commands/emergency-recovery-commands.txt` - Emergency help

**2. Run diagnostics:**
```bash
~/system-config-log/snapshot-monitor.sh
~/system-config-log/system-alerts.sh
```

**3. Verify system state:**
```bash
systemctl list-timers | grep snapper
snapper -c home list | tail -10
df -h
```

**Remember:** This system is designed so you can't permanently break your computer. When in doubt, create a snapshot and experiment safely! 🛡️
