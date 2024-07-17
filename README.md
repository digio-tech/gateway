# gateway
### What is gateway sdk
One sdk for launching all things @Digio. Import only the sdk you need. Giving you full control over what you app should import and work with. 

### How to integrate 
1. Add jitpack to your project 
```
allprojects {
    repositories {
        ...
        maven { url 'https://jitpack.io' }
    }
}

plugins {
   id 'com.android.application' version '7.4.2' apply false
   id 'com.android.library' version '7.4.2' apply false
   id 'org.jetbrains.kotlin.android' version '1.7.20' apply false
   id 'androidx.navigation.safeargs' version '2.4.2' apply false
   id 'com.google.gms.google-services' version '4.3.10' apply false
}
```

2. Add databinding and view binding settings in the app gradle file 
```
plugins {
   id 'com.android.application'
}

android {
   ...
   buildFeatures {
       viewBinding true
       dataBinding true
   }
}
```

3. Add the dependencies to use the gateway sdk, please ensure to use the digio bom dependency to manage version of all imports 
```
dependencies {
    implementation(platform("com.github.digio-tech:digio-bom:v1.0.1"))
    implementation 'androidx.appcompat:appcompat'
    implementation 'com.google.android.material:material'
    implementation "androidx.navigation:navigation-fragment-ktx"
    implementation "androidx.navigation:navigation-ui-ktx"

    implementation 'com.github.digio-tech:gateway'
    implementation 'androidx.swiperefreshlayout:swiperefreshlayout'
    implementation 'com.android.volley:volley'
    implementation 'com.squareup.okhttp3:okhttp'
    implementation 'com.squareup.okhttp3:logging-interceptor'
    implementation("com.google.code.gson:gson")


    implementation 'com.github.digio-tech:digio_permissions'
    implementation 'com.google.android.gms:play-services-location'

```

4. Every feature by digio is given as a dependency import, version controlled by bom. Please include dependencies based on feature used. Please ensure the bom dependency is added along with gateway dependencies  

5. Include Esign OTP in project
```
    implementation 'com.github.digio-tech:esign_otp'
    implementation 'com.github.digio-tech:protean-esign'
    implementation 'com.github.digio-tech:cvl_esign'
    implementation 'com.scottyab:rootbeer-lib'
```
6. Include Esign biometric or face signing in project
```
    implementation 'com.github.digio-tech:protean-esign'
    implementation 'com.github.digio-tech:cvl_esign'
    implementation 'com.scottyab:rootbeer-lib'
    implementation 'com.github.digio-tech:esign_biometrics'
    implementation 'com.github.digio-tech:cvl_rdservice'
```

7. Include Kyc phone camera sdk implementation in project
```
    implementation 'com.github.digio-tech:sdk_native_camera'
    implementation 'com.github.digio-tech:image_processor'
    implementation 'androidx.exifinterface:exifinterface'

```

8. Include Kyc with google's ml camera implementations in project 
```
    implementation 'com.github.digio-tech:sdk_ml_camera'
    implementation 'com.afollestad.material-dialogs:core'
    implementation 'com.google.mlkit:face-detection'
    implementation "androidx.camera:camera-core"
    implementation "androidx.camera:camera-camera2"
    implementation "androidx.camera:camera-lifecycle"
    implementation "androidx.camera:camera-view"
    implementation "androidx.camera:camera-video"
    implementation 'androidx.preference:preference-ktx'
    implementation 'androidx.exifinterface:exifinterface'

```

9. Include Signing mandate in project 
```
    implementation 'com.github.digio-tech:esign_mandate'
    implementation 'androidx.core:core-ktx'
    implementation 'androidx.appcompat:appcompat'
    implementation 'androidx.activity:activity-ktx'
    implementation("androidx.constraintlayout:constraintlayout")

```

10. Include Offline Kyc in project
```
    implementation 'com.github.digio-tech:sdk_offlinekyc'
    implementation 'androidx.activity:activity-ktx'
    implementation 'androidx.constraintlayout:constraintlayout'
    implementation 'androidx.appcompat:appcompat'
    implementation 'com.google.android.material:material'
    implementation "androidx.navigation:navigation-fragment-ktx"
    implementation "androidx.navigation:navigation-ui-ktx"
    implementation 'androidx.swiperefreshlayout:swiperefreshlayout'
    implementation 'com.google.code.gson:gson'

```
11. Include UPI in project 
```
    implementation 'com.github.digio-tech:sdk_upi'
    implementation("androidx.core:core-ktx")
    implementation("com.squareup.retrofit2:retrofit")
    implementation("org.jetbrains.kotlinx:kotlinx-coroutines-android")
    implementation("org.jetbrains.kotlinx:kotlinx-coroutines-core")
    implementation("androidx.fragment:fragment-ktx")
    implementation("androidx.lifecycle:lifecycle-livedata-ktx")
    implementation("com.squareup.retrofit2:converter-gson")

```

### Please ensure using the gateway and the digio bom dependency first before using any other feature. When using bom dependency we handle the versions for each module and dependency implementations. In case any dependency is missing for a module, please check logs for import messages. In case you want to override provided dependency version, give the specific version when adding dependency. 


### Permissions : Add required permissions in manifest file and run time. Note - This is the common SDK for various KYC flows

