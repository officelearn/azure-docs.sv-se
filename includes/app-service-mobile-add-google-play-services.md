1. Öffnen Sie den Android SDK Manager, auf das Symbol auf der Symbolleiste von Android Studio klicken oder indem Sie **Tools** -> **Android** -> **SDK Manager** im Menü. Suchen Sie die Zielversion des Android SDK, das verwendet wird, in Ihrem Projekt, öffnen Sie es, und wählen **Google-APIs**, wenn es nicht bereits installiert ist.

2. Wechseln Sie zu **Datei**, **Projektstruktur**. Aktivieren Sie Pushbenachrichtigungen unter **Benachrichtigungen**.

3. Öffnen Sie **AndroidManifest.xml** und fügen Sie dieses Tag in die *Anwendung* Tag.

        <meta-data android:name="com.google.android.gms.version"
            android:value="@integer/google_play_services_version" />
 






