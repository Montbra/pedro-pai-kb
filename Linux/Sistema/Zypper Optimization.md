# Zypper Optimization Documentation

## Date: June 8, 2025

This document records the zypper optimizations applied to improve package management performance on the Lenovo Legion 5 Slim (16AHP9) running openSUSE Tumbleweed.

## System Information
- **CPU**: AMD Ryzen 7 8845HS
- **RAM**: 30 GB
- **Storage**: NVMe SSDs (Seagate XPG GAMMIX S11 Pro + Lexar SSD NM620)
- **OS**: openSUSE Tumbleweed (Kernel 6.13.8-1-default)

## Original Configuration Backup
The original zypper configuration has been backed up to:
```
/etc/zypp/zypp.conf.bak
```

## Optimizations Applied

### 1. Delta RPM Usage
```
download.use_deltarpm = true
download.use_deltarpm.always = true
```
**Purpose**: Reduces download sizes by using binary diffs instead of full packages.

### 2. Solver Behavior Optimization
```
solver.onlyRequires = true
solver.focus = speed
```
**Purpose**: Speeds up dependency resolution by focusing on required packages only and prioritizing speed.

### 3. Repository Refresh Delay Reduction
```
repo.refresh.delay = 5
```
**Purpose**: Reduces the minimum time between repository refreshes from 10 to 5 minutes.

### 4. Download Timeout Optimization
```
download.connect_timeout = 30
download.transfer_timeout = 120
```
**Purpose**: Reduces connection timeout from 60 to 30 seconds and transfer timeout from 180 to 120 seconds.

### 5. Download Retry Attempts
```
download.max_silent_tries = 3
```
**Purpose**: Sets a reasonable number of retry attempts for downloads.

### 6. Cache Management
```
keeppackages = 1
```
**Purpose**: Keeps downloaded packages in the cache for potential reuse.

### 7. Auto-agree with Licenses
```
installRecommends = false
```
**Purpose**: Skips installation of recommended packages to speed up installations.

## Existing Optimizations Maintained
- Parallel downloads: `download.max_concurrent_connections = 10`
- Vendor change protection: `solver.dupAllowVendorChange = false`
- Multiversion kernel settings

## Reverting Changes
If issues occur, restore the original configuration:
```bash
sudo cp /etc/zypp/zypp.conf.bak /etc/zypp/zypp.conf
```

## Performance Observations
- Initial observation: Zypper is noticeably faster with zstd compression
- After optimizations: [To be filled in after testing]

## Additional Notes
- The zstd compression algorithm is already being used by zypper (confirmed via `ldd $(which zypper) | grep -i zstd`)
- RPM packages use cpio archive format with zstd compression
