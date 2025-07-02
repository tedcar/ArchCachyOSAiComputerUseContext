# **Project Bedrock Resilience: Implementation Task List**
## **From Strategic Plan to Executed Reality**

**Project Status:** READY FOR EXECUTION
**Total Estimated Time:** 8-12 hours across 4 phases
**Prerequisites:** Strategic roadmap approved (pr.md)

---

## **Pre-Execution Checklist**

### **SAFETY-001: System Backup Verification**
- [x] Verify current root snapshots exist: `sudo snapper -c root list`
- [x] Confirm last root snapshot is recent (within 24 hours)
- [ ] Test current rollback capability from GRUB menu
- [ ] Document current system state in notebook/file
- [x] Verify `/home` contains no critical unsaved work

### **SAFETY-002: Prerequisites Check**
- [x] Confirm available disk space: `df -h` (should show >600GB free)
- [x] Verify Btrfs subvolume layout: `sudo btrfs subvolume list /`
- [x] Confirm `/home` is mounted as `@home` subvolume
- [x] Test sudo access and permissions
- [ ] Close all non-essential applications

### **SAFETY-003: Documentation Setup**
- [x] Create working directory: `mkdir -p ~/system-config-log`
- [x] Create session log file: `touch ~/system-config-log/session-$(date +%Y%m%d).log`
- [ ] Test screenshot capability for documentation
- [ ] Prepare text editor for note-taking

---

## **PHASE 1: HOME DIRECTORY PROTECTION**
**Target:** Implement manual snapshot capability for `/home`
**Duration:** 1-2 hours
**Risk Level:** LOW

### **TASK-101: Create Home Snapper Configuration**
- [x] Execute: `sudo snapper -c home create-config /home`
- [x] Verify configuration created: `sudo snapper list-configs`
- [x] Confirm "home" appears in configuration list
- [x] Check configuration file exists: `ls -la /etc/snapper/configs/home`
- [x] Document configuration creation timestamp

### **TASK-102: Verify Snapshot Directory Setup**
- [x] Check `.snapshots` directory created: `ls -la /home/.snapshots`
- [x] Verify directory permissions and ownership
- [x] Test snapshot directory access: `sudo ls -la /home/.snapshots/`
- [x] Confirm no errors in system journal: `journalctl -u snapper -n 20`

### **TASK-103: Configure Home Snapshot Settings**
- [x] Open configuration file: `sudo nano /etc/snapper/configs/home`
- [x] Verify `TIMELINE_CREATE="no"` (manual-only initially)
- [x] Add exclusions line: `EXCLUDE_PATTERNS=".cache:.local/share/Trash:Downloads:.local/share/Steam"`
- [x] Set space limits: `SPACE_LIMIT="0.3"` and `FREE_LIMIT="0.1"`
- [x] Set user permissions: `ALLOW_USERS="carnateo"` or `ALLOW_GROUPS="wheel"`
- [x] Save and close file

### **TASK-104: Test Manual Snapshot Creation**
- [x] Create test snapshot: `sudo snapper -c home create --description "test-manual-snapshot"`
- [x] Verify snapshot created: `sudo snapper -c home list`
- [x] Note snapshot number from list output
- [x] Browse snapshot directory: `sudo ls -la /home/.snapshots/[NUMBER]/snapshot/`
- [x] Confirm your user directory visible in snapshot

### **TASK-105: Test File Restoration Procedure**
- [x] Create test file: `echo "test content" > ~/test-restore-file.txt`
- [x] Create snapshot: `sudo snapper -c home create --description "before-test-modification"`
- [x] Modify test file: `echo "modified content" >> ~/test-restore-file.txt`
- [x] Identify snapshot with original file: `sudo snapper -c home list`
- [x] Restore original file: `sudo cp /home/.snapshots/[NUMBER]/snapshot/$(whoami)/test-restore-file.txt ~/test-restore-file.txt`
- [x] Verify file restoration: `cat ~/test-restore-file.txt`
- [x] Clean up test file: `rm ~/test-restore-file.txt`

