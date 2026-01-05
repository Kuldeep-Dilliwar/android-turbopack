# android-turbopack
This repo contains info about the installation of unofficial Next.js SWC binaries for Android ARM64 (only work with npm).

## Steps
1. Pre
```
pkg update && yes | pkg install unzip wget nodejs
```
```
npx create-next-app@v16.1.1-canary.11 my-app --yes
cd my-app
```
2. Download the zip
```
wget https://github.com/Kuldeep-Dilliwar/next.js/releases/download/v16.1.1-canary.11-android/next-swc-binaries-aarch64-linux-android.zip
```
3. Unzip the downloaded file
```
unzip next-swc-binaries-aarch64-linux-android
```
4. Remove the zip file
```
rm next-swc-binaries-aarch64-linux-android.zip
```
5. Renaming wasm.js to wasm1.js so it fallback to native bin
```
mv ./node_modules/next/wasm/@next/swc-wasm-nodejs/wasm.js ./node_modules/next/wasm/@next/swc-wasm-nodejs/wasm1.js
```
6. Moving our bin to right fallbacked dir
```
mv next-swc.android-arm64.node ./node_modules/next/next-swc-fallback/@next/swc-android-arm64/
```
7. Enjoy
```
npm run dev
```

### or you can download specific canary version from this url https://github.com/Kuldeep-Dilliwar/next.js/releases (this is my personal build, includes version v16.1.1-canary.11 and ahead).
