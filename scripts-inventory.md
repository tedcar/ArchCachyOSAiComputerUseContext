# Scripts and Tools Inventory
**Project Bedrock Resilience - Complete Script Documentation**

## Overview

This document provides a comprehensive inventory of all scripts, tools, and automated systems created for the three-layer backup and recovery system. Each script serves a specific purpose in maintaining system reliability and enabling advanced recovery capabilities.

## Script Categories

### 🏠 **System Administration Scripts**
Located in: `~/system-config-log/`

### 🔧 **System Integration Scripts**  
Located in: `/usr/local/bin/`

### 📚 **Reference Documentation**
Located in: `~/commands/`

---

## Detailed Script Inventory

### 1. Comprehensive System Monitor
**File:** `~/system-config-log/snapshot-monitor.sh`
**Purpose:** Primary system health monitoring and reporting tool
**Status:** ✅ Critical - DO NOT DELETE

**What it does:**
- Monitors all three snapshot layers (root, home, nix)
- Analyzes disk usage with color-coded alerts
- Checks Btrfs filesystem health and compression
- Monitors systemd services status
- Assesses performance impact of snapshot system
- Analyzes storage efficiency and overhead
- Generates intelligent recommendations
- Creates detailed logs with timestamps

**How to use:**
```bash
~/system-config-log/snapshot-monitor.sh
```

**When to use:**
- Weekly system health checks
- Before/after major changes
- When investigating performance issues
- As part of regular maintenance

**Output locations:**
- Screen: Colored comprehensive report
- Log file: `~/system-config-log/snapshot-monitor-YYYYMMDD.log`

---

### 2. System Alerts and Notifications
**File:** `~/system-config-log/system-alerts.sh`
**Purpose:** Proactive monitoring and alerting system
**Status:** ✅ Important - DO NOT DELETE

**What it does:**
- Monitors disk space thresholds (alerts at 85%)
- Detects service failures automatically
- Checks snapshot count limits (alerts at 50+)
- Monitors performance degradation
- Sends notifications for critical issues
- Integrates with system logging

**How to use:**
```bash
~/system-config-log/system-alerts.sh
```

**Automation potential:**
- Can be added to cron for automated monitoring
- Recommended: Run daily via systemd timer

---

### 3. Advanced Restoration Helper
**File:** `~/system-config-log/restore-helper.sh`
**Purpose:** Interactive file and directory restoration tool
**Status:** ✅ Important - DO NOT DELETE

**What it does:**
- Provides interactive snapshot browsing
- Compares files between snapshots and current state
- Enables batch restoration of multiple files
- Offers selective restoration options
- Guides users through recovery processes
- Validates restoration operations

**How to use:**
```bash
~/system-config-log/restore-helper.sh
```

**When to use:**
- When you need to restore specific files
- For comparing different versions of files
- When doing complex recovery operations
- For guided restoration assistance

---

### 4. Weekly System Optimization
**File:** `~/system-config-log/weekly-optimization.sh`
**Purpose:** Automated system maintenance and optimization
**Status:** ✅ Maintenance - DO NOT DELETE

**What it does:**
- Performs Btrfs filesystem optimization (balance, scrub)
- Runs duplicate file removal (duperemove)
- Cleans up system caches and temporary files
- Optimizes snapshot storage efficiency
- Tunes system performance parameters
- Reports optimization results

**How to use:**
```bash
~/system-config-log/weekly-optimization.sh
```

**Recommended schedule:**
- Run weekly during low-usage periods
- Can be automated via systemd timer

---

### 5. System Integration Tester
**File:** `~/system-config-log/system-integration-test.sh`
**Purpose:** Comprehensive system validation and testing
**Status:** ✅ Validation - DO NOT DELETE

**What it does:**
- Tests snapshot creation across all layers
- Validates restoration procedures
- Checks service integration and communication
- Performs end-to-end recovery testing
- Benchmarks system performance
- Verifies all automation systems
- Generates comprehensive test reports

**How to use:**
```bash
~/system-config-log/system-integration-test.sh
```

**When to use:**
- After system updates
- Before major changes
- Monthly validation runs
- Troubleshooting system issues

---

### 6. Monthly Home Archive Creator
**File:** `/usr/local/bin/monthly-home-archive.sh`
**Purpose:** Creates permanent monthly snapshots
**Status:** ✅ Critical - DO NOT DELETE
**Automation:** Runs automatically via systemd timer

**What it does:**
- Creates monthly "important" snapshots of home directory
- Marks snapshots to never be auto-deleted
- Prevents duplicate monthly archives
- Provides comprehensive error handling
- Logs all operations with timestamps
- Integrates with cleanup systems

**Automation schedule:**
- Runs 1st of each month at 02:00 AM
- Includes randomization (±30 minutes)
- Persistent across reboots

**Manual usage:**
```bash
sudo /usr/local/bin/monthly-home-archive.sh
```

**Log location:** `~/system-config-log/monthly-archive.log`

---

## Support Tools and Utilities

### 7. System Monitor (Simplified)
**File:** `~/system-config-log/system-monitor.sh`
**Purpose:** Lightweight system monitoring
**Status:** ✅ Utility

**What it does:**
- Quick system overview
- Basic health checks
- Simplified reporting for daily use

---

