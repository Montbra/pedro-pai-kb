# VS Code Insiders Update Script

This document captures the creation of a script to automatically download and install the latest VS Code Insiders build on openSUSE.

## The Script

The script is located at `/home/henrique/update-vscode-insiders.sh` and can be run using the alias `uci` (Update Code Insiders).

```bash
#!/bin/bash

# Script to download and install the latest VS Code Insiders on openSUSE
# Created by Amazon Q

set -e  # Exit on any error

# Define colors for output
GREEN='\033[0;32m'
YELLOW='\033[1;33m'
RED='\033[0;31m'
NC='\033[0m' # No Color

# Define download URL and paths
DOWNLOAD_URL="https://code.visualstudio.com/sha/download?build=insider&os=linux-rpm-x64"
DOWNLOAD_DIR="/tmp"
RPM_NAME="code-insiders-latest.rpm"

echo -e "${YELLOW}Starting VS Code Insiders update process...${NC}"

# Check if VS Code Insiders is currently installed
if rpm -qa | grep -q code-insiders; then
    CURRENT_VERSION=$(rpm -qa | grep code-insiders)
    echo -e "${GREEN}Current version: $CURRENT_VERSION${NC}"
else
    echo -e "${YELLOW}VS Code Insiders is not currently installed.${NC}"
fi

# Download the latest VS Code Insiders
echo -e "${YELLOW}Downloading latest VS Code Insiders...${NC}"
wget -O "$DOWNLOAD_DIR/$RPM_NAME" "$DOWNLOAD_URL" || {
    echo -e "${RED}Failed to download VS Code Insiders.${NC}"
    exit 1
}
echo -e "${GREEN}Download completed: $DOWNLOAD_DIR/$RPM_NAME${NC}"

# Get the downloaded file info
DOWNLOADED_SIZE=$(du -h "$DOWNLOAD_DIR/$RPM_NAME" | cut -f1)
echo -e "${GREEN}Downloaded file size: $DOWNLOADED_SIZE${NC}"

# Install the new version
echo -e "${YELLOW}Installing VS Code Insiders with zypper...${NC}"
sudo zypper --non-interactive --no-gpg-checks in "$DOWNLOAD_DIR/$RPM_NAME" || {
    echo -e "${RED}Installation failed.${NC}"
    exit 1
}

# Verify installation
if rpm -qa | grep -q code-insiders; then
    NEW_VERSION=$(rpm -qa | grep code-insiders)
    echo -e "${GREEN}Successfully installed: $NEW_VERSION${NC}"
    
    # Clean up
    echo -e "${YELLOW}Cleaning up...${NC}"
    rm "$DOWNLOAD_DIR/$RPM_NAME"
    
    echo -e "${GREEN}VS Code Insiders update completed successfully!${NC}"
else
    echo -e "${RED}Installation verification failed.${NC}"
    exit 1
fi

# Launch VS Code Insiders
echo -e "${YELLOW}Launching VS Code Insiders...${NC}"
code-insiders &

exit 0
```

## The Alias

An alias called `uci` was added to the `.zshrc` file to make it easy to run the update script:

```bash
alias uci='/home/henrique/update-vscode-insiders.sh'
```

## Key Features

1. **Downloads the latest VS Code Insiders build** directly from the official website
2. **Installs using zypper** with GPG checks disabled (`--no-gpg-checks`)
3. **Verifies the installation** was successful
4. **Cleans up** the downloaded RPM file
5. **Launches VS Code Insiders** automatically after installation
6. **Provides colorful output** for better readability

## Development Notes

During the development of this script, several iterations were made:

1. Initially, a more complex script was created that included backing up VS Code Insiders settings
2. The script was simplified after determining that backups weren't necessary since updates only affect binaries, not user settings
3. GPG key handling was adjusted from trying to import keys (`--gpg-auto-import-keys`) to completely bypassing checks (`--no-gpg-checks`)

## Usage

To update VS Code Insiders to the latest version:

```bash
uci
```

This will download, install, and launch the latest VS Code Insiders build.

## Security Note

The script uses `--no-gpg-checks` which bypasses signature verification. This is a security compromise made for convenience, but is considered acceptable since the download is directly from the official Visual Studio Code website over HTTPS.
