7. Mit der rechten Maustaste in der API-app-Projekt in **Projektmappen-Explorer** und wählen Sie **Veröffentlichen** Öffnen Sie das Dialogfeld "Veröffentlichen". Das Veröffentlichungsprofil, das Sie zuvor erstellt haben, sollte vorausgewählt sein. 

9. Klicken Sie auf **Veröffentlichen** um den Bereitstellungsprozess zu beginnen. 

    ![Bereitstellen der API-App](./media/app-service-api-pub-web-deploy/26-5-deployment-success-v3.png)

    Die **Azure App Service-Aktivität** Fenster zeigt den Fortschritt der Bereitstellung. 

    ![Statusbenachrichtigung im Fenster "Azure App Service-Aktivität"](./media/app-service-api-pub-web-deploy/26-5-deployment-success-v4.png)

    Während dieses Bereitstellungsprozesses versucht Visual Studio automatisch neu starten den *Gateway*. Das Gateway ist eine Web-app, die Verwaltungsfunktionen für alle API-apps in einer Ressourcengruppe verarbeitet, und muss neu gestartet werden, um die Änderungen in einer API-app-API-Definition zu erkennen oder *apiapp.json* Datei. 
 
    Wenn Sie eine API-App mithilfe einer anderen Methode bereitstellen oder wenn Visual Studio das Gateway nicht neu starten kann, müssen Sie das Gateway möglicherweise manuell neu starten. Dies wird in den folgenden Schritten erläutert.

1. Wechseln Sie in Ihrem Browser zu der [Azure Preview-Portal](https://portal.azure.com). 

2. Navigieren Sie zu der **API-app** Blatt für die API-app, die Sie bereitgestellt.

    Informationen zu den **API-app** Blatt und zum Suchen finden Sie unter [Verwalten von API-apps](../articles/app-service-api/app-service-api-manage-in-portal.md).

4. Klicken Sie auf die **Gateway** Link.

3. In der **Gateway** Blatt, klicken Sie auf **Neustart**.

    ![](./media/app-service-api-pub-web-deploy/restartgateway.png)


