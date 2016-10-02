## 应用清单文件
### 清单文件结构
``` xml
<manifest>

    <uses-permission />
    <permission />
    <permission-tree />
    <permission-group />
    <instrumentation />
    <uses-sdk />
    <uses-configuration />  
    <uses-feature />  
    <supports-screens />  
    <compatible-screens />  
    <supports-gl-texture />  

    <application>

        <activity>
            <intent-filter>
                <action />
                <category />
                <data />
            </intent-filter>
            <meta-data />
        </activity>

        <activity-alias>
            <intent-filter> . . . </intent-filter>
            <meta-data />
        </activity-alias>

        <service>
            <intent-filter> . . . </intent-filter>
            <meta-data/>
        </service>

        <receiver>
            <intent-filter> . . . </intent-filter>
            <meta-data />
        </receiver>

        <provider>
            <grant-uri-permission />
            <meta-data />
            <path-permission />
        </provider>

        <uses-library />

    </application>

</manifest>
```

#### \<activity\>
``` xml
<activity android:allowEmbedded=["true" | "false"]
          android:allowTaskReparenting=["true" | "false"]
          android:alwaysRetainTaskState=["true" | "false"]
          android:autoRemoveFromRecents=["true" | "false"]
          android:banner="drawable resource"
          android:clearTaskOnLaunch=["true" | "false"]
          android:configChanges=["mcc", "mnc", "locale",
                                 "touchscreen", "keyboard", "keyboardHidden",
                                 "navigation", "screenLayout", "fontScale",
                                 "uiMode", "orientation", "screenSize",
                                 "smallestScreenSize"]
          android:documentLaunchMode=["intoExisting" | "always" |
                                  "none" | "never"]
          android:enabled=["true" | "false"]
          android:excludeFromRecents=["true" | "false"]
          android:exported=["true" | "false"]
          android:finishOnTaskLaunch=["true" | "false"]
          android:hardwareAccelerated=["true" | "false"]
          android:icon="drawable resource"
          android:label="string resource"
          android:launchMode=["standard" | "singleTop" |
                              "singleTask" | "singleInstance"]
          android:maxRecents="integer"
          android:multiprocess=["true" | "false"]
          android:name="string"
          android:noHistory=["true" | "false"]  
          android:parentActivityName="string" 
          android:permission="string"
          android:process="string"
          android:relinquishTaskIdentity=["true" | "false"]
          android:resizeableActivity=["true" | "false"]
          android:screenOrientation=["unspecified" | "behind" |
                                     "landscape" | "portrait" |
                                     "reverseLandscape" | "reversePortrait" |
                                     "sensorLandscape" | "sensorPortrait" |
                                     "userLandscape" | "userPortrait" |
                                     "sensor" | "fullSensor" | "nosensor" |
                                     "user" | "fullUser" | "locked"]
          android:stateNotNeeded=["true" | "false"]
          android:supportsPictureInPicture=["true" | "false"]
          android:taskAffinity="string"
          android:theme="resource or theme"
          android:uiOptions=["none" | "splitActionBarWhenNarrow"]
          android:windowSoftInputMode=["stateUnspecified",
                                       "stateUnchanged", "stateHidden",
                                       "stateAlwaysHidden", "stateVisible",
                                       "stateAlwaysVisible", "adjustUnspecified",
                                       "adjustResize", "adjustPan"] >   
    . . .
</activity>
```

#### \<activity-alias\>
``` xml
<activity-alias android:enabled=["true" | "false"]
                android:exported=["true" | "false"]
                android:icon="drawable resource"
                android:label="string resource"
                android:name="string"
                android:permission="string"
                android:targetActivity="string" >
    . . .
</activity-alias>
```

#### \<application\>
``` xml
<application android:allowTaskReparenting=["true" | "false"]
             android:allowBackup=["true" | "false"]
             android:backupAgent="string"
             android:banner="drawable resource"
             android:debuggable=["true" | "false"]
             android:description="string resource"
             android:enabled=["true" | "false"]
             android:hasCode=["true" | "false"]
             android:hardwareAccelerated=["true" | "false"]
             android:icon="drawable resource"
             android:isGame=["true" | "false"]
             android:killAfterRestore=["true" | "false"]
             android:largeHeap=["true" | "false"]
             android:label="string resource"
             android:logo="drawable resource"
             android:manageSpaceActivity="string"
             android:name="string"
             android:permission="string"
             android:persistent=["true" | "false"]
             android:process="string"
             android:restoreAnyVersion=["true" | "false"]
             android:requiredAccountType="string"
             android:resizeableActivity=["true" | "false"]
             android:restrictedAccountType="string"
             android:supportsRtl=["true" | "false"]
             android:taskAffinity="string"
             android:testOnly=["true" | "false"]
             android:theme="resource or theme"
             android:uiOptions=["none" | "splitActionBarWhenNarrow"]
             android:usesCleartextTraffic=["true" | "false"]
             android:vmSafeMode=["true" | "false"] >
    . . .
</application>
```

