1. Öffnen Sie den Android SDK Manager, auf das Symbol auf der Symbolleiste von Android Studio klicken oder indem Sie **Tools** -> **Android** -> **SDK Manager** im Menü. Suchen Sie die Zielversion des Android SDK, das verwendet wird, in Ihrem Projekt, öffnen Sie es, und wählen **Google-APIs**, wenn es nicht bereits installiert ist.

2. Führen Sie einen Bildlauf nach unten, um **Extras**, erweitern Sie ihn, und wählen **Google Play Services**, wie unten dargestellt. Klicken Sie auf **Installationspakete**. Beachten Sie den SDK-Pfad für den folgenden Schritt. 

    ![](./media/notification-hubs-android-get-started/notification-hub-create-android-app4.png)


3. Öffnen Sie die **build.gradle** -Datei in die app-Verzeichnis.

    ![](./media/mobile-services-android-get-started-push/android-studio-push-build-gradle.png)

4. Fügen Sie diese Zeile unter *Abhängigkeiten*: 

        compile 'com.google.android.gms:play-services-base:6.5.87'

5. Klicken Sie unter *DefaultConfig*, ändern Sie *MinSdkVersion* bis 9.
 
6. Klicken Sie auf die **Sync Project with Gradle Files** Symbol in der Symbolleiste.

7. Öffnen Sie **AndroidManifest.xml** und fügen Sie dieses Tag in die *Anwendung* Tag.

        <meta-data android:name="com.google.android.gms.version"
            android:value="@integer/google_play_services_version" />
 






