# android-turbopack
This repo contains info about how to run turbopack on Android (arm 64bit).
This script automates the installation of unofficial Next.js SWC binaries for Android ARM64.
It is designed for developers running Next.js projects on a mobile device using Termux.

## Prerequisites
Before running the script, install the required tools in Termux:
```
pkg update && pkg install curl jq unzip
```

* curl : Used to download the binary.
* jq   : Used to parse package.json.
* unzip: Used to extract the downloaded assets.

## Usage
1. Download the Script
Save the install-next-swc.sh script into the root directory of your Next.js project.
```
nano install-next-swc.sh
```
```
#!/data/data/com.termux/files/usr/bin/bash

# ==========================================
# Next.js Android ARM64 Binary Installer
# ==========================================
# Description:
#   1. Detects the Next.js version from package.json
#   2. Downloads the matching pre-built binary from GitHub
#   3. Patches the existing WASM file (renames it)
#   4. Installs the new ARM64 binary into node_modules
# ==========================================

# --- Configuration & Constants ---
REPO_OWNER="Kuldeep-Dilliwar"
REPO_NAME="next.js"

# The file we want to download and extract
ZIP_FILENAME="next-swc-binaries-aarch64-linux-android.zip"
BINARY_FILENAME="next-swc.android-arm64.node"

# Paths inside node_modules
DESTINATION_DIR="./node_modules/next/next-swc-fallback/@next/swc-android-arm64/"
WASM_ORIGINAL="./node_modules/next/wasm/@next/swc-wasm-nodejs/wasm.js"
WASM_RENAMED="./node_modules/next/wasm/@next/swc-wasm-nodejs/wasm1.js"

# --- Helper Functions ---

# Print a formatted header for steps
function log_step() {
    echo -e "\n\033[1;34m[STEP]\033[0m $1"
}

# Print error message and exit
function error_exit() {
    echo -e "\033[1;31m[ERROR]\033[0m $1"
    exit 1
}

# 1. Get the Next.js version from package.json
function get_next_version() {
    log_step "Checking package.json for Next.js version..."
    
    if [ ! -f "package.json" ]; then
        error_exit "package.json not found. Are you in the project root?"
    fi

    # Extract version using jq, checking dependencies or devDependencies
    RAW_VERSION=$(jq -r '.dependencies.next // .devDependencies.next' package.json)

    if [ "$RAW_VERSION" == "null" ] || [ -z "$RAW_VERSION" ]; then
        error_exit "Next.js dependency not found in package.json."
    fi

    # Remove special chars like ^ or ~ (e.g., ^16.1.1 -> 16.1.1)
    CLEAN_VERSION=$(echo $RAW_VERSION | sed 's/[\^~]//g')
    echo "Detected Version: $CLEAN_VERSION"
    
    # Return the version for the next step
    echo "$CLEAN_VERSION"
}

# 2. Find the download URL from GitHub Releases
function get_download_url() {
    local version=$1
    local tag="v$version" # GitHub releases usually use 'v' prefix

    log_step "Fetching download URL for tag: $tag..."

    local api_url="https://api.github.com/repos/$REPO_OWNER/$REPO_NAME/releases"

    # Use jq to filter through releases to find the one matching our tag and asset name
    local url=$(curl -s $api_url | jq -r --arg tag "$tag" --arg asset "$ZIP_FILENAME" '
        .[] | select(.tag_name == $tag) | .assets[] | select(.name == $asset) | .browser_download_url
    ')

    if [ -z "$url" ] || [ "$url" == "null" ]; then
        error_exit "Release not found for $tag. Check https://github.com/$REPO_OWNER/$REPO_NAME/releases"
    fi

    echo "$url"
}

# 3. Rename the WASM file to prevent conflicts
function patch_wasm_file() {
    log_step "Patching WASM file (wasm.js -> wasm1.js)..."

    if [ -f "$WASM_ORIGINAL" ]; then
        mv "$WASM_ORIGINAL" "$WASM_RENAMED"
        echo "✅ Renamed wasm.js to wasm1.js"
    elif [ -f "$WASM_RENAMED" ]; then
        echo "ℹ️  File already renamed (wasm1.js exists). Skipping."
    else
        echo "⚠️  Warning: Original wasm.js not found at $WASM_ORIGINAL. Skipping rename."
    fi
}

# 4. Download, Extract, and Install
function install_binary() {
    local url=$1

    log_step "Downloading binary..."
    curl -L -o "$ZIP_FILENAME" "$url"

    # Verify download
    if [ ! -f "$ZIP_FILENAME" ]; then
        error_exit "Download failed."
    fi

    log_step "Extracting..."
    unzip -o "$ZIP_FILENAME" > /dev/null 2>&1

    # Verify extraction
    if [ ! -f "$BINARY_FILENAME" ]; then
        # Clean up zip before exiting
        rm "$ZIP_FILENAME"
        error_exit "Extraction failed. $BINARY_FILENAME not found inside zip."
    fi

    log_step "Installing to node_modules..."
    mkdir -p "$DESTINATION_DIR"
    mv "$BINARY_FILENAME" "$DESTINATION_DIR"
    echo "✅ Binary moved to $DESTINATION_DIR"

    # Cleanup zip file
    rm "$ZIP_FILENAME"
}

# --- Main Execution Flow ---

# A. Get Version
VERSION=$(get_next_version)

# B. Get URL
DOWNLOAD_URL=$(get_download_url "$VERSION")

# C. Patch the WASM file (Rename Step)
patch_wasm_file

# D. Install the new binary
install_binary "$DOWNLOAD_URL"

echo -e "\n\033[1;32m[SUCCESS]\033[0m Next.js setup for Termux complete!"
```
3. Make it Executable
Run this command to give the script permission to run:
```
chmod +x install-next-swc.sh
```
3. Run the Installer
Execute the script:
```
./install-next-swc.sh
```
