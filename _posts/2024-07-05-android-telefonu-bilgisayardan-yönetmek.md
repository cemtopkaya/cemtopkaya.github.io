---
layout: post
title:  "Android Telefonu Bilgisayardan Yönetmek"
date:   2024-07-05 11:57:24 +0000
categories: Android
tags: android networking usb-tethering
---
Kullandığım [scrcpy](https://github.com/Genymobile/scrcpy/tree/master) uygulamasının adresi: [https://github.com/Genymobile/scrcpy/blob/master/doc/windows.md](https://github.com/Genymobile/scrcpy/blob/master/doc/windows.md)

Geliştirici Modu’na getirip “USB Debugging” özelliğini aktif ediniz

<!-- ![](https://miro.medium.com/v2/resize:fit:424/1*jpA_DkvKw6bv108ebg3OdQ.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_jpA_DkvKw6bv108ebg3OdQ.webp)

Benim zip dosyasını açtığım dizinden scrcpy uygulamasını çalıştırınca bağlantı kurdu ve telefonun ekranı geldi:

```
D:\_portables\scrcpy-win64-v2.4\scrcpy-win64-v2.4>scrcpy.exescrcpy 2.4 <https://github.com/Genymobile/scrcpy>INFO: ADB device found:INFO:     -->   (usb)  euprizvslnqgkvv8                device  Redmi_Note_8_ProD:\_portables\scrcpy-win64-v2.4\scrcpy-win64-v2.4\scrcpy-server: 1 file pushed, 0 skipped. 76.9 MB/s (69007 bytes in 0.001s)[server] INFO: Device: [Xiaomi] Redmi Redmi Note 8 Pro (Android 11)INFO: Renderer: direct3dINFO: Texture: 1080x2336
```

Klavye ve fare ile yönetimi başlatmak için:

```
scrcpy -K -Mscrcpy --keyboard=uhid --mouse=uhid
```

<!-- ![](https://miro.medium.com/v2/resize:fit:436/1*VwRYluoXDk0Sfi8xpuR2tQ.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_VwRYluoXDk0Sfi8xpuR2tQ.webp)

Sürekli ekranı açık bırakmak için `--stay-awake` bayrağını kullanabiliriz.

Bağlandıktan sonra telefonun ekranını kapatmak için `--turn-screen-off` bayrağını kullanabiliriz.

<!-- ![](https://miro.medium.com/v2/resize:fit:700/1*di1ByQZYOvdJ3Wq6GgU21Q.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_di1ByQZYOvdJ3Wq6GgU21Q.webp)

Bu şekilde ayaklandırmak için güvenlik ayarları için de izin vermeniz gerekiyor:

<!-- ![](https://miro.medium.com/v2/resize:fit:443/1*gpQ7Jv65WuKloGNx6CLxBg.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_gpQ7Jv65WuKloGNx6CLxBg.webp)

Aksi halde şu şekilde hata alırsınız:

```shell
D:\_portables\scrcpy-win64-v2.4\scrcpy-win64-v2.4>scrcpy -K -M  --turn-screen-off --stay-awake
scrcpy 2.4 <https://github.com/Genymobile/scrcpy>
INFO: ADB device found:
INFO:     -->   (usb)  euprizvslnqgkvv8                device  Redmi_Note_8_Pro
D:\_portables\scrcpy-win64-v2.4\scrcpy-win64-v2.4\scrcpy-s... file pushed, 0 skipped. 70.9 MB/s (69007 bytes in 0.001s)
[server] INFO: Device: [Xiaomi] Redmi Redmi Note 8 Pro (Android 11)
[server] ERROR: Could not invoke method
java.lang.reflect.InvocationTargetException
        at java.lang.reflect.Method.invoke(Native Method)
        at com.genymobile.scrcpy.wrappers.ContentProvider.call(ContentProvider.java:97)
        at com.genymobile.scrcpy.wrappers.ContentProvider.putValue(ContentProvider.java:156)
        at com.genymobile.scrcpy.Settings.getAndPutValue(Settings.java:68)
        at com.genymobile.scrcpy.Server.initAndCleanUp(Server.java:75)
        at com.genymobile.scrcpy.Server.lambda$startInitThread$2(Server.java:206)
        at com.genymobile.scrcpy.Server$$ExternalSyntheticLambda0.run(Unknown Source:4)
        at java.lang.Thread.run(Thread.java:923)
Caused by: java.lang.SecurityException: Permission denial: writing to settings requires:android.permission.WRITE_SECURE_SETTINGS
        at android.os.Parcel.createExceptionOrNull(Parcel.java:2376)
        at android.os.Parcel.createException(Parcel.java:2360)
        at android.os.Parcel.readException(Parcel.java:2343)
        at android.database.DatabaseUtils.readExceptionFromParcel(DatabaseUtils.java:190)
        at android.database.DatabaseUtils.readExceptionFromParcel(DatabaseUtils.java:142)
        at android.content.ContentProviderProxy.call(ContentProviderNative.java:732)
        ... 8 more
Caused by: android.os.RemoteException: Remote stack trace:
        at com.android.providers.settings.SettingsProvider.enforceWritePermission(SettingsProvider.java:2105)
        at com.android.providers.settings.SettingsProvider.mutateGlobalSetting(SettingsProvider.java:1291)
        at com.android.providers.settings.SettingsProvider.insertGlobalSetting(SettingsProvider.java:1245)
        at com.android.providers.settings.SettingsProvider.call(SettingsProvider.java:398)
        at android.content.ContentProvider.call(ContentProvider.java:2471)

[server] WARN: Could not get and put settings value via ContentProvider, fallback to settings process
com.genymobile.scrcpy.SettingsException: Could not access settings: global put stay_on_while_plugged_in 7
        at com.genymobile.scrcpy.wrappers.ContentProvider.putValue(ContentProvider.java:158)
        at com.genymobile.scrcpy.Settings.getAndPutValue(Settings.java:68)
        at com.genymobile.scrcpy.Server.initAndCleanUp(Server.java:75)
        at com.genymobile.scrcpy.Server.lambda$startInitThread$2(Server.java:206)
        at com.genymobile.scrcpy.Server$$ExternalSyntheticLambda0.run(Unknown Source:4)
        at java.lang.Thread.run(Thread.java:923)
Caused by: java.lang.reflect.InvocationTargetException
        at java.lang.reflect.Method.invoke(Native Method)
        at com.genymobile.scrcpy.wrappers.ContentProvider.call(ContentProvider.java:97)
        at com.genymobile.scrcpy.wrappers.ContentProvider.putValue(ContentProvider.java:156)
        ... 5 more
Caused by: java.lang.SecurityException: Permission denial: writing to settings requires:android.permission.WRITE_SECURE_SETTINGS
        at android.os.Parcel.createExceptionOrNull(Parcel.java:2376)
        at android.os.Parcel.createException(Parcel.java:2360)
        at android.os.Parcel.readException(Parcel.java:2343)
        at android.database.DatabaseUtils.readExceptionFromParcel(DatabaseUtils.java:190)
        at android.database.DatabaseUtils.readExceptionFromParcel(DatabaseUtils.java:142)
        at android.content.ContentProviderProxy.call(ContentProviderNative.java:732)
        ... 8 more
Caused by: android.os.RemoteException: Remote stack trace:
        at com.android.providers.settings.SettingsProvider.enforceWritePermission(SettingsProvider.java:2105)
        at com.android.providers.settings.SettingsProvider.mutateGlobalSetting(SettingsProvider.java:1291)
        at com.android.providers.settings.SettingsProvider.insertGlobalSetting(SettingsProvider.java:1245)
        at com.android.providers.settings.SettingsProvider.call(SettingsProvider.java:398)
        at android.content.ContentProvider.call(ContentProvider.java:2471)

[server] ERROR: Could not invoke method
java.lang.reflect.InvocationTargetException
        at java.lang.reflect.Method.invoke(Native Method)
        at com.genymobile.scrcpy.wrappers.ContentProvider.call(ContentProvider.java:97)
        at com.genymobile.scrcpy.wrappers.ContentProvider.putValue(ContentProvider.java:156)
        at com.genymobile.scrcpy.Settings.putValue(Settings.java:53)
        at com.genymobile.scrcpy.Settings.getAndPutValue(Settings.java:78)
        at com.genymobile.scrcpy.Server.initAndCleanUp(Server.java:75)
        at com.genymobile.scrcpy.Server.lambda$startInitThread$2(Server.java:206)
        at com.genymobile.scrcpy.Server$$ExternalSyntheticLambda0.run(Unknown Source:4)
        at java.lang.Thread.run(Thread.java:923)
Caused by: java.lang.SecurityException: Permission denial: writing to settings requires:android.permission.WRITE_SECURE_SETTINGS
        at android.os.Parcel.createExceptionOrNull(Parcel.java:2376)
        at android.os.Parcel.createException(Parcel.java:2360)
        at android.os.Parcel.readException(Parcel.java:2343)
        at android.database.DatabaseUtils.readExceptionFromParcel(DatabaseUtils.java:190)
        at android.database.DatabaseUtils.readExceptionFromParcel(DatabaseUtils.java:142)
        at android.content.ContentProviderProxy.call(ContentProviderNative.java:732)
        ... 9 more
[server] WARN: Could not put settings value via ContentProvider, fallback to settings process
com.genymobile.scrcpy.SettingsException: Could not access settings: global put stay_on_while_plugged_in 7
        at com.genymobile.scrcpy.wrappers.ContentProvider.putValue(ContentProvider.java:158)
        at com.genymobile.scrcpy.Settings.putValue(Settings.java:53)
        at com.genymobile.scrcpy.Settings.getAndPutValue(Settings.java:78)
        at com.genymobile.scrcpy.Server.initAndCleanUp(Server.java:75)
        at com.genymobile.scrcpy.Server.lambda$startInitThread$2(Server.java:206)
        at com.genymobile.scrcpy.Server$$ExternalSyntheticLambda0.run(Unknown Source:4)
        at java.lang.Thread.run(Thread.java:923)
Caused by: java.lang.reflect.InvocationTargetException
        at java.lang.reflect.Method.invoke(Native Method)
        at com.genymobile.scrcpy.wrappers.ContentProvider.call(ContentProvider.java:97)
        at com.genymobile.scrcpy.wrappers.ContentProvider.putValue(ContentProvider.java:156)
        ... 6 more
Caused by: java.lang.SecurityException: Permission denial: writing to settings requires:android.permission.WRITE_SECURE_SETTINGS
        at android.os.Parcel.createExceptionOrNull(Parcel.java:2376)
        at android.os.Parcel.createException(Parcel.java:2360)
        at android.os.Parcel.readException(Parcel.java:2343)
        at android.database.DatabaseUtils.readExceptionFromParcel(DatabaseUtils.java:190)
        at android.database.DatabaseUtils.readExceptionFromParcel(DatabaseUtils.java:142)
        at android.content.ContentProviderProxy.call(ContentProviderNative.java:732)
        ... 9 more
[server] ERROR: Could not change "stay_on_while_plugged_in"
com.genymobile.scrcpy.SettingsException: Could not access settings: put global stay_on_while_plugged_in 7
        at com.genymobile.scrcpy.Settings.execSettingsPut(Settings.java:24)
        at com.genymobile.scrcpy.Settings.putValue(Settings.java:59)
        at com.genymobile.scrcpy.Settings.getAndPutValue(Settings.java:78)
        at com.genymobile.scrcpy.Server.initAndCleanUp(Server.java:75)
        at com.genymobile.scrcpy.Server.lambda$startInitThread$2(Server.java:206)
        at com.genymobile.scrcpy.Server$$ExternalSyntheticLambda0.run(Unknown Source:4)
        at java.lang.Thread.run(Thread.java:923)
Caused by: java.io.IOException: Command [settings, put, global, stay_on_while_plugged_in, 7] returned with value 255
        at com.genymobile.scrcpy.Command.exec(Command.java:16)
        at com.genymobile.scrcpy.Settings.execSettingsPut(Settings.java:22)
        ... 6 more
INFO: Renderer: direct3d
INFO: Texture: 1080x2336
[server] INFO: Device screen turned off
```