### **TASK-106: Document Manual Workflow**
- [x] Create reference file: `nano ~/system-config-log/home-snapshot-commands.txt`
- [x] Document snapshot creation command
- [x] Document snapshot listing command
- [x] Document file restoration procedure
- [x] Document cleanup commands
- [x] Test all documented commands work correctly

---

## **PHASE 2: TIMELINE AUTOMATION**
**Target:** Implement sophisticated automatic snapshot timeline
**Duration:** 2-3 hours
**Risk Level:** MEDIUM

### **TASK-201: Configure Timeline Parameters**
- [x] Open home config: `sudo nano /etc/snapper/configs/home`
- [x] Set `TIMELINE_CREATE="yes"`
- [x] Set `TIMELINE_LIMIT_DAILY="3"`
- [x] Set `TIMELINE_LIMIT_WEEKLY="4"`
- [x] Set `TIMELINE_LIMIT_MONTHLY="0"`
- [x] Set `TIMELINE_LIMIT_YEARLY="0"`
- [x] Set `TIMELINE_MIN_AGE="1800"` (30 minutes)
- [x] Save configuration file

### **TASK-202: Enable Timeline Services**
- [x] Enable timeline timer: `sudo systemctl enable snapper-timeline.timer`
- [x] Start timeline timer: `sudo systemctl start snapper-timeline.timer`
- [x] Enable cleanup timer: `sudo systemctl enable snapper-cleanup.timer`
- [x] Start cleanup timer: `sudo systemctl start snapper-cleanup.timer`
- [x] Verify timers active: `systemctl status snapper-timeline.timer`
- [x] Check cleanup timer: `systemctl status snapper-cleanup.timer`

### **TASK-203: Verify Timeline Configuration**
- [x] Check timer schedule: `systemctl list-timers | grep snapper`
- [x] Verify next run times are reasonable
- [x] Check service configurations: `systemctl cat snapper-timeline.service`
- [x] Verify home config is included in service
- [x] Test manual timeline run: `sudo systemctl start snapper-timeline.service`
- [x] Check for new snapshots: `sudo snapper -c home list`

### **TASK-204: Test Automatic Cleanup**
- [x] Create multiple test snapshots (5-6): `sudo snapper -c home create --description "test-cleanup-[1-6]"`
- [x] Verify all snapshots created: `sudo snapper -c home list`
- [x] Run cleanup manually: `sudo systemctl start snapper-cleanup.service`
- [x] Verify cleanup worked: `sudo snapper -c home list`
- [x] Confirm retention limits respected
- [x] Check cleanup service logs: `journalctl -u snapper-cleanup.service -n 20`

### **TASK-205: Monitor Storage Impact**
- [x] Record baseline storage: `df -h /home` and note usage
- [x] Wait for several automatic snapshots (or create manually)
- [x] Monitor storage growth: `df -h /home`
- [x] Check snapshot space usage: `sudo btrfs filesystem show /home`
- [x] Verify space usage is reasonable (<5% overhead)
- [x] Document storage usage patterns

### **TASK-206: Setup Monthly Archive System**
- [x] Create monthly snapshot script: `sudo nano /usr/local/bin/monthly-home-archive.sh`
- [x] Add script content (see script template below)
- [x] Make executable: `sudo chmod +x /usr/local/bin/monthly-home-archive.sh`
- [x] Test script execution: `sudo /usr/local/bin/monthly-home-archive.sh`
- [x] Verify monthly snapshot created with "important" tag
- [x] Check important snapshots: `sudo snapper -c home list | grep important`

**Monthly Archive Script Template:**
```bash
#!/bin/bash
# Monthly Home Archive Script
DATE=$(date +%Y-%m)
DESCRIPTION="monthly-archive-$DATE"
snapper -c home create --description "$DESCRIPTION"
SNAPSHOT_NUM=$(snapper -c home list | tail -1 | awk '{print $1}')
snapper -c home modify $SNAPSHOT_NUM --userdata "important=yes"
echo "Monthly archive snapshot #$SNAPSHOT_NUM created: $DESCRIPTION"
```

