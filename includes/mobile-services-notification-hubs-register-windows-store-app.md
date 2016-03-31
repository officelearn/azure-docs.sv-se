

1. Wenn Sie Ihre app noch nicht registriert haben, navigieren Sie zu der [Submit an app page] im Entwicklungscenter für Windows Store-apps, melden Sie sich mit Ihrem Microsoft-Konto, und klicken Sie dann auf **Anwendungsname**.

    ![](./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-submit-win8-app.png)

2. Geben Sie einen Namen für Ihre app im **Anwendungsname**, klicken Sie auf **Anwendungsname reservieren**, und klicken Sie dann auf **Speichern**.

    ![](./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-win8-app-name.png)

    Auf diese Weise wird eine neue Windows Store-Registrierung für Ihre Anwendung erstellt.

3. Öffnen Sie in Visual Studio das Projekt, das Sie erstellt haben, wenn Sie das Lernprogramm **Erste Schritte mit Mobile Services**.

4. Im Projektmappen-Explorer mit der rechten Maustaste, klicken Sie auf **Store**, und klicken Sie dann auf **App mit Store verknüpfen**. 

    ![](./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-store-association.png)

    Dies zeigt die **Zuordnen Ihrer Anwendung im Windows Store** Assistenten.

5. Klicken Sie im Assistenten auf **Anmelden** und melden Sie sich mit Ihrem Microsoft-Konto.

6. Wählen Sie die Anwendung, die Sie in Schritt 2 registriert haben, klicken Sie auf **Weiter**, und klicken Sie dann auf **zuordnen**.

    ![](./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-select-app-name.png)

    Auf diese Weise werden die erforderlichen Windows Store-Registrierungsinformationen zum Anwendungsmanifest hinzugefügt.    

7. (Optional) Wiederholen Sie die Schritte 4 bis 6, um auch das Windows Phone Store-Projekt mit der universellen Windows-App zu registrieren.

8. Klicken Sie in der Windows-Entwicklungscenter-Seite für Ihre neue Anwendung auf **Services**. 

    ![](./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-win8-edit-app.png) 

9. Klicken Sie auf der Seite Dienste auf **Live Services-Website** unter **Azure Mobile Services**.

    ![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-win8-edit2-app.png)

10. Klicken Sie auf **Authentifizieren des Diensts** und notieren Sie die Werte von **Clientschlüssel** und **Paket-Sicherheits-ID (SID)**. 

    ![](./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-win8-app-push-auth.png)

    > [AZURE.NOTE] Der geheime Clientschlüssel und Paket-SID sind wichtige Sicherheitsanmeldeinformationen. Geben Sie diese Informationen nicht weiter und verteilen Sie sie nicht mit Ihrer Anwendung.

11. Melden Sie sich auf die [klassischen Azure-Portal](https://manage.windowsazure.com/), klicken Sie auf **Mobile Services**, und klicken Sie dann auf Ihre app.

    ![](./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-selection.png)

12. Klicken Sie auf die **Push** Registerkarte und geben Sie die **Clientschlüssel** und **Paket-SID** Werte aus WNS, klicken Sie dann auf **Speichern**.

    >[AZURE.NOTE]Wenn Sie dieses Lernprogramm mit einem älteren mobilen Dienst ausführen, finden Sie möglicherweise eine Verknüpfung am unteren Rand der **Push** Registerkarte **erweiterten Push aktivieren**. Klicken Sie jetzt darauf, um Ihren mobilen Dienst für die Integration mit Notification Hubs zu aktualisieren. Diese Änderung kann nicht mehr rückgängig gemacht werden. Einzelheiten zum Aktivieren der erweiterten Pushbenachrichtigungen in einem mobilen Dienst einer Produktionsumgebung finden Sie in <a href="http://go.microsoft.com/fwlink/p/?LinkId=391951">dieser Anleitung</a>. 

    ![](./media/mobile-services-notification-hubs-register-windows-store-app/mobile-push-tab.png)

    >[AZURE.NOTE]Wenn Sie Ihre WNS-Anmeldeinformationen für erweiterte Pushbenachrichtigungen in der **Push** Registerkarte im Portal gelten für Notification Hubs für den benachrichtigungshub für Ihre app konfigurieren.

<!-- URLs. -->
[Get started with Mobile Services]: ../articles/mobile-services-windows-store-get-started.md
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582


