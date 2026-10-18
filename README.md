# Builds an installable APK of the Savings Checker Companion on GitHub's servers.
# Run it from the Actions tab: Build APK > Run workflow. It also runs whenever you upload changes.
name: Build APK

on:
  workflow_dispatch:
  push:
    branches: [main, master]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Get the project
        uses: actions/checkout@v4

      - name: Set up Java 17
        uses: actions/setup-java@v4
        with:
          distribution: temurin
          java-version: '17'

      - name: Set up the Android SDK
        uses: android-actions/setup-android@v3

      - name: Install the Android platform and build tools
        run: sdkmanager "platforms;android-35" "build-tools;34.0.0"

      # Uploading through a web page can drop the "runnable" flag from this file.
      - name: Make the Gradle wrapper runnable
        run: chmod +x ./gradlew

      - name: Build the debug APK
        run: ./gradlew --no-daemon assembleDebug

      - name: Keep the APK
        uses: actions/upload-artifact@v4
        with:
          name: SavingsCheckerCompanion-apk
          path: app/build/outputs/apk/debug/app-debug.apk
          if-no-files-found: error
