# SDK Documentation

## Introduction
This SDK is designed to launch an activity that walks the user through multiple ID-check steps. After completion, the SDK will return meta-data to the calling application, indicating the success or failure of the operation. No sensitive information will be returned directly by the SDK.

## Permissions
Permisions required by the SDK:
```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.CAMERA" />
<uses-permission android:name="android.permission.NFC" />
<uses-permission android:name="android.permission.VIBRATE" />
```

## Installation
### Add the SDK to your project
To integrate the SDK into your Android project, add the following dependency to your `build.gradle` file:

```gradle
dependencies {
    implementation 'import com.victoria_id.check.id.sdk:1.5.0'
}
```
Make sure to sync your project to download and install the SDK.

## Usage
### Import SDK
Firstly import the Activity using following code:
```kotlin
import com.victoria_id.check.id.sdk.Victoria_ID_Check_SDK_Activity
```
### Start SDK
To start the SDK activity from your application, use the following code:
```kotlin
        val sdkIntent = Intent(applicationContext, Victoria_ID_Check_SDK_Activity::class.java)
        sdkIntent.putExtra("primary_color", "#FF6200EE")
        sdkIntent.putExtra("secondary_color", "#FF03DAC5")
        sdkIntent.putExtra("api_uri", "https://example.api.victoria-id.com/screenee/<SCREENE_ID>/check/identity/travel_document/text_chip_certificate/?domain=example.victoria-id.com&token=<TOKEN>")

        val resultLauncher = registerForActivityResult(ActivityResultContracts.StartActivityForResult()) {
            result ->
                if(result.resultCode == Activity.RESULT_OK) {
                    val data = result.data
                    // Do something with data
                }
        }

        resultLauncher.launch(sdkIntent)
```
- `primary_color`: Set the primary color of the SDK’s UI.
- `secondary_color`: Set the secondary color for accents.
- `api_uri`: Provide the API URL (with token) for data retrieval. If omitted, the SDK will display the QR-code scanner step, which can still be themed using the passed color values.
