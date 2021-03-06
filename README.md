# Remote-Config-App-Update-Dialog

[![Codacy Badge](https://api.codacy.com/project/badge/Grade/eb918005617e4cb1a47b4c113cc7b58a)](https://app.codacy.com/manual/niraj-prajapati/Remote-Config-App-Update-Dialog?utm_source=github.com&utm_medium=referral&utm_content=niraj-prajapati/Remote-Config-App-Update-Dialog&utm_campaign=Badge_Grade_Dashboard)

It helps to change the behavior and appearance of your app without publishing an app update, at no cost, for unlimited daily active users.

<h2>What is Firebase Remote Config?</h2>

Firebase Remote Config is a cloud service that lets you change the behavior and appearance of your app without requiring users to download an app update. When using Remote Config, you create in-app default values that control the behavior and appearance of your app. Then, you can later use the Firebase console or the Remote Config REST API to override in-app default values for all app users or for segments of your user base. Your app controls when updates are applied, and it can frequently check for updates and apply them with a negligible impact on performance.

<h2>Add Firebase to your app</h2>

If you haven't already, [add Firebase to your Android project](https://firebase.google.com/docs/android/setup).

<h2>Add dependency</h2>

```
implementation 'com.google.firebase:firebase-config:19.0.4'
```

<h2>Open Remote Config in Console, Add needed parameters and publish</h2>

![](https://i.ibb.co/SrKXS0g/image.png)


<h2>Add default values</h2>

Create `remote_config_defaults.xml` file in `res>xml` folder

add default values like

```
<?xml version="1.0" encoding="utf-8"?>
<defaultMap>
    <entry>
        <key>latestVersion</key>
        <value>1.0</value>
    </entry>
    <entry>
        <key>forceUpdate</key>
        <value>false</value>
    </entry>
    <entry>
        <key>releaseNotes</key>
        <value>- Feature 1</value>
    </entry>
    <entry>
        <key>updateUrl</key>
        <value>https://nandroidex.wordpress.com/</value>    <!--TODO : PLAY STORE URL-->
    </entry>
</defaultMap>
```

<h2>Fetch values from Firebase</h2>

```
val remoteConfig = FirebaseRemoteConfig.getInstance()
val configSettings = FirebaseRemoteConfigSettings.Builder()
    .setMinimumFetchIntervalInSeconds(10)
    .build()
remoteConfig.setConfigSettingsAsync(configSettings)
remoteConfig.setDefaultsAsync(R.xml.remote_config_defaults)
remoteConfig.fetchAndActivate()

val latestVersion = remoteConfig.getDouble("latestVersion")
val forceUpdate = remoteConfig.getBoolean("forceUpdate")
val updateUrl = remoteConfig.getString("updateUrl")
val releaseNotes = remoteConfig.getString("releaseNotes")
Log.e("TAG", "latestVersion : $latestVersion")
Log.e("TAG", "forceUpdate : $forceUpdate")
Log.e("TAG", "updateUrl : $updateUrl")
Log.e("TAG", "releaseNotes : $releaseNotes")
```

<h2>Check current app version</h2>

```
try {
    val currentVersion = packageManager.getPackageInfo(packageName, 0).versionName
} catch (e: PackageManager.NameNotFoundException) {
    e.printStackTrace()
}
```

<h2>Show dialog if version is different than app version</h2>


```
val dialogUpdate = Dialog(this@MainActivity)
if (currentVersion.toFloat() != latestVersion.toFloat()) {
    dialogUpdate.requestWindowFeature(Window.FEATURE_NO_TITLE)
    dialogUpdate.setContentView(R.layout.dialog_update)
    dialogUpdate.setCancelable(false)
    dialogUpdate.tvReleaseNotes.text = releaseNotes
    dialogUpdate.btnUpdate.setOnClickListener {
        val intent = Intent(Intent.ACTION_VIEW)
        intent.data = Uri.parse(updateUrl)
        startActivity(intent)
    }
    if (forceUpdate) {
        dialogUpdate.btnCancel.visibility = INVISIBLE
    } else {
        dialogUpdate.btnCancel.visibility = VISIBLE
    }
    dialogUpdate.btnCancel.setOnClickListener {
        dialogUpdate.dismiss()
    }
    dialogUpdate.show()
} else {
    if (dialogUpdate.isShowing) {
        dialogUpdate.dismiss()
    }
}
```


# Thanks

<b>Your contributions are welcome.</b>
