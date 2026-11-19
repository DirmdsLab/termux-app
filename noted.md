# Termux Android Build Environment


## 1. Start the Ubuntu Container

```bash
podman run -it \
  --name termux-build \
  -v "$(pwd):/workspace" \
  -w /workspace \
  ubuntu:24.04 \
  bash
```

## 2. Install Build Dependencies

Update the package list and install the required tools.

```bash
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
```

Verify the Java installation:

```bash
java -version
javac -version
```

## 3. Install Android Command-Line Tools

Create the Android SDK directories and download the command-line tools.

```bash
mkdir -p /opt/android-sdk/cmdline-tools /tmp/android-cli

curl -fL \
  https://dl.google.com/android/repository/commandlinetools-linux-15859902_latest.zip \
  -o /tmp/android-tools.zip
```

Verify the downloaded archive:

```bash
echo "4e4c464f145a7512b57d088ac6c278c03c9eea610886b35a5e0804e74eedf583  /tmp/android-tools.zip" | sha256sum -c -
```

Extract and configure the Android command-line tools:

```bash
unzip -q /tmp/android-tools.zip -d /tmp/android-cli

mv /tmp/android-cli/cmdline-tools /opt/android-sdk/cmdline-tools/latest
```

Configure the Android SDK environment:

```bash
export ANDROID_HOME=/opt/android-sdk
export ANDROID_SDK_ROOT=/opt/android-sdk
export PATH="$ANDROID_HOME/cmdline-tools/latest/bin:$ANDROID_HOME/platform-tools:$PATH"
```

Verify the installation:

```bash
sdkmanager --version
```

## 4. Install Android SDK Packages

Accept the Android SDK licenses:

```bash
yes | sdkmanager --sdk_root="$ANDROID_HOME" --licenses
```

Install the required SDK packages:

```bash
sdkmanager --sdk_root="$ANDROID_HOME" --install \
  "platform-tools" \
  "platforms;android-36" \
  "build-tools;36.0.0" \
  "ndk;29.0.14206865"
```

## 5. Configure Build Environment

Set the Android NDK and Termux package variant:

```bash
export ANDROID_HOME=/opt/android-sdk
export ANDROID_SDK_ROOT=/opt/android-sdk
export ANDROID_NDK_HOME=/opt/android-sdk/ndk/29.0.14206865
export TERMUX_PACKAGE_VARIANT=apt-android-7
export PATH="$ANDROID_HOME/cmdline-tools/latest/bin:$ANDROID_HOME/platform-tools:$PATH"
```

Verify the SDK and NDK directories:

```bash
ls -ld \
  "$ANDROID_HOME/platforms/android-36" \
  "$ANDROID_HOME/build-tools/36.0.0" \
  "$ANDROID_NDK_HOME"
```

## 6. Build Termux APK

Make the Gradle wrapper executable:

```bash
chmod +x gradlew
```

Build the debug APK:

```bash
./gradlew --no-daemon assembleDebug
```

## 7. Locate Generated APK

Find the generated APK files:

```bash
find app/build/outputs/apk -type f -name "*.apk" -exec ls -lh {} \;
```

## 8. Clean Build (Optional)

To clean previous build artifacts and rebuild the debug APK:

```bash
./gradlew --no-daemon clean assembleDebug
```

**Note:** Run all commands inside the same Podman container session to preserve the configured environment variables.