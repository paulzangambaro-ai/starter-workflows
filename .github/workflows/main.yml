name: Build Zangambaro Education APK

on:
  workflow_dispatch:
  push:
    branches:
      - main
      - master

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Set up Java
        uses: actions/setup-java@v4
        with:
          distribution: temurin
          java-version: '17'

      - name: Set up Android
        uses: android-actions/setup-android@v3

      - name: Find Android project
        run: |
          echo "=== PROJECT FILES ==="
          find . -maxdepth 4 -type f \( -name "settings.gradle" -o -name "settings.gradle.kts" -o -name "build.gradle" -o -name "build.gradle.kts" \) -print

      - name: Find Gradle project and build
        run: |
          PROJECT=$(find . -type f \( -name "settings.gradle" -o -name "settings.gradle.kts" \) -print -quit | xargs -r dirname)

          if [ -z "$PROJECT" ]; then
            echo "ERROR: No Gradle settings file was found."
            echo "Repository contents:"
            find . -maxdepth 3 -type f | sort
            exit 1
          fi

          echo "Gradle project found at: $PROJECT"
          cd "$PROJECT"

          if [ -f "./gradlew" ]; then
            chmod +x ./gradlew
            ./gradlew --no-daemon assembleDebug
          else
            gradle --no-daemon assembleDebug
          fi

      - name: Find APK
        run: |
          echo "=== APK FILES ==="
          find . -type f -name "*.apk" -print

      - name: Upload APK
        uses: actions/upload-artifact@v4
        with:
          name: Zangambaro-Education-APK
          path: |
            **/build/outputs/apk/debug/*.apk
          if-no-files-found: error
