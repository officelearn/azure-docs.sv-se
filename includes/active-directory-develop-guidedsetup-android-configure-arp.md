## <a name="add-the-applications-registration-information-to-your-app"></a>Lägg till programmets registreringsinformation i din app

I det här steget behöver du Lägg till klient-ID i projektet.

1.  Öppna `MainActivity` (under `app`  >  `java`  >  *`{host}.{namespace}`*)
2.  Ersätt den rad som börjar med `final static String CLIENT_ID` med:
```java
final static String CLIENT_ID = "[Enter the application Id here]";
```
3. Öppna: `app` > `manifests` > `AndroidManifest.xml`
4. Lägg till att följande aktiviteter `manifest\application` noden. Den här registrera en `BrowserTabActivity` så att operativsystem och återuppta ditt program när du har slutfört autentiseringen:

```xml
<!--Intent filter to capture System Browser calling back to our app after Sign In-->
<activity
    android:name="com.microsoft.identity.client.BrowserTabActivity">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />

        <!--Add in your scheme/host from registered redirect URI-->
        <!--By default, the scheme should be similar to 'msal[appId]' -->
        <data android:scheme="msal[Enter the application Id here]"
            android:host="auth" />
    </intent-filter>
</activity>
```

