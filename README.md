# Skandinav Car Headunit

This repository addresses the car head unit originally sold by Skandinav, a company that ceased operations in 2020. The device's Google platform integration (i.e., Google login functionality) became non-functional around October 2025. The main objective is to keep this device usable (e.g., via a Custom Android build, Linux, etc.) and avoid espionage. Please feel free to contribute your knowledge via [issues tab](../../issues)!

## General Information

* MCU: TS907.170330
* Android 6.0, Security Patch: Jun 01 2016
* Linux version 3.18.19 (root@ubuntu) (gcc version 4.9.x-google 20140827 (prerelease) (GCC) ) #5 SMP PREEMPT Fri Oct 27 15:49:42 CST 2017
* Build: android-trunk-m0-V1.1, alps/full_evb3562v_c_66_m0, userdebug/test-keys
* processor: ARM64-v8a Dual-core?
* storage: 2 GB	RAM

## Update Google Apps

Google Apps stopped working, because of expired SSL certificates and/or outdated apps. Automated updates stop working, because of connectivity issues. SSL certificates can't be updated without root permission. User defined root certificates produce occupy warnings.  
'Google Play Store' and 'Google Service' can be updated manually, but account registration still gets stucked while being asked, if current phone number should be used for the current account. Skip and accept button do not work.

* Deinstall/Deactivate 'Google Play Store' and 'Google Play Service' to undo updates.
* Install [Google Play Services 10.2.99](https://www.apkmirror.com/apk/google-inc/google-play-services/google-play-services-10-2-99-release/google-play-services-10-2-99-440-148249840-android-apk-download/). (Later versions contain HOME intent, which is forbidden by os via adb installation, but Android itself is able to install such versions.)
* Install [Google Play Store 48.7.17-29](https://www.apkmirror.com/apk/google-inc/google-play-store/google-play-store-48-7-17-release/google-play-store-48-7-17-29-0-pr-826582504-4-android-apk-download/). (currently latest)
* Open 'Google Play Store' and update 'Google Framework' via three dots menu in the top right corner.
* Protect cannot be activated, because the device itself is not [listed](https://storage.googleapis.com/play_public/supported_devices.html) as safe.

* Install [Google Maps 11.127.0103](https://www.apkmirror.com/apk/google-inc/maps/maps-11-127-0103-release/google-maps-11-127-0103-2-android-apk-download/) (latest Android 6.0+ version)
* Install [Android Auto 7.4.620994](https://www.apkmirror.com/apk/google-inc/android-auto/android-auto-7-4-6209-release/android-auto-7-4-620994-release-android-apk-download/) (latest Android 6.0+ version)

```shell
adb install -r "com.google.android.gms_10.2.99_(440-148249840)-10299440_minAPI23(arm64-v8a,armeabi-v7a)(nodpi)_apkmirror.com.apk" # Google Play Services
adb install -r "com.android.vending_48.7.17-23_0_PR_826582504-84871700_minAPI23(arm64-v8a,armeabi-v7a,x86,x86_64)(nodpi)_apkmirror.com.apk" # Google Play Store
# Open 'Google Play Store' app and update 'Google Framework' via the three dots in the top right corner
adb install -r "com.google.android.apps.maps_11.127.0103-1067509019_minAPI23(arm64-v8a,armeabi-v7a,x86,x86_64)(nodpi)_apkmirror.com.apk" # Google Maps
adb install -r "com.google.android.projection.gearhead_7.4.620994-release-74620994_minAPI23(arm64-v8a)(nodpi)_apkmirror.com.apk" # Android Auto
```

## Security Flaws

### Asian NTP

By default, the NTP server is configured to `asia.pool.ntp.org`. This can be changed via ADB. Check [this list](https://www.ntppool.org/) for possible values.

```shell
$ adb shell settings get global ntp_server
null
$ adb shell settings put global ntp_server europe.pool.ntp.org
$ adb shell settings get global ntp_server
europe.pool.ntp.org
```

### Dialing home

Analysis suggests the device transmits the user's GPS coordinates to China using an **unencrypted** connection.

```
GET http://connectivitycheck.gstatic.com/generate_204 HTTP/1.1
HTTP/1.1 204 No Content

GET http://car.forfan.com.cn/78666170705f763133755f3137.zip
HTTP/1.1 404 Not found

POST http://ljj.forfan.com.cn/forfan/update.php HTTP/1.1
DEVICE=${DEVICE}&GPSX${GPSX}&GPSY=${GPSY}
HTTP/1.1 200 OK
DEVICE: base64 encoded binary data?
GPSX: Longitude in degree * 10e6 (integer, i.e. 13405300 for 13.405300°E)
GPSY: Latitude in degree * 10e6 (integer, i.e. 52520000 for 52.520000°N)
```
