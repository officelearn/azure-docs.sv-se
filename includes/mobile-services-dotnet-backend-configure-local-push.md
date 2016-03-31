
Sie können Push-Benachrichtigungen wahlweise testen, wobei der mobile Dienst auf dem lokalen Computer oder dem virtuellen Computer ausgeführt wird, bevor Sie sie in Azure veröffentlichen. Hierzu müssen Sie Informationen über den Benachrichtigungs-Hub, der von Ihrer App verwendet wird, in der Datei "web.config" angeben. Diese Informationen werden nur bei der lokalen Ausführung der App zum Herstellen einer Verbindung mit dem Benachrichtigungs-Hub verwendet. Sie wird bei der Veröffentlichung in Azure ignoriert.

1. In den **Push** Registerkarte des mobilen Diensts, klicken Sie auf die **Notification Hub** Link.

    ![](./media/mobile-services-dotnet-backend-configure-local-push/link-to-notification-hub.png)

    Damit gelangen Sie zum Benachrichtigungs-Hub, der von Ihrem mobilen Dienst verwendet wird.

2. In der Notification Hub-Seite, notieren Sie sich den Namen des Notification Hub, und klicken Sie dann **View Connection String**.

    ![](./media/mobile-services-dotnet-backend-configure-local-push/notification-hub-page.png)

3. In den **Zugriff auf die Verbindungsinformationen**, Kopie der **DefaultFullSharedAccessSignature** Verbindungszeichenfolge.

    ![](./media/mobile-services-dotnet-backend-configure-local-push/notification-hub-connection-string.png)

4. In Ihrem mobilen Dienst-Projekt in Visual Studio, öffnen Sie die Datei "Web.config" für den Dienst und im **ConnectionStrings**, ersetzen Sie die Verbindungszeichenfolge für **MS_NotificationHubConnectionString** mit der Verbindungszeichenfolge aus dem vorherigen Schritt.

5. In **AppSettings**, ersetzen Sie den Wert von der **MS_NotificationHubName** app-Einstellung mit dem Namen des Notification Hub.

Ihr mobiles Dienstprojekt ist so konfiguriert, dass eine Verbindung mit dem Benachrichtigungs-Hub in Azure hergestellt wird, wenn es lokal ausgeführt wird. Beachten Sie, dass Sie den gleichen Benachrichtigungs-Hubnamen und die gleiche Verbindungszeichenfolge wie im Portal verwenden müssen, weil diese Web.config-Projekteinstellungen bei der Ausführung in Azure überschrieben werden.

