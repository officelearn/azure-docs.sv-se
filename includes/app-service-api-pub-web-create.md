1. In **Projektmappen-Explorer**, mit der rechten Maustaste auf das Projekt (nicht die Projektmappe), und klicken Sie auf **Veröffentlichen**. 

    ![Menüoption zur Projektveröffentlichung](./media/app-service-api-pub-web-create/20-publish-gesture-v3.png)

2. Klicken Sie auf die **Profil** Registerkarte, und klicken Sie auf **Microsoft Azure API-Apps (Vorschau)**. 

    ![Dialogfeld zur Webveröffentlichung](./media/app-service-api-pub-web-create/21-select-api-apps-for-deployment-v2.png)

3. Klicken Sie auf **Neu** auf eine neue API-App im Azure-Abonnement bereitzustellen.

    ![Dialogfeld zur Auswahl vorhandener API-Dienste](./media/app-service-api-pub-web-create/23-publish-to-apiapps-v3.png)

4. In der **Erstellen einer API-App** Dialogfeld, geben Sie Folgendes ein:

    - Für **API App Name**, geben Sie den Namen, die Sie für dieses Lernprogramm verwenden. 
    - Wenn Sie über mehrere Azure-Abonnements verfügen, wählen Sie dasjenige aus, das Sie verwenden möchten.
    - Für **App Service-Plan**, wählen Sie aus Ihren vorhandenen App Service-Plänen, oder wählen Sie **neuen App Service-Plan erstellen** und geben Sie den Namen eines neuen Plans. 
    - Für **Ressourcengruppe**, wählen Sie eine vorhandene Ressourcengruppe aus, oder wählen Sie **neue Ressourcengruppe erstellen** und geben Sie einen Namen ein. 
    - Für **Zugriffsebene**, Option **für alle Benutzer verfügbar**. Sie können den Zugriff später über das Azure-Vorschauportal einschränken.
    - Für **Region**, wählen Sie eine Region in Ihrer Nähe.  

    ![Dialogfeld zum Konfigurieren von Microsoft Azure Web-Apps](./media/app-service-api-pub-web-create/24-new-api-app-dialog-v3.png)

5. Klicken Sie auf **OK** an die API-App in Ihrem Abonnement zu erstellen. 

    Da dieser Vorgang einige Minuten in Anspruch nehmen kann, zeigt Visual Studio ein Bestätigungsdialogfeld an.  

6. Klicken Sie auf **OK** im Bestätigungsdialogfeld auf. 
 
    Im Rahmen des Bereitstellungsprozesses werden die Ressourcengruppe und die API-App in Ihrem Azure-Abonnement erstellt. Visual Studio zeigt den Fortschritt in der **Azure App Service-Aktivität** Fenster. 

    ![Statusbenachrichtigung über das Fenster "Azure App Service-Aktivität"](./media/app-service-api-pub-web-create/26-provisioning-success-v3.png)

