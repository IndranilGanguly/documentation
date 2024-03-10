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

## Generate .aab (Android App Bundle) for Google Play Store Deployment or .apk for testing

Refer to following Docs

[React Native Play Store Publish](https://reactnative.dev/docs/0.71/signed-apk-android)

Generate a **.keystore** file for signing the app


Open Command Prompt as Administrator in Windows and navigate to your java installation **bin** folder

```C:\Program Files\Java\jdkx.x.x_x\bin```

and run the command

```bash
keytool -genkeypair -v -storetype PKCS12 -keystore "my-upload-key.keystore" -alias my-key-alias -keyalg RSA -keysize 2048 -validity 10000
```

This will save the keystore as ```my-upload-key.keystore``` in the bin folder

Copy the keystore file to your React Native ```android/app``` directory

Edit the ```android/gradle.properties``` and add the following

```properties
MYAPP_UPLOAD_STORE_FILE=my-upload-key.keystore
MYAPP_UPLOAD_KEY_ALIAS=my-key-alias
MYAPP_UPLOAD_STORE_PASSWORD=*****
MYAPP_UPLOAD_KEY_PASSWORD=*****
```

Replace the ```*****``` with the keystore password,alias and key password used during generating the keystore


Edit the file ```android/app/build.gradle``` and add the following in ```signingConfigs``` block and ```release``` block

<pre>
android {
    ...
    defaultConfig { ... }
    signingConfigs {
        release {
            <b>`if (project.hasProperty('MYAPP_UPLOAD_STORE_FILE')) {
                storeFile file(MYAPP_UPLOAD_STORE_FILE)
                storePassword MYAPP_UPLOAD_STORE_PASSWORD
                keyAlias MYAPP_UPLOAD_KEY_ALIAS
                keyPassword MYAPP_UPLOAD_KEY_PASSWORD
            }</b>
        }
    }
    buildTypes {
        release {
            ...
            <b>signingConfig signingConfigs.release</b>
        }
    }
}
</pre>


### A. To generate .aab file

Run the following command

```bash
npx react-native build-android --mode=release
```

After successful build, the generated .aab file will be generated as **app-release.aab** in the following folder

```tree
├── project-name 
│   ├── android
│   │   ├──app
|   |   |   ├──build
|   |   |   |   ├──outputs
|   |   |   |   ├   |──bundle
|   |   |   |   ├   |   |─release
|   |   |   |   ├   |   |   |─app-release.aab
```


### B. To generate .apk file

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
