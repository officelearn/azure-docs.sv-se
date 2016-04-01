
Sie können Push-Benachrichtigungen wahlweise testen, wobei der mobile Dienst auf dem lokalen Computer oder dem virtuellen Computer ausgeführt wird, bevor Sie sie in Azure veröffentlichen. Hierzu müssen Sie Informationen über den Benachrichtigungs-Hub, der von Ihrer App verwendet wird, in der Datei "web.config" angeben. Diese Informationen werden nur bei der lokalen Ausführung der App zum Herstellen einer Verbindung mit dem Benachrichtigungs-Hub verwendet. Sie wird bei der Veröffentlichung in Azure ignoriert.

1. Öffnen Sie in die Datei "readme.html" im Projektordner des mobilen Diensts. 

    Dies zeigt die **Push-Installation ist fast abgeschlossen** Seite, wenn Sie noch nicht geöffnet ist. Der Abschnitt **Schritt 3: Ändern von Web Config** enthält Informationen zu Notification Hub.

2. In Ihrem mobilen Dienst-Projekt in Visual Studio, öffnen Sie die Datei "Web.config" für den Dienst dann in **ConnectionStrings**, Hinzufügen der **MS_NotificationHubConnectionString** -Verbindungszeichenfolge aus der **Push-Installation ist fast abgeschlossen** Seite.

3. In **AppSettings**, ersetzen Sie den Wert von der **MS_NotificationHubName** app-Einstellung mit dem Namen des Notification Hubs finden Sie in den **Push-Installation ist fast abgeschlossen** Seite.

4. Mit der rechten Maustaste den mobilen Dienst-Projekt, und klicken Sie auf **Debuggen** dann **neue Instanz starten** und notieren Sie die Stamm-URL der Startseite im Browser angezeigt.

    Dies ist die URL des lokalen Computers für das .NET-Backendprojekt. Diese URL wird benötigt, um die App mit dem mobilen Dienst zu testen, der auf dem lokalen Computer ausgeführt wird.

Ihr mobiles Dienstprojekt ist so konfiguriert, dass eine Verbindung mit dem Benachrichtigungs-Hub in Azure hergestellt wird, wenn es lokal ausgeführt wird. Beachten Sie, dass Sie den gleichen Benachrichtigungs-Hubnamen und die gleiche Verbindungszeichenfolge wie im Portal verwenden müssen, weil diese Projekteinstellungen in der Datei "Web.config" bei der Ausführung in Azure mit den Portaleinstellungen überschrieben werden. 

