# SDK Documentation

## Introduction
This SDK is designed to launch an activity that walks the user through multiple ID-check steps. After completion, the SDK will return meta-data to the calling application, indicating the success or failure of the operation. No sensitive information will be returned directly by the SDK.

## Permissions
Permissions required by the SDK:
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
        val sdkIntent = Intent(applicationContext, Victoria_ID_Check_ID_SDK_Activity::class.java)
        sdkIntent.putExtra("color_primary", "#f108a7")
        sdkIntent.putExtra("color_secondary", "#dfbdfe")
        sdkIntent.putExtra("color_tertiary", "#13f3cb")
        sdkIntent.putExtra("color_font", "#0b0c5d")
        sdkIntent.putExtra("color_background", "#eedad6")
        sdkIntent.putExtra("api_uri", "https://api.victoria-id.com/screenee/:screenee_id/check/identity/travel_document/text_chip_certificate/?domain=example.victoria-id.com&token=<token>")

        val resultLauncher = registerForActivityResult(ActivityResultContracts.StartActivityForResult()) {
            result ->
                if(result.resultCode == Activity.RESULT_OK) {
                    val code = API.check.result.code
                    val data = API.check.result.data
                }
        }

        resultLauncher.launch(sdkIntent)
```
- `color_<variant>`: Set the variant color of the SDK’s UI.
- `api_uri`: Provide the API URL (with token) for data retrieval. If omitted, the SDK will display the QR-code scanner step, which can still be themed using the passed color values.

SDK flow statuses (API.check.result.code):
- "success"
- "exception"
- "api_error.data"
- "api_error.link_invalid"
- "api_error.exception"
- "feature_not_found.camera"
- "feature_not_found.nfc"

SDK flow result (API.check.result.data):
- json data returned from the api, if the call was successful,
- exception message, if there was an exception