### **TASK-207: Schedule Monthly Archives**
- [x] Create systemd service: `sudo nano /etc/systemd/system/monthly-home-archive.service`
- [x] Create systemd timer: `sudo nano /etc/systemd/system/monthly-home-archive.timer`
- [x] Reload systemd: `sudo systemctl daemon-reload`
- [x] Enable monthly timer: `sudo systemctl enable monthly-home-archive.timer`
- [x] Start monthly timer: `sudo systemctl start monthly-home-archive.timer`
- [x] Verify timer scheduled: `systemctl list-timers | grep monthly`

---

## **PHASE 3: NIX INTEGRATION**
**Target:** Install and integrate Nix package manager with snapshot system
**Duration:** 4-6 hours
**Risk Level:** MEDIUM-HIGH

### **TASK-301: Pre-Nix System Preparation**
- [x] Create system checkpoint: `sudo snapper -c root create --description "pre-nix-installation"`
- [x] Create home checkpoint: `sudo snapper -c home create --description "pre-nix-installation"`
- [x] Verify checkpoints: `sudo snapper -c root list` and `sudo snapper -c home list`
- [x] Check available space: `df -h` (should have >100GB free)
- [x] Update system: `sudo pacman -Syu`
- [ ] Reboot system to ensure clean state

### **TASK-302: Install Nix Package Manager**
- [x] Download Nix installer: `curl -L https://nixos.org/nix/install | sh`
- [x] Source Nix environment: `. ~/.nix-profile/etc/profile.d/nix.sh`
- [x] Verify Nix installation: `nix --version`
- [x] Test basic Nix command: `nix-shell -p hello --run "hello"`
- [x] Check Nix store created: `ls -la /nix/`
- [x] Verify Nix daemon running: `systemctl status nix-daemon`

### **TASK-303: Configure Nix Subvolume (Optional)**
- [x] Stop Nix daemon: `sudo systemctl stop nix-daemon`
- [x] Backup current Nix: `sudo cp -r /nix /nix-backup`
- [x] Create Nix subvolume: `sudo btrfs subvolume create /mnt/@nix`
- [x] Move Nix store: `sudo mv /nix/* /mnt/@nix/`
- [x] Mount new subvolume: `sudo mount -o subvol=@nix /dev/nvme0n1p5 /nix`
- [x] Update fstab: `sudo nano /etc/fstab` (add Nix mount entry)
- [x] Test Nix works: `nix-shell -p hello --run "hello"`
- [x] Start Nix daemon: `sudo systemctl start nix-daemon`

### **TASK-304: Test Nix Package Operations**
- [x] Install test package: `nix-env -iA nixpkgs.tree`
- [x] Verify package works: `tree --version`
- [x] Check Nix generations: `nix-env --list-generations`
- [x] Install another package: `nix-env -iA nixpkgs.htop`
- [x] Check updated generations: `nix-env --list-generations`
- [x] Test Nix rollback: `nix-env --rollback`
- [x] Verify rollback worked: `nix-env --list-generations`

### **TASK-305: Add Nix to Snapper (Optional)**
- [x] Verify `/nix` is subvolume: `sudo btrfs subvolume list / | grep nix`
- [x] Create Nix Snapper config: `sudo snapper -c nix create-config /nix`
- [x] Configure Nix snapshots: `sudo nano /etc/snapper/configs/nix`
- [x] Set conservative limits: `TIMELINE_LIMIT_DAILY="1"`, `TIMELINE_LIMIT_WEEKLY="2"`
- [x] Set `TIMELINE_CREATE="no"` (manual only initially)
- [x] Test Nix snapshot: `sudo snapper -c nix create --description "nix-baseline"`
- [x] Verify Nix snapshot: `sudo snapper -c nix list`

### **TASK-306: Document Nix Workflow**
- [x] Create Nix reference: `nano ~/commands/nix-commands.txt`
- [x] Document package installation procedure
- [x] Document generation management commands
- [x] Document rollback procedures
- [x] Document snapshot integration workflow
- [x] Test all documented procedures work

---

## **PHASE 4: ADVANCED FEATURES**
**Target:** Implement monitoring, optimization, and advanced management
**Duration:** 2-4 hours
**Risk Level:** LOW

