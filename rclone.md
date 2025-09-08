# Complete rclone Google Drive Setup Guide

This comprehensive guide covers everything from installing rclone to mounting Google Drive on Linux systems, with special considerations for immutable distributions like Bazzite.

## Table of Contents
1. [Installing rclone](#installing-rclone)
2. [Setting up Google Drive API](#setting-up-google-drive-api)
3. [Configuring rclone](#configuring-rclone)
4. [Testing the Connection](#testing-the-connection)
5. [Mounting Google Drive](#mounting-google-drive)
6. [Troubleshooting](#troubleshooting)
7. [Advanced Usage](#advanced-usage)

## Installing rclone

### Method 1: Homebrew (Linux/macOS)
```bash
# Install Homebrew if not already installed
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Install rclone
brew install rclone
```

### Method 2: System Package Manager
```bash
# Fedora/RHEL/CentOS
sudo dnf install rclone

# Ubuntu/Debian
sudo apt update && sudo apt install rclone

# Arch Linux
sudo pacman -S rclone

# Bazzite (immutable Fedora)
rpm-ostree install rclone
sudo systemctl reboot
```

### Method 3: Official Script
```bash
curl https://rclone.org/install.sh | sudo bash
```

### Method 4: Flatpak
```bash
flatpak install flathub org.rclone.rclone
```

## Setting up Google Drive API

### Step 1: Create a Google Cloud Project

1. Go to the [Google Cloud Console](https://console.cloud.google.com/)
2. Sign in with your Google account
3. Click on "Select a project" dropdown at the top
4. Click "New Project"
5. Enter a project name (e.g., "rclone-gdrive")
6. Click "Create"
7. Wait for the project to be created and select it

### Step 2: Enable Google Drive API

1. In the Google Cloud Console, navigate to "APIs & Services" > "Library"
2. Search for "Google Drive API"
3. Click on "Google Drive API"
4. Click "Enable"
5. Wait for the API to be enabled

### Step 3: Create Credentials

#### For Personal Use (OAuth 2.0):

1. Go to "APIs & Services" > "Credentials"
2. Click "Create Credentials" > "OAuth client ID"
3. If prompted, configure the OAuth consent screen:
   - Choose "External" user type
   - Fill in the required fields:
     - App name: "rclone" (or any name you prefer)
     - User support email: your email
     - Developer contact information: your email
   - Click "Save and Continue"
   - Skip adding scopes (click "Save and Continue")
   - Add test users if needed, or click "Save and Continue"
4. Back in Credentials, click "Create Credentials" > "OAuth client ID"
5. Choose "Desktop application"
6. Name it "rclone" or similar
7. Click "Create"
8. **Important**: Copy and save the Client ID and Client Secret

#### Alternative: Use rclone's Built-in Credentials
For quick setup, you can use rclone's built-in Google Drive credentials, but creating your own is recommended for better performance and reliability.

## Configuring rclone

### Step 1: Start Configuration
```bash
rclone config
```

### Step 2: Create New Remote
1. Type `n` for "New remote"
2. Enter a name for your remote (e.g., `gdrive`, `mygdrive`, or `l.ofwarhd`)
3. From the list of storage providers, select the number for "Google Drive" (usually `15` or `18`)

### Step 3: Configure Google Drive Settings

1. **Client ID**: 
   - If you created your own: paste your Client ID
   - If using rclone's built-in: press Enter to leave blank

2. **Client Secret**: 
   - If you created your own: paste your Client Secret
   - If using rclone's built-in: press Enter to leave blank

3. **Scope**: Choose option `1` (Full access to all files)

4. **Service Account File**: Press Enter (leave blank for personal use)

5. **Edit Advanced Config**: Type `n` (No)

6. **Use Auto Config**: 
   - Type `y` (Yes) if you're on a desktop with a browser
   - Type `n` (No) if you're on a headless server

### Step 4: Authentication

#### If you chose "Yes" for auto config:
1. A browser window will open
2. Sign in to your Google account
3. Grant permissions to the application
4. You'll see a success message

#### If you chose "No" for auto config:
1. rclone will display a URL
2. Copy this URL and open it on a device with a browser
3. Sign in and authorize the application
4. Copy the authorization code
5. Paste it back in the terminal

### Step 5: Finish Configuration
1. Choose `n` when asked about team drives (unless you need them)
2. Review the configuration
3. Type `y` to confirm and save
4. Type `q` to quit the config menu

## Testing the Connection

### List files in your Google Drive root:
```bash
rclone ls gdrive:
```

### List directories:
```bash
rclone lsd gdrive:
```

### Check quota and usage:
```bash
rclone about gdrive:
```

## Mounting Google Drive

### Step 1: Create Mount Point
```bash
mkdir -p ~/mnt/gdrive
# Replace 'gdrive' with your remote name
```

### Step 2: Basic Mount Command

#### For Homebrew rclone on Bazzite/immutable systems:
```bash
export PATH="/usr/sbin:/usr/bin:$PATH"
rclone mount gdrive: ~/mnt/gdrive --vfs-cache-mode writes
```

#### For system rclone:
```bash
rclone mount gdrive: ~/mnt/gdrive --vfs-cache-mode writes
```

### Step 3: Mount with Additional Options
```bash
# Mount with better performance and caching
rclone mount gdrive: ~/mnt/gdrive \
  --vfs-cache-mode writes \
  --vfs-cache-max-size 1G \
  --vfs-cache-max-age 1h \
  --buffer-size 256M \
  --dir-cache-time 72h \
  --drive-chunk-size 256M \
  --timeout 1h \
  --umask 002 \
  --allow-other
```

### Step 4: Mount as Background Service
```bash
# Mount in daemon mode
rclone mount gdrive: ~/mnt/gdrive \
  --vfs-cache-mode writes \
  --daemon
```

### Step 5: Unmount
```bash
# Unmount the drive
fusermount -u ~/mnt/gdrive
# or
fusermount3 -u ~/mnt/gdrive
```

## Troubleshooting

### Common Issues and Solutions

#### 1. "Operation not permitted" Error
**Problem**: FUSE mount fails with permission error

**Solutions**:
```bash
# For Homebrew rclone on Bazzite/immutable systems
export PATH="/usr/sbin:/usr/bin:$PATH"

# Check fuse.conf
sudo nano /etc/fuse.conf
# Uncomment: user_allow_other

# Add to your shell profile for permanent fix
echo 'export PATH="/usr/sbin:/usr/bin:$PATH"' >> ~/.bashrc
```

#### 2. "Transport endpoint is not connected"
**Problem**: Mount point becomes unresponsive

**Solution**:
```bash
# Force unmount
sudo fusermount -uz ~/mnt/gdrive
# or
sudo umount -l ~/mnt/gdrive
```

#### 3. Slow Performance
**Solutions**:
```bash
# Use better cache settings
rclone mount gdrive: ~/mnt/gdrive \
  --vfs-cache-mode full \
  --vfs-cache-max-size 10G \
  --buffer-size 1G \
  --drive-chunk-size 256M
```

#### 4. "Client_id not found" Error
**Problem**: Invalid or expired credentials

**Solution**:
```bash
# Reconfigure the remote
rclone config reconnect gdrive:
# or delete and recreate
rclone config delete gdrive
rclone config
```

### Log Analysis
```bash
# Run with verbose logging
rclone mount gdrive: ~/mnt/gdrive --vfs-cache-mode writes -v

# Maximum logging detail
rclone mount gdrive: ~/mnt/gdrive --vfs-cache-mode writes -vv --log-file ~/rclone.log
```

## Advanced Usage

### Creating a Systemd Service

Create a systemd service file for automatic mounting:

```bash
sudo nano /etc/systemd/system/rclone-gdrive.service
```

Add the following content:
```ini
[Unit]
Description=RClone mount of Google Drive
AssertPathIsDirectory=/home/yourusername/mnt/gdrive
After=network-online.target

[Service]
Type=notify
User=yourusername
Group=yourusername
ExecStartPre=/bin/mkdir -p /home/yourusername/mnt/gdrive
ExecStart=/usr/bin/rclone mount gdrive: /home/yourusername/mnt/gdrive \
  --config /home/yourusername/.config/rclone/rclone.conf \
  --vfs-cache-mode writes \
  --vfs-cache-max-size 1G \
  --buffer-size 256M \
  --allow-other
ExecStop=/bin/fusermount -u /home/yourusername/mnt/gdrive
Restart=always
RestartSec=10

[Install]
WantedBy=default.target
```

Enable and start the service:
```bash
sudo systemctl enable rclone-gdrive.service
sudo systemctl start rclone-gdrive.service
sudo systemctl status rclone-gdrive.service
```

### Useful Mount Options Explained

- `--vfs-cache-mode writes`: Cache writes to improve performance
- `--vfs-cache-mode full`: Cache reads and writes (uses more disk space)
- `--vfs-cache-max-size 1G`: Maximum cache size
- `--vfs-cache-max-age 1h`: Maximum age of cache files
- `--buffer-size 256M`: Buffer size for each open file
- `--dir-cache-time 72h`: How long to cache directory listings
- `--drive-chunk-size 256M`: Upload chunk size
- `--allow-other`: Allow other users to access the mount
- `--daemon`: Run in background
- `--umask 002`: Set file permissions
- `--timeout 1h`: IO idle timeout

### Selective Sync

Mount only specific folders:
```bash
rclone mount gdrive:Documents ~/mnt/gdrive-docs --vfs-cache-mode writes
rclone mount gdrive:"My Folder" ~/mnt/gdrive-folder --vfs-cache-mode writes
```

### Configuration File Location

Your rclone configuration is stored at:
- Linux: `~/.config/rclone/rclone.conf`
- macOS: `~/Library/Application Support/rclone/rclone.conf`

### Backup Your Configuration

```bash
# Backup config file
cp ~/.config/rclone/rclone.conf ~/.config/rclone/rclone.conf.backup

# Or export encrypted config
rclone config show > ~/rclone-config-backup.txt
```

## Security Considerations

1. **Keep your credentials secure**: Never share your `rclone.conf` file
2. **Use service accounts**: For server deployments, consider using service accoun