#### \<compatible-screens\>
``` xml
<compatible-screens>
    <screen android:screenSize=["small" | "normal" | "large" | "xlarge"]
            android:screenDensity=["ldpi" | "mdpi" | "hdpi" | "xhdpi"
                                   | "280" | "360" | "420" | "480" | "560" ] />
    ...
</compatible-screens>
```

#### \<data\>
``` xml
<data android:scheme="string"
      android:host="string"
      android:port="string"
      android:path="string"
      android:pathPattern="string"
      android:pathPrefix="string"
      android:mimeType="string" />
```
      
#### \<grant-uri-permission\>
``` xml
<grant-uri-permission android:path="string"
                      android:pathPattern="string"
                      android:pathPrefix="string" />
```

#### \<instrumentation\>
``` xml
<instrumentation android:functionalTest=["true" | "false"]
                 android:handleProfiling=["true" | "false"]
                 android:icon="drawable resource"
                 android:label="string resource"
                 android:name="string"
                 android:targetPackage="string" />
```
                 
#### \<intent-filter\>
``` xml
<intent-filter android:icon="drawable resource"
               android:label="string resource"
               android:priority="integer" >
    . . .
</intent-filter>
```

#### \<manifest\>
``` xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
          package="string"
          android:sharedUserId="string"
          android:sharedUserLabel="string resource" 
          android:versionCode="integer"
          android:versionName="string"
          android:installLocation=["auto" | "internalOnly" | "preferExternal"] >
    . . .
</manifest>
```

#### \<meta-data\>
``` xml
<meta-data android:name="string"
           android:resource="resource specification"
           android:value="string" />
```

#### \<path-permission\>
``` xml
<path-permission android:path="string"
                 android:pathPrefix="string"
                 android:pathPattern="string"
                 android:permission="string"
                 android:readPermission="string"
                 android:writePermission="string" />
```

#### \<permission\>
``` xml
<permission android:description="string resource"
            android:icon="drawable resource"
            android:label="string resource"
            android:name="string"
            android:permissionGroup="string"
            android:protectionLevel=["normal" | "dangerous" |
                                     "signature" | "signatureOrSystem"] />
```

#### \<permission-group\>
``` xml
<permission-group android:description="string resource"
                  android:icon="drawable resource"
                  android:label="string resource"
                  android:name="string" />
```

#### \<permission-tree\>
``` xml
<permission-tree android:icon="drawable resource"
                 android:label="string resource" ]
                 android:name="string" />
```

#### \<provider\>
``` xml
<provider android:authorities="list"
          android:enabled=["true" | "false"]
          android:exported=["true" | "false"]
          android:grantUriPermissions=["true" | "false"]
          android:icon="drawable resource"
          android:initOrder="integer"
          android:label="string resource"
          android:multiprocess=["true" | "false"]
          android:name="string"
          android:permission="string"
          android:process="string"
          android:readPermission="string"
          android:syncable=["true" | "false"]
          android:writePermission="string" >
    . . .
</provider>
```

#### \<receiver\>
``` xml
<receiver android:enabled=["true" | "false"]
          android:exported=["true" | "false"]
          android:icon="drawable resource"
          android:label="string resource"
          android:name="string"
          android:permission="string"
          android:process="string" >
    . . .
</receiver>
```

#### \<service\>
``` xml
<service android:enabled=["true" | "false"]
         android:exported=["true" | "false"]
         android:icon="drawable resource"
         android:isolatedProcess=["true" | "false"]
         android:label="string resource"
         android:name="string"
         android:permission="string"
         android:process="string" >
    . . .
</service>
```

#### \<supports-screens\>
``` xml
<supports-screens android:resizeable=["true"| "false"]
                  android:smallScreens=["true" | "false"]
                  android:normalScreens=["true" | "false"]
                  android:largeScreens=["true" | "false"]
                  android:xlargeScreens=["true" | "false"]
                  android:anyDensity=["true" | "false"]
                  android:requiresSmallestWidthDp="integer"
                  android:compatibleWidthLimitDp="integer"
                  android:largestWidthLimitDp="integer"/>
```

#### \<uses-configuration\>
``` xml
<uses-configuration
  android:reqFiveWayNav=["true" | "false"]
  android:reqHardKeyboard=["true" | "false"]
  android:reqKeyboardType=["undefined" | "nokeys" | "qwerty" | "twelvekey"]
  android:reqNavigation=["undefined" | "nonav" | "dpad" | "trackball" | "wheel"]
  android:reqTouchScreen=["undefined" | "notouch" | "stylus" | "finger"] />
```

#### \<uses-feature\>
``` xml
<uses-feature
  android:name="string"
  android:required=["true" | "false"]
  android:glEsVersion="integer" />
```

#### \<uses-library\>
``` xml
<uses-library
  android:name="string"
  android:required=["true" | "false"] />
```
  
#### \<uses-permission\>
``` xml
<uses-permission android:name="string"
        android:maxSdkVersion="integer" />
```

#### \<uses-sdk\>
``` xml
<uses-sdk android:minSdkVersion="integer"
          android:targetSdkVersion="integer"
          android:maxSdkVersion="integer" />
```
