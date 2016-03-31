
Der letzte Schritt dieses Lernprogramms besteht im Erstellen und Ausführen der neuen App.

1. Navigieren Sie zu dem Verzeichnis, in dem Sie die komprimierten Projektdateien gespeichert haben, erweitern Sie die Dateien auf Ihrem Computer, und öffnen Sie die Projektmappen-Datei in Visual Studio.

2. Drücken Sie die **F5** um das Projekt neu erstellen und die app zu starten.

3. Geben Sie in der app einen sinnvollen Text ein, z. B. *Abschließen des Lernprogramms*, in **Insert a TodoItem**, und klicken Sie dann auf **Speichern**.

    Dadurch wird eine POST-Anforderung an den neuen, in Azure gehosteten mobilen Dienst gesendet. Daten von der Anforderung werden in die TodoItem-Tabelle eingefügt. In der Tabelle gespeicherte Werte werden vom mobilen Dienst zurückgegeben, und die Daten werden in der zweiten Spalte der App angezeigt.

4. (Optional) Ändern Sie in einer universellen Windows-Lösung das Standard-Startprojekt in die andere App und führen Sie die App erneut aus.

    Beachten Sie, dass nach dem Starten der App im vorhergehenden Schritt gespeicherte Daten aus dem mobilen Dienst geladen werden.
 
4. In der [klassischen Azure-Portal](https://manage.windowsazure.com/), klicken Sie auf die **Daten** Registerkarte, und klicken Sie dann auf die **TodoItems** Tabelle.

    Nun können Sie die von der App in die Tabelle eingefügten Daten durchsuchen.

    ![](./media/mobile-services-javascript-backend-run-app/mobile-data-browse.png)

