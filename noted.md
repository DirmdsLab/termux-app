podman run -it \
  --name termux-build \
  -v "$(pwd):/workspace" \
  -w /workspace \
  ubuntu:24.04 \
  bash


apt-get update && apt-get install -y \
    openjdk-17-jdk \
    curl \
    wget \
    unzip \
    zip \
    git \
    ca-certificates \
    python3 \
    file


java -version
javac -version


mkdir -p /opt/android-sdk/cmdline-tools /tmp/android-cli

curl -fL \
  https://dl.google.com/android/repository/commandlinetools-linux-15859902_latest.zip \
  -o /tmp/android-tools.zip

echo "4e4c464f145a7512b57d088ac6c278c03c9eea610886b35a5e0804e74eedf583  /tmp/android-tools.zip" | sha256sum -c -

unzip -q /tmp/android-tools.zip -d /tmp/android-cli

mv /tmp/android-cli/cmdline-tools /opt/android-sdk/cmdline-tools/latest

export ANDROID_HOME=/opt/android-sdk
export ANDROID_SDK_ROOT=/opt/android-sdk
export PATH="$ANDROID_HOME/cmdline-tools/latest/bin:$ANDROID_HOME/platform-tools:$PATH"

sdkmanager --version


export ANDROID_HOME=/opt/android-sdk
export ANDROID_SDK_ROOT=/opt/android-sdk
export PATH="$ANDROID_HOME/cmdline-tools/latest/bin:$ANDROID_HOME/platform-tools:$PATH"

yes | sdkmanager --sdk_root="$ANDROID_HOME" --licenses

sdkmanager --sdk_root="$ANDROID_HOME" --install \
    "platform-tools" \
    "platforms;android-36" \
    "build-tools;36.0.0" \
    "ndk;29.0.14206865"


export ANDROID_HOME=/opt/android-sdk
export ANDROID_SDK_ROOT=/opt/android-sdk
export ANDROID_NDK_HOME=/opt/android-sdk/ndk/29.0.14206865
export PATH="$ANDROID_HOME/cmdline-tools/latest/bin:$ANDROID_HOME/platform-tools:$PATH"


ls -ld \
    "$ANDROID_HOME/platforms/android-36" \
    "$ANDROID_HOME/build-tools/36.0.0" \
    "$ANDROID_NDK_HOME"


export ANDROID_HOME=/opt/android-sdk
export ANDROID_SDK_ROOT=/opt/android-sdk
export ANDROID_NDK_HOME=/opt/android-sdk/ndk/29.0.14206865
export TERMUX_PACKAGE_VARIANT=apt-android-7

chmod +x gradlew

./gradlew --no-daemon assembleDebug