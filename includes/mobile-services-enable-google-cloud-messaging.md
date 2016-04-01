
1. Navigieren Sie zu der [Google Cloud Console](https://console.developers.google.com/project), melden Sie sich mit Ihrem Google-Anmeldeinformationen. 
 
2. Klicken Sie auf **Create Project**, geben Sie einen Projektnamen ein, und klicken Sie dann **Erstellen**. Falls angefordert, führen Sie die SMS-Verifizierung aus, und klicken Sie auf **Erstellen** erneut.

    ![](./media/mobile-services-enable-google-cloud-messaging/mobile-services-google-new-project.png)   

     Geben Sie Ihre neue **Projektname** und klicken Sie auf **Create Project**.

3. Notieren Sie sich die Projektnummer, die in der **Projekte** Abschnitt. Sie müssen diesen Wert festlegen die *PROJECT_ID* in den Client in der Variablen.

4. Klicken Sie auf das Projektdashboard auf **Google-APIs verwenden** > **Cloud Messaging für Android**, klicken Sie auf der nächsten Seite **Enable API**. 

5. Klicken Sie in der API-Manager auf **Anmeldeinformationen** > **Anmeldeinformationen hinzufügen** > **API-Schlüssel**. 

    ![](./media/mobile-services-enable-google-cloud-messaging/mobile-services-google-create-server-key.png)

6. In **erstellt einen neuen Schlüssel**, klicken Sie auf **Serverschlüssel**, geben Sie einen Namen für den Schlüssel, und klicken Sie dann **Erstellen**.

7. Notieren Sie sich die **API-Schlüssel** Wert.

    Mit diesem API-Schlüsselwert geben Sie Azure die Möglichkeit, sich bei GCM zu authentifizieren und Pushbenachrichtigungen im Auftrag Ihrer App zu verschicken.



