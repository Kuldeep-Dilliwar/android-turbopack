# How to run Next.js with turbopack in Termux?
## Here is how to:
```
yes | pkg update
&&
yes | pkg install unzip wget nodejs vim
&&
yes | npx create-next-app@v16.1.1-canary.12 my-app --yes
&&
cd my-app
&&
wget https://github.com/Kuldeep-Dilliwar/next.js/releases/download/v16.1.1-canary.12-android/next-swc-binaries-aarch64-linux-android.zip
&&
unzip next-swc-binaries-aarch64-linux-android
&&
rm next-swc-binaries-aarch64-linux-android.zip
&&
mv ./node_modules/next/wasm/@next/swc-wasm-nodejs/wasm.js ./node_modules/next/wasm/@next/swc-wasm-nodejs/wasm1.js
&&
mkdir -p ./node_modules/next/next-swc-fallback/@next/swc-android-arm64/
&&
mv next-swc.android-arm64.node ./node_modules/next/next-swc-fallback/@next/swc-android-arm64/
&&
npm run dev
```
