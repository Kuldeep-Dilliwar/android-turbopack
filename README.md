# android-turbopack
This repo contains info about the installation of unofficial Next.js SWC binaries for Android ARM64 (only work with npm and termux).

## Steps
```
pkg update && yes | pkg install unzip wget nodejs
npx create-next-app@v16.1.1-canary.11 my-app --yes
cd my-app
wget https://github.com/Kuldeep-Dilliwar/next.js/releases/download/v16.1.1-canary.11-android/next-swc-binaries-aarch64-linux-android.zip
unzip next-swc-binaries-aarch64-linux-android
rm next-swc-binaries-aarch64-linux-android.zip
mv ./node_modules/next/wasm/@next/swc-wasm-nodejs/wasm.js ./node_modules/next/wasm/@next/swc-wasm-nodejs/wasm1.js
mkdir -p ./node_modules/next/next-swc-fallback/@next/swc-android-arm64/
mv next-swc.android-arm64.node ./node_modules/next/next-swc-fallback/@next/swc-android-arm64/
npm run dev
```

### or you can download specific canary version from this url https://github.com/Kuldeep-Dilliwar/next.js/releases (this is my personal build, includes version v16.1.1-canary.11 and ahead).
