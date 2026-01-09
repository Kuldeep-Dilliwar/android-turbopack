# How to run Next.js with turbopack in Termux?
## Here is how to:
```
yes | pkg update && yes | pkg install unzip wget nodejs vim && clear && \
yes | npx create-next-app@v16.1.1-canary.19 my-app --yes && clear && \
cd my-app && clear && \
wget https://github.com/Kuldeep-Dilliwar/next.js/releases/download/v16.1.1-canary.19-android/next-swc-binaries-aarch64-linux-android.zip && clear && \
unzip next-swc-binaries-aarch64-linux-android.zip && clear && \
rm next-swc-binaries-aarch64-linux-android.zip && clear && \
mv ./node_modules/next/wasm/@next/swc-wasm-nodejs/wasm.js ./node_modules/next/wasm/@next/swc-wasm-nodejs/wasm1.js && clear && \
mkdir -p ./node_modules/next/next-swc-fallback/@next/swc-android-arm64/ && clear && \
mv next-swc.android-arm64.node ./node_modules/next/next-swc-fallback/@next/swc-android-arm64/ && clear && \
npm run dev
```
