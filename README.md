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
2. Make it Executable
Run this command to give the script permission to run:
```
chmod +x install-next-swc.sh
```
3. Run the Installer
Execute the script:
```
./install-next-swc.sh
```