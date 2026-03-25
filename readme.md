# Victoria-ID - ID check - SDK - Documentation - Android

## Table of contents

1. [Victoria-ID - ID check - SDK - Documentation - Android](#victoria-id---id-check---sdk---documentation---android)
   1. [Table of contents](#table-of-contents)
   2. [Introduction](#introduction)
   3. [Adding the SDK to your project](#adding-the-sdk-to-your-project)
      1. [Gradle](#gradle)
   4. [Usage](#usage)
      1. [Importing the SDK](#importing-the-sdk)
      2. [Starting the SDK](#starting-the-sdk)


## Introduction

This SDK contains the ID check from Victoria-ID. It can be embedded in your application, which we will refer to as the "host application".
The SDK is designed to launch an activity that walks the user through multiple steps of the ID check. After completion, the SDK will return metadata to the host application, indicating the success or failure of the operation.


## Adding the SDK to your project

### 1. Add the Maven repository

Add the Victoria-ID Maven repository to your project. You can do this either in your root `build.gradle`:

```gradle
allprojects {
    repositories {
        google()
        mavenCentral()
        maven { url 'https://raw.githubusercontent.com/victoria-id/victoria_id_check_id_sdk_android/master/maven/' }
    }
}
```

Or, if your project uses `dependencyResolutionManagement`, add it to your `settings.gradle` instead:

```gradle
dependencyResolutionManagement {
    repositories {
        google()
        mavenCentral()
        maven { url 'https://raw.githubusercontent.com/victoria-id/victoria_id_check_id_sdk_android/master/maven/' }
    }
}
```

### 2. Add the dependency

In your app module's `build.gradle`, add the SDK dependency:

```gradle
dependencies {
    implementation 'com.victoria-id:victoria-id-check-id-sdk-android:1.14.0'
}
```

### 3. Build your project

Sync and build your project to download and install the SDK.


## Usage

### Importing the SDK

First import the Activity using:

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
iID_Check_SDK.putExtra("color_primary", "#f108a7")     // The primary color used for call-to-action elements.
iID_Check_SDK.putExtra("color_secondary", "#dfbdfe")   // Reserved.
iID_Check_SDK.putExtra("color_tertiary", "#13f3cb")    // Reserved.

iID_Check_SDK.putExtra("font_color", "#0b0c5d")        // Reserved.
iID_Check_SDK.putExtra("background_color", "#eedad6")  // Reserved.


/*
 Provide the API URL (with token).

 Your API is expected to call `GET screenee/:screenee_id/check/identity/travel_document/text_chip_certificate/token/`
  as described in the Victoria Connect API documentation at https://doc.api.victoria-id.com/#1f481ddb-3547-4c17-8ec4-e47dfd47fb71
  to get the temporary token required for the API to start the process.
*/
iID_Check_SDK.putExtra("api_uri", "https://api.victoria-id.com/screenee/:screenee_id/check/identity/travel_document/text_chip_certificate/?domain=example.victoria-id.com&token=<token>")


// Prepare a result launcher to process the result of the ID check SDK.
val resultLauncher = registerForActivityResult(ActivityResultContracts.StartActivityForResult()) {
    result ->
        if(result.resultCode == Activity.RESULT_OK) {

            // The result code can be..
            when (ID_Check_Result.code) {

                ID_Check_Result_Code.feature_not_found_camera -> {
                    Log.w("ID check", "The device does not have a camera needed to scan a QR code and/or ID document.")
                }

                ID_Check_Result_Code.feature_not_found_nfc -> {
                    Log.w("ID check", "The device does not have NFC capability.")
                }

                ID_Check_Result_Code.exception_api_url -> {
                    Log.w("ID check", "The Victoria Connect API did not accept the API URL to be able to start the process.")
                }

                ID_Check_Result_Code.exception_api_data -> {
                    Log.w("ID check", "The Victoria Connect API did not accept the data payload to finish the process.")
                }

                ID_Check_Result_Code.exception_generic -> {
                    Log.w("ID check", "Generic exception:")

                    val strException_Description = ID_Check_Result.data
                    Log.w("ID check", strException_Description)
                }

                ID_Check_Result_Code.cancel -> {
                    Log.w("ID check", "User canceled ID check flow.")
                }

                ID_Check_Result_Code.data_share_decline -> {
                    Log.w("ID check", "User declined to share data.")
                }

                ID_Check_Result_Code.success -> {
                    Log.w("ID check", "ID check was completed successfully.")

                    /*
                     Your API is expected to fetch the information directly from the Victoria Connect API using:
                      `GET group/:group_id/screening/:screening_id/screenee/:screenee_id/`.
                    */
                }
            }
        }
}


// Launch the main ID check SDK activity.
resultLauncher.launch(iID_Check_SDK)

```
