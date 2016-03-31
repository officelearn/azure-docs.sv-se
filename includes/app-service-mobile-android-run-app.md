
1. Besuchen Sie die [Azure Portal]. Klicken Sie auf **Alle durchsuchen** > **Mobile Apps** > das Back-End, das Sie gerade erstellt haben. Klicken Sie in der mobilen app-Einstellungen auf **Schnellstart** > **Android)**. Unter **Konfigurieren Ihrer Client-Basis**, klicken Sie auf **herunterladen**. Damit laden Sie ein vollständiges Android-Projekt für eine App herunter, das für eine Verbindung mit dem Back-End vorkonfiguriert ist. 

2. Öffnen Sie das Projekt mit **Android Studio**, wobei **Import Project (Eclipse ADT, Gradle, usw.)**. Achten Sie auf diese wichtige Importauswahl, um JDK-Fehler zu vermeiden.

3. Drücken Sie die **Ausführen 'Anwendung'** um das Projekt erstellen und die app im Android-Simulator zu starten.

4. Geben Sie in der app einen sinnvollen Text ein, z. B. _Abschließen des Lernprogramms_ und klicken Sie dann auf die Schaltfläche "Hinzufügen". Damit wird eine POST-Anforderung an das Azure-Back-End gesendet, das Sie zuvor bereitgestellt haben. Die Back-End fügt Daten aus der Anforderung in die TodoItem-SQL-Tabelle ein und gibt Informationen über die neu gespeicherten Elemente an die mobile App zurück. Die mobile App zeigt diese Daten in der Liste an. 

    ![](./media/mobile-services-android-get-started/mobile-quickstart-startup-android.png)

[Azure Portal]: https://portal.azure.com/


