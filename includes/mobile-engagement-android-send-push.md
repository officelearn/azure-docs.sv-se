
### <a name="update-manifest-file-to-enable-notifications"></a>Uppdatera manifestfilen om du vill aktivera meddelanden
Kopiera resurserna för aviseringar via app nedan och klistra in dem i din Manifest.xml-fil mellan taggarna `<application>` och `</application>`.

        <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity" android:theme="@android:style/Theme.Light" android:exported="false">
            <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT" />
                <data android:mimeType="text/plain" />
            </intent-filter>
        </activity>
        <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity" android:theme="@android:style/Theme.Light" android:exported="false">
            <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT" />
                <data android:mimeType="text/html" />
            </intent-filter>
        </activity>
        <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementPollActivity" android:theme="@android:style/Theme.Light" android:exported="false">
            <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
                <category android:name="android.intent.category.DEFAULT" />
            </intent-filter>
        </activity>
        <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementLoadingActivity" android:theme="@android:style/Theme.Dialog" android:exported="false">
            <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.LOADING"/>
                <category android:name="android.intent.category.DEFAULT"/>
            </intent-filter>
        </activity>
        <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver" android:exported="false">
            <intent-filter>
                <action android:name="android.intent.action.BOOT_COMPLETED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
            </intent-filter>
        </receiver>
        <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachDownloadReceiver">
            <intent-filter>
                <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
            </intent-filter>
        </receiver>

### <a name="specify-an-icon-for-notifications"></a>Ange en ikon för meddelanden
Klistra in följande XML-kodstycke i din Manifest.xml-fil mellan taggarna `<application>` och `</application>`.

        <meta-data android:name="engagement:reach:notification:icon" android:value="engagement_close"/>

Detta definierar ikonen som visas både i systemmeddelanden och aviseringar via appen. Det är valfritt för aviseringar via app men obligatoriskt för systemmeddelanden. Android avvisar systemmeddelanden med ogiltiga ikoner.

Se till att du använder en ikon som finns i en av **drawable**-mapparna (t.ex. ``engagement_close.png``). **mipmap**-mappen stöds inte.

> [!NOTE]
> Du bör inte använda **launcher**-ikonen. Den har en annan upplösning och ligger vanligtvis i mipmap-mapparna som inte stöds.
> 
> 

För riktiga appar kan du använda en ikon som passar för aviseringar enligt [designriktlinjerna för Android](http://developer.android.com/design/patterns/notifications.html).

> [!TIP]
> För att vara säker på att du använder rätt ikonupplösning, kan du kolla på [de här exemplen](https://www.google.com/design/icons).
> Bläddra ned till **Notification**-avsnittet, klicka på en ikon och klicka sedan på `PNGS` för att hämta drawable-ikonuppsättningen. Du kan se vilka drawable-mappar med vilken upplösning som ska användas för varje version av ikonen.
> 
> 

### <a name="enable-your-app-to-receive-gcm-push-notifications"></a>Konfigurera appen för att ta emot push-meddelanden med GCM
1. Klistra in följande i din Manifest.xml mellan taggarna `<application>` och `</application>` efter att du ersatt **Avsändar-ID** som du fick från din Firebase-projektkonsol. Markeringen ”\n” är avsiktlig, så se till att du avslutar projektnumret med det.
   
        <meta-data android:name="engagement:gcm:sender" android:value="************\n" />
2. Klistra in koden nedan i din Manifest.xml-fil mellan taggarna `<application>` och `</application>`. Ersätt paketnamnet <Your package name>.
   
        <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMEnabler"
        android:exported="false">
            <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT" />
            </intent-filter>
        </receiver>
   
        <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMReceiver" android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="<Your package name>" />
            </intent-filter>
        </receiver>
3. Lägg till den sista uppsättningen behörigheter som är markerade innan `<application>`-taggen. Ersätt `<Your package name>` med det faktiska paketnamnet för programmet.
   
        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
        <uses-permission android:name="<Your package name>.permission.C2D_MESSAGE" />
        <permission android:name="<Your package name>.permission.C2D_MESSAGE" android:protectionLevel="signature" />

<!--HONumber=Oct16_HO3-->


