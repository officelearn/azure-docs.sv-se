
1. Klicken Sie in Visual Studio im Projektmappen-Explorer mit der rechten Maustaste, klicken Sie auf das Dienstprojekt, und klicken Sie auf **neue Instanz starten** unter der **Debuggen** Kontextmenü.

    ![Mobiles Dienstprojekt lokal starten](./media/mobile-services-dotnet-backend-test-local-service-api-documentation/vs-start-debug-service-project.png)

    Visual Studio öffnet die Standardwebseite für Ihren Dienst. Standardmäßig hostet Visual Studio Ihren mobilen Dienst lokal in IIS Express.

2. Klicken Sie mit der rechten Maustaste auf das IIS Express-Symbol in der Windows-Taskleiste, und überprüfen Sie, ob Ihr mobiler Dienst gestartet wurde.

     ![Mobilen Dienst in der Taskleiste überprüfen](./media/mobile-services-dotnet-backend-test-local-service-api-documentation/iis-express-tray.png)

3. Klicken Sie auf der Startseite Ihres .NET Back-Ends auf **ausprobieren**.

    ![Startseite des mobilen Diensts](./media/mobile-services-dotnet-backend-test-local-service-api-documentation/service-welcome-page.png)

    Daraufhin wird die API-Dokumentationsseite angezeigt, die Sie zum Testen des mobilen Diensts verwenden können.

    >[AZURE.NOTE]Authentifizierung ist nicht erforderlich, um diese Seite zu öffnen, die lokal ausgeführt werden. Bei Ausführung in Azure müssen Sie den Anwendungsschlüssel als Kennwort (ohne Benutzernamen) angeben, um auf diese Seite zugreifen zu können.

4. Klicken Sie auf die **GET Tables/TodoItem** Link.

    ![](./media/mobile-services-dotnet-backend-test-local-service-api-documentation/service-api-documentation-page.png)
    
    Daraufhin wird die GET-Antwortseite für die API angezeigt.

5. Klicken Sie auf **Probieren Sie dies** und klicken Sie dann auf **Senden**.
 
    ![](./media/mobile-services-dotnet-backend-test-local-service-api-documentation/service-try-this-out-get-todoitems.png)

    Dadurch wird eine GET-Anforderung an den lokalen mobilen Dienst gesendet, die zur Rückgabe aller Zeilen in der TodoItem-Tabelle auffordert. Da die Tabelle vom Initialisierer mit Startwerten belegt wird, werden im Hauptteil der Antwortnachricht zwei TodoItem-Objekte zurückgegeben. Weitere Informationen zu Initialisierern finden Sie unter [Bereitstellen von Änderungen des Datenmodells für einen mobilen .NET Back-End-Dienst](../articles/mobile-services-dotnet-backend-how-to-use-code-first-migrations.md).

    ![](./media/mobile-services-dotnet-backend-test-local-service-api-documentation/service-try-this-out-get-response.png)