### **TASK-401: Performance Monitoring Setup**
- [x] Create monitoring script: `nano ~/system-config-log/snapshot-monitor.sh`
- [x] Add disk usage monitoring
- [x] Add snapshot count monitoring
- [x] Add performance impact monitoring
- [x] Make executable: `chmod +x ~/system-config-log/snapshot-monitor.sh`
- [x] Test monitoring script execution
- [x] Schedule monitoring: Add to crontab or systemd timer

### **TASK-402: Optimization Implementation**
- [x] Enable Btrfs compression verification: `mount | grep compress`
- [x] Check for duplicate data: `sudo duperemove -r /home` (dry run)
- [x] Implement defragmentation schedule if needed
- [x] Configure swap file if not present
- [x] Optimize Snapper cleanup settings based on usage patterns
- [x] Document optimization settings

### **TASK-403: Advanced Restoration Tools**
- [x] Create restoration helper script: `nano ~/system-config-log/restore-helper.sh`
- [x] Add interactive snapshot browser
- [x] Add file comparison tools
- [x] Add batch restoration capabilities
- [x] Test restoration helper with various scenarios
- [x] Document advanced restoration procedures

### **TASK-404: Alerting and Notifications**
- [x] Configure snapshot failure notifications
- [x] Set up storage space alerts
- [x] Configure cleanup failure alerts
- [x] Test notification system
- [x] Document notification management

### **TASK-405: System Integration Verification**
- [x] Test complete system rollback scenarios
- [x] Verify all components work together
- [x] Test recovery from various failure modes
- [x] Document integration verification results
- [x] Create emergency recovery procedures

---

## **VALIDATION CHECKLIST**

### **End-to-End Testing**
- [x] **Hyprland Config Test:** Modify Hyprland config, snapshot, break it, restore it
- [x] **Nix Package Test:** Install packages, rollback, verify state consistency
- [x] **Timeline Test:** Verify automatic snapshots happening on schedule
- [x] **Storage Test:** Confirm storage usage within expected parameters
- [x] **Performance Test:** Verify no significant performance degradation

### **Recovery Scenario Testing**
- [x] **Individual File Recovery:** Test restoring single config file
- [x] **Directory Recovery:** Test restoring entire config directory
- [x] **Full Home Recovery:** Test rolling back entire home directory
- [x] **System Recovery:** Test root filesystem rollback
- [x] **Nix Environment Recovery:** Test Nix generation rollback

### **Documentation Verification**
- [x] All procedures documented and tested
- [x] Emergency recovery procedures available
- [x] Command references accurate and complete
- [x] Troubleshooting guides created
- [x] Success metrics achieved and documented

---

## **COMPLETION CRITERIA**

### **Technical Success Criteria**
- [x] Zero data loss during any tested recovery scenario
- [x] Recovery time <5 minutes for any snapshot restoration
- [x] Storage overhead <15% of home directory size
- [x] Performance impact <2% on daily operations
- [x] All automated systems running without errors

### **User Experience Success Criteria**
- [x] Confidence in experimenting with system configurations
- [x] One-command rollback capability for mistakes
- [x] Surgical file recovery without full rollbacks
- [x] Clear understanding of all recovery procedures
- [x] System reliability enables aggressive learning and experimentation

---

## **PROJECT COMPLETION SIGN-OFF**

**Phase 1 Complete:** ☑ Home directory protection operational
**Phase 2 Complete:** ☑ Timeline automation functional  
**Phase 3 Complete:** ☑ Nix integration successful  
**Phase 4 Complete:** ☑ Advanced features implemented

**Final Verification:** ☑ All success criteria met  
**Documentation Complete:** ☑ All procedures documented and tested  
**User Training Complete:** ☑ User comfortable with all procedures  

**PROJECT STATUS:** ☑ **COMPLETE - ULTIMATE TINKERER'S SAFETY NET ACHIEVED**

---

*This task list represents the complete implementation roadmap. Each task should be completed in order, with verification at each step. If any task fails, stop and resolve the issue before proceeding to ensure system stability and reliability.*
