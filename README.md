name: Build Zangambaro Education APK

on:
  workflow_dispatch:
  push:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-24.04

    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Show project structure
        run: |
          echo "Current directory:"
          pwd
          echo "Files:"
          find . -maxdepth 4 -type f | sort

      - name: Set up Java 17
        uses: actions/setup-java@v5
        with:
          distribution: temurin
          java-version: '17'

      - name: Set up Android SDK
        uses: android-actions/setup-android@v3

      - name: Locate Gradle project
        id: locate
        run: |
          PROJECT=$(find . -type f \
            \( -name "settings.gradle" -o -name "settings.gradle.kts" \) \
            -print -quit | xargs -r dirname)

          if [ -z "$PROJECT" ]; then
            echo "NO GRADLE PROJECT FOUND"
            exit 1
          fi

          echo "Found Gradle project: $PROJECT"
          echo "project=$PROJECT" >> "$GITHUB_OUTPUT"

      - name: Build APK
        working-directory: ${{ steps.locate.outputs.project }}
        run: |
          if [ -f "./gradlew" ]; then
            chmod +x ./gradlew
            ./gradlew --no-daemon assembleDebug
          else
            gradle --no-daemon assembleDebug
          fi

      - name: Find APK
        run: |
          find . -type f -name "*.apk" -print

      - name: Upload APK
        uses: actions/upload-artifact@v4
        with:
          name: Zangambaro-Education-APK
          path: "**/build/outputs/apk/debug/*.apk"
          if-no-files-found: error
