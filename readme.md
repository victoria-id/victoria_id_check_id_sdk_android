# Victoria ID - ID check - SDK - Documentation - Android

## Table of contents

1. [Victoria ID - ID check - SDK - Documentation - Android](#victoria-id---id-check---sdk---documentation---android)
   1. [Table of contents](#table-of-contents)
   2. [Introduction](#introduction)
   3. [Permissions](#permissions)
   4. [Installation](#installation)
      1. [Adding the SDK to your project](#adding-the-sdk-to-your-project)
   5. [Usage](#usage)
      1. [Importing the SDK](#importing-the-sdk)
      2. [Starting the SDK](#starting-the-sdk)


## Introduction

This SDK contains the ID check from Victoria ID. It can be embedded in your application, which we will refer to as the "host application".
The SDK is designed to launch an activity that walks the user through multiple steps of the ID check. After completion, the SDK will return metadata to the host application, indicating the success or failure of the operation.


## Permissions

Permissions required by the SDK:

```xml

<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.CAMERA" />
<uses-permission android:name="android.permission.NFC" />
<uses-permission android:name="android.permission.VIBRATE" />

```


## Installation

### Adding the SDK to your project

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

To start the SDK activity from the host application (your application), use the code below.
For your convenience, we merged the code and this part of the documentation together so your codebase can benefit from code comments.

```kotlin

// Create a new intent to launch the ID check SDK.
val iID_Check_SDK = Intent(applicationContext, Victoria_ID_Check_ID_SDK_Activity::class.java)


// Set the initial theme colors of the UI.
// These colors should match the colors as they are set in the Portal settings of the screening portal.
// When the SDK reaches step 3 of the user flow, it has made contact with the portal and fetched updated colors.
iID_Check_SDK.putExtra("color_primary", "#f108a7")
iID_Check_SDK.putExtra("color_secondary", "#dfbdfe")
iID_Check_SDK.putExtra("color_tertiary", "#13f3cb")

iID_Check_SDK.putExtra("color_font", "#0b0c5d")
iID_Check_SDK.putExtra("color_background", "#eedad6")


/*
 Provide the API URL (with token). If omitted, the SDK will display the QR code scanner step,
  requiring the user to scan the QR code from the portal on a desktop computer.

 Your API is expected to call `GET screenee/:screenee_id/check/identity/travel_document/text_chip_certificate/token/`
  as described in the Victoria Connect API documentation at https://doc.api.victoria-id.com/#1f481ddb-3547-4c17-8ec4-e47dfd47fb71
  to get the temporary token required for the API to start and finish the process.
*/
iID_Check_SDK.putExtra("api_uri", "https://api.victoria-id.com/screenee/:screenee_id/check/identity/travel_document/text_chip_certificate/?domain=example.victoria-id.com&token=<token>")


// Prepare a result launcher to process the result of the ID check SDK.
val resultLauncher = registerForActivityResult(ActivityResultContracts.StartActivityForResult()) {
    result ->
        if(result.resultCode == Activity.RESULT_OK) {

            // The result code of the ID check will be stored in `ID_Check_Result.code`.
            val strID_Check_Result_Code = ID_Check_Result.code

            // The result code can be..
            when (strID_Check_Result_Code) {

                "feature_not_found.camera" -> {
                    // The device does not have a camera needed to scan a QR code and/or ID document.
                }

                "feature_not_found.nfc" -> {
                    // The device does not have NFC capability.
                }

                "exception.api.url" -> {
                    // The Victoria Connect API did not accept the API URL to be able to start the process.
                }

                "exception.api.data" -> {
                    // The Victoria Connect API did not accept the data payload to finish the process.
                }

                "exception.generic" -> {
                    // Generic exception.
                    val strException_Description = ID_Check_Result.data
                }

                "success" -> {
                    // ID check was completed successfully.
                    val strID_Data_JSON = ID_Check_Result.data
                }
            }
        }
}


// Launch the main ID check SDK activity.
resultLauncher.launch(iID_Check_SDK)

```
