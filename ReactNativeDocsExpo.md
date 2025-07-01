# REACT NATIVE DOCS FOR EXPO PROJECTS

## Generate a **.keystore** file for signing the app (Debug mode/Test Mode) ##


Open Command Prompt as Administrator in Windows and navigate to your java installation **bin** folder

```C:\Program Files\Java\jdkx.x.x_x\bin```

and run the command

```bash
keytool -genkeypair -v -storetype PKCS12 -keystore "my-upload-key.keystore" -alias my-key-alias -keyalg RSA -keysize 2048 -validity 10000
```

This will save the keystore as ```my-upload-key.keystore``` in the bin folder

Copy the keystore file to your React Native ```android/app``` directory

Open build.gradle file (app level)
```android/app/build.gradle```

Ensure the following is present in **android** block
<pre>

android {

    ...
    signingConfigs {
        debug {
            storeFile file('debug.keystore')
            storePassword 'storepass'
            keyAlias 'my-key-alias'
            keyPassword 'keypassword'
        }
    }
    ...
}
</pre>

# Generate release apk file for testing mode #

## Pre Requisites depending on Libraties used ##

If SQLITE database is used ***expo-sqlite*** 
install the following package

```npm
npm i wa-sqlite
```

then in **metro.config.js** include the wasm for sqlite

```js

const { getDefaultConfig } = require('expo/metro-config')

const config = getDefaultConfig(__dirname, {
    // [Web-only]: Enables CSS support in Metro.
    isCSSEnabled: true,
})

config.resolver.assetExts.push('wasm');  // add this line to include the wasm for sqlite

module.exports = config
```

### Step 1: Go to app.json file and under expo object write the following lines ###

```json
{
  "expo": {
    
    "newArchEnabled": true,
    "updates": {
      "enabled": true,
      "checkAutomatically": "ON_LOAD",
      "fallbackToCacheTimeout": 0
    }        
  }
}
```

### Step 2: Create a JS bundle folder before creating the bare workflow so to bundle all the dependencies and files ###

Run the following command from the root folder of the react native app

```cmd
npx expo export --dev --output-dir ./dist
```

It will create the JS bundle and will be stored in **dist** folder in the Root Directory

### Step 3: Create the android native folder(bare workflow) by running the following ###

```cmd
expo prebuild --clean
```

### Step 4: Make the following changes inside the native android files ###

Inside app level gradle file _android/app/build.gradle_

Inside the **android** block make the following changes

<pre>
android {
    ...
    
    defaultConfig {
        applicationId 'com.cdackolkata.ekap'
        minSdkVersion rootProject.ext.minSdkVersion
        targetSdkVersion rootProject.ext.targetSdkVersion
        versionCode 1
        versionName "1.0.0"

	    //add this line//
        buildConfigField("boolean", "IS_NEW_ARCHITECTURE_ENABLED", "false")
    }
    
    // add this block
    project.ext.react = [
        entryFile: "index.tsx", // or "index.tsx" or your main entry file
        enableHermes: false,   // enable Hermes engine or false to use JSC
        bundleInRelease: true // this enables JS bundling on release builds
    ]

    buildTypes {
            debug {
                signingConfig signingConfigs.debug
            }
            release {
                // Caution! In production, you need to generate your own keystore file.
                // see https://reactnative.dev/docs/signed-apk-android.
                signingConfig signingConfigs.release   // change it from debug to release
                shrinkResources (findProperty('android.enableShrinkResourcesInReleaseBuilds')?.toBoolean() ?: false)
                minifyEnabled enableProguardInReleaseBuilds
                proguardFiles getDefaultProguardFile("proguard-android.txt"), "proguard-rules.pro"
                crunchPngs (findProperty('android.enablePngCrunchInReleaseBuilds')?.toBoolean() ?: true)
            }
        }

    ...
}
</pre>

In MainApplication.kt in the following directory _android/app/src/main/java/<yourpackage>/MainApplication.kt

```kt

override fun onCreate() {
    super.onCreate()
    // @generated begin xml-fonts-init - expo prebuild (DO NOT MODIFY) sync-da39a3ee5e6b4b0d3255bfef95601890afd80709

    // @generated end xml-fonts-init
    SoLoader.init(this, OpenSourceMergedSoMapping)
    if (BuildConfig.IS_NEW_ARCHITECTURE_ENABLED) {
    
      // If you opted-in for the New Architecture, we load the native entry point for this app.
      
      //comment this load() function
      //load()
      
      
	  // add this new load() function
      load(bridgelessEnabled=false)

	  
    }
    ApplicationLifecycleDispatcher.onApplicationCreate(this)
  }
```

In AndroidManifest.xml in the following directory _android/app/src/main/AndroidManifest.xml_

Inside **application** tag mention this property only for testing mode

```xml
<application android:usesCleartextTraffic="true">
    ... other configs
</application>
```

### Step 5: Run the following command one by one ###

```cmd
gradlew clean
```

```cmd
gradlew assembleRelease // for generating apk
```

the release apk _app-release.apk_ will be generated in the following folder

_android/app/build/outputs/apk/release_

```cmd
gradlew bundleRelease // for generating aab bundle
```

the release bundle _app-release.aab_ will be generated in the following folder

_android/app/build/outputs/bundle/release_

