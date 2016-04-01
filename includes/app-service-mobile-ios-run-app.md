
1. Auf Ihrem Mac finden Sie auf der [Azure Portal]. Klicken Sie auf **Alle durchsuchen** > **Mobile Apps** > das Back-End, das Sie gerade erstellt haben. Klicken Sie in der mobilen app-Einstellungen auf **Schnellstart** > **iOS (Objective-C)**. Klicken Sie auf Wunsch Swift **Schnellstart** > **iOS (Swift)** stattdessen. Unter **herunterladen und Ausführen der iOS-Projekt**, klicken Sie auf **herunterladen**. Damit laden Sie ein vollständiges Xcode-Projekt für eine App herunter, das für eine Verbindung mit dem Back-End vorkonfiguriert ist. Öffnen Sie das Projekt in Xcode.

2. Drücken Sie die **Ausführen** um das Projekt erstellen und die app im iOS-Simulator zu starten.

3. Geben Sie in der app einen sinnvollen Text ein, z. B. _Abschließen des Lernprogramms_ und klicken Sie dann auf das Pluszeichen (**+**) Symbol. Damit wird eine POST-Anforderung an das Azure-Back-End gesendet, das Sie zuvor bereitgestellt haben. Die Back-End fügt Daten aus der Anforderung in die TodoItem-SQL-Tabelle ein und gibt Informationen über die neu gespeicherten Elemente an die mobile App zurück. Die mobile App zeigt diese Daten in der Liste an. 

    ![](./media/mobile-services-ios-run-app/mobile-quickstart-startup-ios.png)

[Azure Portal]: https://portal.azure.com/


