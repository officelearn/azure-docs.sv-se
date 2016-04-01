
Mit dem mobilen Dienstprojekt, das Sie herunterladen, können Sie Ihren neuen mobilen Dienst direkt auf Ihrem lokalen Computer oder einem virtuellen Computer ausführen. Auf diese Weise ist das Debuggen Ihres Dienstcodes ziemlich einfach, bevor Sie diesen in Azure veröffentlichen.

In diesem Abschnitt testen Sie Ihre neue App für den lokal ausgeführten mobilen Dienst.

1. Navigieren Sie zu dem Verzeichnis, in dem Sie die komprimierten Projektdateien gespeichert haben, erweitern Sie die Dateien auf Ihrem Computer, und öffnen Sie die Projektmappen-Datei in Visual Studio.

2. Im Projektmappen-Explorer in Visual Studio mit der Maustaste Projekt für den Dienst, klicken Sie auf **Set as StartUp Project**, und drücken Sie dann die **F5** -Taste, um das Projekt erstellen und den mobilen Dienst lokal zu starten.

    ![](./media/mobile-services-dotnet-backend-test-local-service-dotnet/mobile-service-startup.png)

    Nachdem der mobile Dienst erfolgreich erstellt wurde, wird eine Webseite angezeigt.

3. Zum Testen der Store-app mit der rechten Maustaste in des Client-app-Projekts, klicken Sie auf **Set as StartUp Project**, und drücken Sie dann die **F5** um das Projekt neu erstellen und die app zu starten.

    Auf diese Weise wird die App gestartet, die eine Verbindung mit der lokalen mobilen Dienstinstanz herstellt.   

4. Geben Sie in der app einen sinnvollen Text ein, z. B. _Abschließen des Lernprogramms_, in **Insert a TodoItem**, und klicken Sie dann auf **Speichern**.

    Daraufhin wird eine POST-Anforderung an den lokalen mobilen Dienst gesendet. Daten von der Anforderung werden in die TodoItem-Tabelle eingefügt. In der Tabelle gespeicherte Werte werden vom mobilen Dienst zurückgegeben, und die Daten werden in der zweiten Spalte der App angezeigt.

