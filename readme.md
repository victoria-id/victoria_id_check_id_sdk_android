# Victoria ID - ID check - SDK - Documentation - Android

## Table of contents

1. [Victoria ID - ID check - SDK - Documentation - Android](#victoria-id---id-check---sdk---documentation---android)
   1. [Table of contents](#table-of-contents)
   2. [Introduction](#introduction)
   3. [Permissions](#permissions)
   4. [Installation](#installation)
      1. [Add the SDK to your project](#add-the-sdk-to-your-project)
   5. [Usage](#usage)
      1. [Importing the SDK](#importing-the-sdk)
      2. [Starting the SDK](#starting-the-sdk)


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

Make sure to synchronize your project to download and install the SDK.


## Usage

### Importing the SDK

Firstly import the Activity using following code:

```kotlin
import com.victoria_id.check.id.sdk.Victoria_ID_Check_ID_SDK_Activity
```


### Starting the SDK

To start the SDK activity from the host application (your application), use the following code:

```kotlin
        // Create a new intent to launch the ID check SDK.
        val iID_Check_SDK = Intent(applicationContext, Victoria_ID_Check_ID_SDK_Activity::class.java)

        // These colors should match the colors as they are set in Portal settings of the screening portal.
        iID_Check_SDK.putExtra("color_primary", "#f108a7")
        iID_Check_SDK.putExtra("color_secondary", "#dfbdfe")
        iID_Check_SDK.putExtra("color_tertiary", "#13f3cb")

        iID_Check_SDK.putExtra("color_font", "#0b0c5d")
        iID_Check_SDK.putExtra("color_background", "#eedad6")

        /*
         Call `GET screenee/:screenee_id/check/identity/travel_document/text_chip_certificate/token/` from your API
          as described in the Victoria Connect API documentation at https://doc.api.victoria-id.com/#1f481ddb-3547-4c17-8ec4-e47dfd47fb71
          to get the temporary token required to start and finish the process by sending the ID data back to the Victoria Connect API.
        */
        iID_Check_SDK.putExtra("api_uri", "https://api.victoria-id.com/screenee/:screenee_id/check/identity/travel_document/text_chip_certificate/?domain=example.victoria-id.com&token=<token>")

        val resultLauncher = registerForActivityResult(ActivityResultContracts.StartActivityForResult()) {
            result ->
                if(result.resultCode == Activity.RESULT_OK) {
                    val strID_Check_Result_Code = ID_Check_Result.code
                    val strID_Data = ID_Check_Result.data
                }
        }

        resultLauncher.launch(iID_Check_SDK)
```


Intent values:

* `color_<variant>`: Controls the initial theme colors of the SDK’s UI. Will switch to using the colors as defined in the Portal settings of the screening portal when the SDK reaches step 3 of the user flow.

* `api_uri`: Provide the API URL (with token). If omitted, the SDK will display the QR code scanner step.


SDK return codes (`ID_Check_Result.code`):

* "feature_not_found.camera": The device does not have a camera needed to scan a QR code. Only applicable when `api_uri` is omitted.
* "feature_not_found.nfc": The device does not have NFC capability.

* "exception.generic": Generic exception.
* "exception.api.url.invalid": The Victoria Connect API did not accept the API URL to be able to start the process.
* "exception.api.data": The Victoria Connect API did not accept the data payload to finish the process.
* "exception.api": The Victoria Connect API returned an exception.

* "success": ID check was successfully performed.


SDK return data (`ID_Check_Result.data`):

* JSON data returned from the API, if the call was successful.
* Exception message, if there was an exception.
