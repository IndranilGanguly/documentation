# REACT NATIVE DOCS

## Setting up developement environment

Refer to the following Docs

[React Native Docs](https://reactnative.dev/docs/0.71/getting-started)

## Using React Native Vector Icons for Metro CLI based Projects

Refer to the following Repository

[React Native Vector Icons](https://github.com/oblador/react-native-vector-icons)

## Using React Native SVG Icons for Metro CLI based Projects

Refer to the following Repository

[React Native Vector Images](https://github.com/oblador/react-native-vector-image)

## Generate .aab (Android App Bundle) for Google Play Store Deployment

Refer to following Docs

[React Native Play Store Publish](https://reactnative.dev/docs/0.71/signed-apk-android)

## Generate .apk file for debug level

Open terminal from VS Code or Open command Prompt and Navigate to your android folder of your React Native Project Structure and run the following command

```bash
gradlew clean
```

After the command ran successfully, it will show **BUILD SUCCESSFUL**
Then run command

```bash
gradlew assembleRelease
```

On successful **BUILD** the apk willbe generated in the following folder as **app-release.apk**

```tree
├── project-name 
│   ├── android
│   │   ├──app
|   |   |   ├──build
|   |   |   |   ├──outputs
|   |   |   |   ├   |──apk
|   |   |   |   ├   |   |─release
|   |   |   |   ├   |   |   |─app-release.apk
```