### 8. Additional Monitoring Scripts
**Files:** Various monitoring utilities in `~/system-config-log/`
**Purpose:** Specialized monitoring for specific components
**Status:** ✅ Supporting tools

---

## Command Reference Documentation

### Location: `~/commands/`

**Core Reference Files:**
- `README.txt` - Master index and quick start guide
- `home-snapshot-commands.txt` - Home directory operations
- `timeline-automation-commands.txt` - Automation management
- `system-monitoring-commands.txt` - Monitoring procedures
- `emergency-recovery-commands.txt` - Emergency procedures
- `nix-commands.txt` - Package management with Nix

**Purpose:** Quick reference for daily operations and emergency procedures

---

## System Integration Points

### Systemd Services Using Scripts

**monthly-home-archive.timer/service:**
- **Script:** `/usr/local/bin/monthly-home-archive.sh`
- **Schedule:** Monthly on 1st at 02:00
- **Purpose:** Automated important snapshots

**Potential Future Integrations:**
- `weekly-optimization.timer` → `weekly-optimization.sh`
- `daily-health-check.timer` → `system-alerts.sh`
- `system-validation.timer` → `system-integration-test.sh`

---

## Script Dependencies

### Required for System Operation:
1. `/usr/local/bin/monthly-home-archive.sh` - Archive automation
2. `~/system-config-log/snapshot-monitor.sh` - Health monitoring

### Important for Maintenance:
3. `~/system-config-log/weekly-optimization.sh` - Performance
4. `~/system-config-log/system-alerts.sh` - Issue detection
5. `~/system-config-log/restore-helper.sh` - Recovery assistance

### Validation and Testing:
6. `~/system-config-log/system-integration-test.sh` - System validation

---

## Usage Patterns

### Daily Operations:
- Use command references in `~/commands/`
- Run `snapshot-monitor.sh` for health checks

### Weekly Maintenance:
- Execute `weekly-optimization.sh`
- Review `system-alerts.sh` output

### Monthly Reviews:
- Verify `monthly-home-archive.sh` execution
- Run `system-integration-test.sh`

### Emergency Situations:
- Use `restore-helper.sh` for guided recovery
- Reference `emergency-recovery-commands.txt`

---

## Protection and Backup of Scripts

### Critical Script Locations:
- **System scripts:** `/usr/local/bin/` (protected, requires root to modify)
- **User scripts:** `~/system-config-log/` (protected by home snapshots)
- **Documentation:** `~/commands/` (protected by home snapshots)

### Backup Strategy:
- All user scripts are protected by home directory snapshots
- System scripts are protected by root filesystem snapshots
- Documentation is automatically backed up in timeline snapshots
- Monthly archives provide long-term script preservation

### Recovery of Scripts:
If scripts are accidentally deleted, they can be recovered from:
1. **Home snapshots:** `snapper -c home list` → restore from appropriate snapshot
2. **Root snapshots:** `sudo snapper -c root list` → restore system scripts
3. **Monthly archives:** Long-term recovery option

---

## Script Maintenance Guidelines

### DO NOT DELETE:
- Any script in `/usr/local/bin/`
- Any script in `~/system-config-log/`
- Any documentation in `~/commands/`

### SAFE TO MODIFY:
- Script parameters and thresholds (after understanding impact)
- Log locations and retention
- Notification preferences

### MODIFICATION BEST PRACTICES:
1. Create snapshot before modifying: `snapper -c home create --description "pre-script-modification"`
2. Test modifications thoroughly
3. Keep backup copies of original scripts
4. Document any changes made

---

## Troubleshooting Script Issues

### Common Problems:
1. **Permission errors:** Check file permissions and ownership
2. **Missing dependencies:** Verify required tools are installed
3. **Path issues:** Ensure all paths in scripts are correct
4. **Service failures:** Check systemd service status

### Diagnostic Commands:
```bash
# Check script permissions
ls -la ~/system-config-log/*.sh

# Test script syntax
bash -n ~/system-config-log/script-name.sh

# Check systemd service status
systemctl status monthly-home-archive.service

# Review logs
journalctl -u monthly-home-archive.service -n 20
```

---

## Script Performance Impact

### Resource Usage:
- **Monitoring scripts:** Minimal CPU/memory impact
- **Optimization scripts:** Temporary increased I/O during execution
- **Archive scripts:** Brief CPU/disk usage during snapshot creation

### Execution Times:
- **snapshot-monitor.sh:** 30-60 seconds
- **weekly-optimization.sh:** 5-15 minutes
- **monthly-home-archive.sh:** 10-30 seconds
- **system-integration-test.sh:** 2-5 minutes

---

## Summary

This script ecosystem provides:
✅ **Automated Maintenance:** Monthly archives, optimization, monitoring
✅ **Interactive Tools:** Restoration assistance, health checking
✅ **Comprehensive Monitoring:** Multi-layer system observation
✅ **Recovery Assistance:** Guided restoration and emergency procedures
✅ **Performance Optimization:** Automated system tuning
✅ **Validation Testing:** System integrity verification

**Total Scripts:** 8+ comprehensive tools
**Protection Level:** Full backup coverage via snapshot system
**Maintenance Required:** Minimal - mostly automated
**User Interaction:** Optional - system runs autonomously

All scripts are designed to be self-contained, well-documented, and integrated with the three-layer protection system for maximum reliability and recoverability.