---

# 🚀 Guide: Running Next.js with Turbopack on Termux

This guide outlines how to set up Next.js (Canary) on an Android device using Termux, specifically patching the SWC binary to ensure Turbopack runs correctly on the Android architecture.

### 📋 Prerequisites

Before starting, ensure your Termux environment is up to date and has the necessary tools.

#### 1. System Update

First, update your package lists and upgrade existing packages to avoid conflicts.

```bash
pkg update -y && pkg upgrade -y

```

#### 2. Install CA Certificates (Crucial)

The custom binary likely has a hardcoded path for SSL certificates (`/data/data/com.termux/files/usr/etc/tls/cert.pem`). Installing this package ensures the path exists and avoids SSL errors.

```bash
pkg install ca-certificates

```

#### 3. Install Dependencies

Install Node.js and utilities for downloading and editing files.

```bash
pkg install unzip wget nodejs vim

```

---

### 🛠️ Installation & Patching

We will create a specific version of a Next.js app and inject the compatible Android binary.

#### 4. Create the Project

Run the creation command. We use a specific Canary version (`v16.2.0-canary.4`) to match the custom binary we are about to download.

```bash
# Create the app (accepting defaults)
npx create-next-app@v16.2.0-canary.4 my-app --yes

# Enter the directory
cd my-app

```

#### 5. Download & Apply the Custom Binary Patch

Instead of running a long one-liner, you can run these commands step-by-step to ensure everything works correctly.

**A. Download the custom SWC binary:**

```bash
wget https://github.com/Kuldeep-Dilliwar/next.js/releases/download/v16.2.0-canary.4-android/next-swc-binaries-aarch64-linux-android.zip
unzip next-swc-binaries-aarch64-linux-android.zip
rm next-swc-binaries-aarch64-linux-android.zip

```

**B. Disable/Rename the existing WASM fallback:**
This step prevents Next.js from defaulting to the slower WASM build or conflicting with our new binary.

```bash
mv ./node_modules/next/wasm/@next/swc-wasm-nodejs/wasm.js ./node_modules/next/wasm/@next/swc-wasm-nodejs/wasm1.js

```

**C. Install the Android Binary:**
Create the correct directory structure and move the `.node` binary into place.

```bash
mkdir -p ./node_modules/next/next-swc-fallback/@next/swc-android-arm64/
mv next-swc.android-arm64.node ./node_modules/next/next-swc-fallback/@next/swc-android-arm64/

```

#### 6. Run the Server

You are now ready to start the development server.

```bash
npm run dev

```

---

### 🏢 Optional: Corporate Network Setup

If you are behind a corporate firewall or proxy that intercepts SSL (Zscaler, etc.), you may need to explicitly point to your company's certificate.

```bash
export TURBO_SSL_CERT_FILE=/path/to/your/corp-cert.pem
npm run dev

```

---

### 🔗 Credits & Resources

* **Source Repository:** [Kuldeep-Dilliwar/next.js (branch: personal-build)](https://github.com/Kuldeep-Dilliwar/next.js/tree/personal-build)
* **Releases:** [GitHub Releases](https://github.com/Kuldeep-Dilliwar/next.js/releases)
* **CI/CD Pipeline:** [GitHub Actions](https://github.com/Kuldeep-Dilliwar/next.js/actions?query=branch%3Apersonal-build)

> **Note:** This solution relies on a specific canary version and a custom build. If you update Next.js in the future, you may need to check the source repository for a matching updated binary.
