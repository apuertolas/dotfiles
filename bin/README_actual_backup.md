# Actual Database Backup Scripts

Configurable scripts for backing up and restoring [Actual Budget](https://actualbudget.org/) database files with optional compression and cloud storage support.

## 📋 Scripts Overview

- **`actual-backup`** - Backs up your Actual database with XZ compression
- **`actual-restore`** - Restores from the latest backup or a specific backup file

## 🚀 Quick Start

```bash
# Create a backup (uses defaults)
actual-backup

# Restore latest backup
actual-restore

# List available backups
actual-restore -l

# Show current configuration
actual-backup -c
actual-restore -c
```

## ⚙️ Configuration

### Priority Order
1. **Command line arguments** (highest priority)
2. **Environment variables**
3. **Configuration file** (`~/.config/actual-backup/config`)
4. **Built-in defaults** (lowest priority)

### Configuration File Setup

```bash
# Create the config directory and file
mkdir -p ~/.config/actual-backup
touch ~/.config/actual-backup/config

# Edit to match your setup
vim ~/.config/actual-backup/config
```

### Environment Variables

```bash
# Set environment variables (useful for scripting)
export ACTUAL_SOURCE_DIR="$HOME/actual/data/user-files"
export ACTUAL_BACKUP_DIR="$HOME/Dropbox/Actual_Backups"
export ACTUAL_MAX_BACKUPS=5
export ACTUAL_USE_COMPRESSION=true
export ACTUAL_COMPRESSION_TYPE="xz"
```

## 🔧 Script Features

### `actual-backup`

**Features:**
- 🗜️ **XZ compression** (78% space savings - 11MB → 2.5MB)
- ☁️ **Cloud storage support** (iCloud, Dropbox, Google Drive, etc.)
- 🧹 **Automatic cleanup** (keeps configurable number of old backups)
- 📊 **Compression statistics** and progress feedback
- 🛡️ **Error handling** and validation
- 🎨 **Colorized output** for better UX

**Usage:**
```bash
actual-backup [OPTIONS]

Options:
  -h, --help              Show help message
  -c, --config            Show current configuration
  -s, --source DIR        Source directory
  -d, --destination DIR   Backup destination directory  
  -m, --max-backups NUM   Maximum backups to keep (default: 10)
  --no-compression        Disable compression
  --compression TYPE      Compression: xz, gzip, bzip2 (default: xz)
```

**Examples:**
```bash
# Basic backup
actual-backup

# Custom paths
actual-backup -s ~/myapp/data -d ~/Dropbox/Backups

# Keep only 5 backups, no compression
actual-backup -m 5 --no-compression

# Use gzip compression instead of xz
actual-backup --compression gzip
```

### `actual-restore`

**Features:**
- 🔍 **Smart backup detection** (latest or specific file)
- 🛡️ **Safety backups** of existing files before restore
- ✋ **Confirmation prompts** to prevent accidental overwrites
- 📋 **Backup listing** and selection
- 🗂️ **Multi-format support** (xz, gzip, bzip2, uncompressed)
- 🧹 **Automatic cleanup** of temporary files

**Usage:**
```bash
actual-restore [OPTIONS] [BACKUP_FILE]

Options:
  -h, --help              Show help message
  -c, --config            Show current configuration
  -t, --target DIR        Target directory for restored files
  -b, --backup-dir DIR    Backup directory to search
  -l, --list              List available backups
  -f, --force             Skip confirmation prompts
  --no-safety-backup      Skip safety backup of existing files
```

**Examples:**
```bash
# Restore latest backup (with confirmation)
actual-restore

# List all available backups
actual-restore -l

# Restore specific backup file
actual-restore actual_backup_20240824_154500.tar.xz

# Force restore without prompts
actual-restore -f

# Custom target directory
actual-restore -t ~/myapp/data
```

## 🏗️ Installation & Setup

### 1. Add to PATH
If `~/.dotfiles/bin` is not in your PATH, add it to your shell profile:

```bash
# For zsh (add to ~/.zshrc)
export PATH="$HOME/.dotfiles/bin:$PATH"

# For bash (add to ~/.bashrc or ~/.bash_profile)  
export PATH="$HOME/.dotfiles/bin:$PATH"
```

### 2. Create Configuration
```bash
# Create config directory
mkdir -p ~/.config/actual-backup

# Create config file
touch ~/.config/actual-backup/config

# Edit configuration
vim ~/.config/actual-backup/config
```

### 3. Test Setup
```bash
# Test backup (dry-run style)
actual-backup -c

# Test restore listing  
actual-restore -l
```

## 📁 Default Paths

| Component | Default Path |
|-----------|--------------|
| **Source Data** | `~/actual/data/user-files` |
| **iCloud Backup** | `~/Library/Mobile Documents/com~apple~CloudDocs/Actual_Backups` |
| **Config File** | `~/.config/actual-backup/config` |
| **Safety Backups** | `~/actual/data/user-files.backup_TIMESTAMP` |

## 🗜️ Compression Comparison

| Method | Original | Compressed | Ratio | Savings | Speed |
|--------|----------|------------|-------|---------|-------|
| **None** | 11.2 MB | 11.2 MB | 100% | 0% | Fastest |
| **gzip** | 11.2 MB | 3.2 MB | 28% | 72% | Fast |
| **bzip2** | 11.2 MB | 3.1 MB | 27% | 73% | Medium |
| **xz** | 11.2 MB | **2.5 MB** | **22%** | **78%** | Slower |

**Recommendation:** Use XZ for maximum space savings, especially important for cloud storage.

## ☁️ Cloud Storage Setup

### iCloud Drive (Default)
```bash
BACKUP_DIR="$HOME/Library/Mobile Documents/com~apple~CloudDocs/Actual_Backups"
```
- ✅ Automatic sync across Apple devices
- ✅ Good for personal use
- ⚠️ May have sync delays

### Dropbox
```bash
BACKUP_DIR="$HOME/Dropbox/Backups/Actual"
```
- ✅ Fast, reliable sync
- ✅ Good compression handling
- ✅ Works across all platforms

### Google Drive
```bash  
BACKUP_DIR="$HOME/Google Drive/Backups/Actual"
```
- ✅ Large storage quota
- ⚠️ May be slower with many files

### External/Network Drives
```bash
BACKUP_DIR="/Volumes/MyExternalDrive/Actual_Backups"
```
- ✅ Full control over storage
- ✅ Fast local access
- ⚠️ Manual management required

## 🔄 Automation Examples

### Daily Backup with Cron
```bash
# Edit crontab
crontab -e

# Add daily backup at 2 AM
0 2 * * * /Users/yourusername/.dotfiles/bin/actual-backup
```

### Weekly Cleanup
```bash
# Weekly cleanup keeping only 4 backups
0 0 * * 0 ACTUAL_MAX_BACKUPS=4 /Users/yourusername/.dotfiles/bin/actual-backup
```

### Backup Before Updates
```bash
#!/bin/bash
# pre-update-backup.sh
echo "Creating backup before update..."
actual-backup
echo "Safe to proceed with update!"
```

## 🆘 Troubleshooting

### Common Issues

**"Source directory not found"**
```bash
# Check if Actual is in the expected location
ls -la ~/actual/data/user-files/

# Or specify custom location
actual-backup -s /path/to/your/actual/data/user-files
```

**"iCloud Drive not accessible"**
```bash
# Check iCloud Drive status
ls ~/Library/Mobile\ Documents/com~apple~CloudDocs/

# Use alternative storage
actual-backup -d ~/Dropbox/Backups
```

**"No backup files found"**
```bash
# List backup directory contents
ls -la ~/Library/Mobile\ Documents/com~apple~CloudDocs/Actual_Backups/

# Check if backups exist elsewhere
actual-restore -b ~/Dropbox/Backups -l
```

### Recovery Scenarios

**Accidental deletion:**
```bash
# Restore latest backup
actual-restore

# If safety backup exists
mv ~/actual/data/user-files.backup_20240824_154500 ~/actual/data/user-files
```

**Corrupted database:**
```bash
# Force restore latest good backup
actual-restore -f
```

**Migration to new machine:**
```bash
# On new machine, restore from cloud backup
actual-restore -b ~/Dropbox/Actual_Backups
```

## 🔐 Security Notes

- **Local safety backups** are created before each restore
- **No passwords** or sensitive data in config files
- **Cloud storage** - ensure proper account security
- **File permissions** - scripts maintain original file permissions

## 📈 Best Practices

1. **Regular backups** - Set up automated daily/weekly backups
2. **Test restores** - Periodically verify backup integrity  
3. **Multiple locations** - Consider backing up to multiple cloud services
4. **Monitor space** - XZ compression reduces storage needs by ~78%
5. **Document changes** - Note any custom configurations in your dotfiles

## 🤝 Integration with Dotfiles

These scripts follow dotfiles conventions:
- ✅ **No hardcoded personal info** (fully configurable)
- ✅ **Environment variable support** for flexibility  
- ✅ **XDG Base Directory** spec compliance (`~/.config/`)
- ✅ **Executable in `bin/`** directory
- ✅ **Comprehensive help** and configuration options

Perfect for sharing in your public dotfiles repository! 🎉
