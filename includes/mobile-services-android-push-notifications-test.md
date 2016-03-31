
###Einrichten des Android-Emulators für den Testvorgang
Wenn Sie diese App im Emulator ausführen, stellen Sie sicher, dass Sie ein Android Virtual Device (AVD) verwenden, das Google-APIs unterstützt.

> [AZURE.IMPORTANT] Damit Sie Pushbenachrichtigungen erhalten, müssen Sie ein Google-Konto festlegen, auf Ihrem Android Virtual Device (Navigieren Sie im Emulator zu **Einstellungen** und klicken Sie auf **Konto hinzufügen**). Überprüfen Sie zudem, dass der Emulator mit dem Internet verbunden ist.

1. Aus **Tools**, klicken Sie auf **Open Android Emulator Manager**, wählen Sie das Gerät, und klicken Sie dann auf **Bearbeiten**.

    ![Android Virtual Device Manager](./media/mobile-services-android-push-notifications-test/notification-hub-create-android-app7.png)

2. Wählen Sie **Google-APIs** in **Ziel**, klicken Sie dann auf **OK**.

    ![Bearbeiten von Android Virtual Device](./media/mobile-services-android-push-notifications-test/notification-hub-create-android-app8.png)

3. Klicken Sie auf der Hauptsymbolleiste auf **Ausführen**, und wählen Sie die app. Daraufhin wird der Emulator gestartet und die App ausgeführt.

  Die Anwendung übernimmt die *RegistrationId* aus GCM und registriert sich beim Notification Hub.

###Durch das Einfügen eines neuen Elements wird eine Benachrichtigung generiert.

1. Geben Sie in der app einen sinnvollen Text ein, z. B. _neue Mobile Services-Aufgabe_ und klicken Sie dann auf die **Hinzufügen** Schaltfläche.

2. Führen Sie eine Streifbewegung vom oberen Bildschirmrand aus, um das Notification Center des Geräts zu öffnen, sodass die Benachrichtigung angezeigt wird.

    ![Anzeigen der Benachrichtigung im Notification Center](./media/mobile-services-android-push-notifications-test/notification-area-received.png)

