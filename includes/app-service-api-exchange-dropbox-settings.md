4. In einem anderen Browserfenster oder Registerkarte, wechseln Sie zu der [Azure Preview-Portal](https://portal.azure.com).

3. Wechseln Sie zu der **API-App** Blatt für Ihren Dropbox-Connector. (Wenn Sie noch die **Ressourcengruppe** Blatt, klicken Sie einfach auf den Dropbox-Connector im Diagramm.)

4. Klicken Sie auf **Einstellungen**, und klicken Sie in der **Einstellungen** Blatt auf **Authentifizierung**.

    ![Klicken Sie auf "Einstellungen"](./media/app-service-api-exchange-dropbox-settings/clicksettings.png)

    ![Klicken Sie auf "Authentifizierung"](./media/app-service-api-exchange-dropbox-settings/clickauth.png)

5. Geben Sie im Blatt für die Authentifizierung die Client-ID und den geheimen Clientschlüssel von der Dropbox-Website, und klicken Sie dann auf **Speichern**.

    ![Geben Sie Einstellungen ein, und klicken Sie auf "Speichern"](./media/app-service-api-exchange-dropbox-settings/authblade.png)

3. Kopieren der **Redirect URI** (graues Feld oberhalb von Client-ID und Clientschlüssel), und fügen Sie den Wert auf der Seite, die Sie im vorherigen Schritt geöffnet gelassen haben. 

    Der Umleitungs-URI folgt diesem Muster:

        [gatewayurl]/api/consent/redirect/[connectorname]

    Beispiel:

        https://dropboxrgaeb4ae60b7.azurewebsites.net/api/consent/redirect/DropboxConnector

    ![Abrufen des Umleitungs-URI](./media/app-service-api-exchange-dropbox-settings/redirecturi.png)

    ![Erstellen einer Dropbox-App](./media/app-service-api-exchange-dropbox-settings/dbappsettings2.png)

