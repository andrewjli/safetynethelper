SafetyNet `attest()` Helper
================

SafetyNet Helper wraps the Google Play Services SafetyNet.API and verifies Safety Net API response with the [Android Device Verification API](https://developer.android.com/google/play/safetynet/start.html#verify-compat-check). The SafetyNet.API analyses the device your app is running on to test its software/hardware configuration matches that of a device that has passed the Android Compatibility Test Suite (CTS). Note this is a client only validation, it's recommended to include [server side validation]().

*Rooted* devices seem to cause `ctsProfileMatch=false`.

**Recommend reading the developers guide to getting started with [SafetyNet](https://developer.android.com/google/play/safetynet/start.html)**


![](./sample/src/main/res/mipmap-xxhdpi/ic_launcher.png)


Extract from Android [SafetyNet API doc](https://developer.android.com/google/play/safetynet/index.html)

*Check if your app is running on a device that matches a device model that has passed Android compatibility testing. This analysis can help you determine if your app will work as expected on the device where it is installed. The service evaluates both software and hardware characteristics of the device, and may use hardware roots of trust, when available.*


##Features

* Calls Google play services Safety Net test
* Local verification of request
* Verifies Safety Net API response with the Android Device Verification API (over SSL pinned connection)


## Requires / Dependencies

* Google Play services 7+ (specifically the SafetyNet API 'com.google.android.gms:play-services-safetynet:7.+')
* Requires Internet permission
* Google API key for the [Android Device Verification API](https://developer.android.com/training/safetynet/index.html#verify-compat-check)

##Server Validation
This library is to get you going with SafetyNet attest API. For more secure and robust validation you _need_ to include a server-side component for the validation.

* Server creates the initial nonce / request Token
* Pass the response from SafetyNet API to your server for validation (the same validation check which this library completes)
	* Check the nonce is the same
	* verify the SafetyNet response is from Google using the Android Device Verification API
	* verify app package, timestamp, apk and certificate digests

With skill and time any device based checks can be bypassed. This is why the validation should be handled by the server. This way at least your server would know if the device (and app installation) was compromised and take appropriate action e.g revoking tokens.


## How to use

You'll need to get a **API key** from the Google developer console to allow you to verify with the Android Device Verification API (in the sample project this is set via a BuildConfig field to keep my api key out of GitHub)

```java

    final SafetyNetHelper safetyNetHelper = new SafetyNetHelper(API_KEY);

    safetyNetHelper.requestTest(context, new SafetyNetHelper.SafetyNetWrapperCallback() {
            @Override
            public void error(int errorCode, String msg) {
                //handle and retry depending on errorCode
                Log.e(TAG, msg);
            }

            @Override
            public void success(boolean ctsProfileMatch) {
                if (ctsProfileMatch) {
                    //handle pass
                } else {
                    //handle fail, maybe warn user device is unsupported?
                }
            }
        });
```

### Add as dependency

This library is not _yet_ released in Maven Central, until then you can add as a library module or use [JitPack.io](https://jitpack.io/#scottyab/safetynethelper)


add remote maven url

```gradle

    repositories {
        maven {
            url "https://jitpack.io"
        }
    }
```

then add a library dependency

```gradle

    dependencies {
        compile 'com.github.scottyab:safetynethelper:0.2.0'
    }
```


## Sample App

The sample app illustrates the helper library in practice. Test your own devices today. It's available on the [playstore](https://play.google.com/store/apps/details?id=com.scottyab.safetynet.sample).

<img width="270" src="./art/sample_req_pass_cts_pass.png">
<br>
<img width="270" src="./art/sample_req_pass_cts_fail.png">
<img width="270" src="./art/sample_req_pass_validation_fail.png">


##Licence

	Copyright (c) 2015 Scott Alexander-Bown

    Licensed under the Apache License, Version 2.0 (the "License");
    you may not use this file except in compliance with the License.
    You may obtain a copy of the License at