```
<!--RECORD_AUDIO and MODIFY_AUDIO_SETTINGS Permission required for Video KYC -->
<uses-permission android:name="android.permission.RECORD_AUDIO" />
<uses-permission android:name="android.permission.MODIFY_AUDIO_SETTINGS" />

/** Required for geo tagging */
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />


/** Required for ID card analysis, selfie  and face match**/
<uses-permission android:name="android.permission.CAMERA" />

<uses-feature android:name="android.hardware.camera" android:required="true" />
<uses-feature android:name="android.hardware.camera.autofocus"   android:required="false" />
```
A fintech android app can't access following permission
- Read_external_storage
- Read_media_images
- Read_contacts
- Access_fine_location - **It can be used but requires clarification when releasing on playstore**
- Read_phone_numbers
- Read_media_videos

**Note**: All required permission should be define in manifest file as well as take runtime permission.
Permissions on Android , optionally required permissions should be asked before starting the workflow.



### How to trigger the sdk flow 
When triggering from java activity

1. Implement the DigioSuccessFailureInterface in the activity or fragment
```
public class MainActivity extends AppCompatActivity implements DigioSuccessFailureInterface {
   @Override
    protected void onCreate(Bundle savedInstanceState) {
     }

//this event is triggered on each event, be it failure or success or screen event updates, keep a log to provide inputs
    @Override
    public void onUpdateEvent(@Nullable GatewayEvent event) {
        assert event != null;
        binding.response.setText(event.toString());
        Log.d(TAG, "onUpdateEvent: " + event.toString());
    }

    //called when the process is completed successfully
    @Override
    public void onSuccess(@Nullable DigioSdkResponse sdkResponse) {
        assert sdkResponse != null;
        binding.response.setText(sdkResponse.toString());
        Log.d(TAG, "onSuccess: " + sdkResponse);
    }

    //called when the process is failed due to any reasons. Please check message inside the DigioSdkResponse object to get the reasons and debug accordingly
    @Override
    public void onFailure(@Nullable DigioSdkResponse sdkResponse) {
        assert sdkResponse != null;
        binding.response.setText(sdkResponse.toString());
        Log.d(TAG, "onFailure: " + sdkResponse);
    }
}
```

2. Add DigioTheme to be used 
```
        DigioTheme theme = new DigioTheme();
        theme.setPrimaryColor(android.R.color.holo_red_dark);
        theme.setPrimaryColorHex("#0261B0");
        theme.setFontFamily("Unbounded");
        theme.setSecondaryColorHex("#141414");
        theme.setFontUrl("https://fonts.googleapis.com/css2?family=Unbounded:wght@200&display=swap");     
```

3. Create a DigioConfig object **Use the theme object created above**
```
        DigioConfig digioConfig = new DigioConfig();
        digioConfig.setTheme(theme);  
        digioConfig.setFaqButton(android.R.drawable.ic_menu_help);
        digioConfig.setCloseButton(android.R.drawable.ic_menu_close_clear_cancel);
        digioConfig.setLogo("https://www.digio.in/images/digio_blue.png"); // Your company logo url
        digioConfig.setEnvironment(DigioEnvironment.DEVELOPMENT); // SANDBOX or PRODUCTION
        digioConfig.setServiceMode(DigioServiceMode.FACE);


        //add the user identifier, request ID and token for the session
        digioConfig.setGToken("GWT2310191738");
        digioConfig.setRequestId("DID2405201027408WRECZKLB57ORQW");
        digioConfig.setUserIdentifier("a@gmail.com");
```

4. Sdk must be initialsed in the oncreate of the activity or fragment. ** Call the start only after init is called **
```
public class MainActivity extends AppCompatActivity implements DigioSuccessFailureInterface {
    Digio digio = new Digio();
    DigioConfig digioConfig = new DigioConfig();
    DigioTheme theme = new DigioTheme();


    @Override
    protected void onCreate(Bundle savedInstanceState) {
      digio.initSession(MainActivity.this, this);
      initDigioConfig()
    }

    private void setTheme() {
        theme.setPrimaryColor(android.R.color.holo_red_dark);
        theme.setPrimaryColorHex("#0261B0");
        theme.setFontFamily("Unbounded");
        theme.setSecondaryColorHex("#141414");
        theme.setFontUrl("https://fonts.googleapis.com/css2?family=Unbounded:wght@200&display=swap");     
    }

    private void initDigioConfig() {
        setTheme()
        digioConfig.setTheme(theme);  
        digioConfig.setFaqButton(android.R.drawable.ic_menu_help);
        digioConfig.setCloseButton(android.R.drawable.ic_menu_close_clear_cancel);
        digioConfig.setLogo("https://www.digio.in/images/digio_blue.png"); // Your company logo url
        digioConfig.setEnvironment(DigioEnvironment.DEVELOPMENT); // SANDBOX or PRODUCTION
        digioConfig.setServiceMode(DigioServiceMode.FACE);

        //add the user identifier, request ID and token for the session
        digioConfig.setGToken("GWT2310191738");
        digioConfig.setRequestId("DID2405201027408WRECZKLB57ORQW");
        digioConfig.setUserIdentifier("a@gmail.com");

        //This can be called even on buttonClicks
        binding.startProcessButton.setOnClickListener(v -> {
           startSession()
        });
        startSession()
    }

    private void startSession() {
        try {
            digio.startSession(
                    digioConfig,
                    this
            );
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```
5. For clients willing to use UPI and OKYC sdk should pass the DigioServiceMode explicitly. 
```
private void initDigioConfig() {
        setTheme()
        ...
        digioConfig.setEnvironment(DigioEnvironment.DEVELOPMENT); 
        digioConfig.setServiceMode(DigioServiceMode.UPI);     //Explicitly pass UPI/OKYC 
        ....
    }
```
6. **Note - Please check logs in the onUpdate event of the DigioSuccessFailureListener for any kind of issues or events** 













































