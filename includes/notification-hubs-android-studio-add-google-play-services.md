1. Öffnen Sie den Android SDK Manager, auf das Symbol auf der Symbolleiste von Android Studio klicken oder indem Sie **Tools** -> **Android** -> **SDK Manager** im Menü. Suchen Sie die Zielversion des Android SDK, das verwendet wird, in Ihrem Projekt, öffnen Sie ihn durch Klicken auf **Paketdetails anzeigen**, und wählen Sie **Google-APIs**, wenn es nicht bereits installiert ist.

2. Klicken Sie auf die **SDK-Tools** Registerkarte. Wenn Sie die Google-Play-Dienst bereits installiert haben, klicken Sie auf **Google Play Services** wie unten dargestellt. Klicken Sie dann auf **Übernehmen** installieren. 
 
    Notieren Sie den SDK-Pfad, den Sie in einem späteren Schritt angeben müssen. 

    ![](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-sdk-manager.png)


3. Öffnen Sie die **build.gradle** -Datei in die app-Verzeichnis.

    ![](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-add-google-play-dependency.png)

4. Fügen Sie diese Zeile unter *Abhängigkeiten*: 

        compile 'com.google.android.gms:play-services-base:6.5.87'

5. Klicken Sie unter *DefaultConfig*, ändern Sie *MinSdkVersion* bis 9.
 
6. Klicken Sie auf die **Sync Project with Gradle Files** Symbol in der Symbolleiste.

7. Öffnen Sie **AndroidManifest.xml** und fügen Sie dieses Tag in die *Anwendung* Tag.

        <meta-data android:name="com.google.android.gms.version"
            android:value="@integer/google_play_services_version" />
 






