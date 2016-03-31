1. Klicken Sie auf **Applikationen** Registerkarte auf der Verzeichnisseite, in dem [klassischen Azure-Portal](https://manage.windowsazure.com/).
  
2. Klicken Sie auf die Registrierung zu Ihrer integrierten Anwendung.

3. Klicken Sie auf **konfigurieren** auf der Anwendungsseite und führen Sie einen Bildlauf nach unten der der **Schlüssel** Abschnitt der Seite. 
4. Klicken Sie auf **1 Jahr** Dauer für einen neuen Schlüssel. Klicken Sie dann auf **Speichern** und das Portal zeigt den neuen Schlüsselwert.
5. Kopieren der **Client-ID** und **Schlüssel** nach dem Speichern angezeigt. Beachten Sie, dass der Schlüsselwert nach dem Speichern nur einmal angezeigt wird. 

    ![](./media/mobile-services-generate-aad-app-registration-access-key-rbac/client-id-and-key.png)

6. Führen Sie einen Bildlauf zum unteren Rand der Konfigurationsseite der integrierten Anwendung und Aktivieren der **Lesen der Verzeichnisdaten** Berechtigung für die Anwendung und auf **Speichern**.

    ![](./media/mobile-services-generate-aad-app-registration-access-key-rbac/app-perms.png)


7. In der [klassischen Azure-Portal](https://manage.windowsazure.com/), wechseln Sie zurück zu Ihrem mobilen Dienst, und klicken Sie auf die **konfigurieren** Registerkarte. Führen Sie einen Bildlauf nach unten, um die **app-Einstellungen** aus, und fügen Sie die folgenden Appeinstellungen und auf **Speichern**. 

    <table border="1">
    <tr>
    <th>Name der App-Einstellung</th><th>Beschreibung</th>
    </tr>
    <tr>
    <td>AAD_CLIENT_ID</td><td>Die Benutzer-ID, die Sie in den vorhergehenden Schritten aus der integrierten App kopiert haben.</td>
    </tr>
    <tr>
    <td>AAD_CLIENT_KEY</td><td>Der App-Schlüssel, den Sie in den vorhergehenden Schritten in der AAD-integrierten App generiert haben.</td>
    </tr>
    <tr>
    <td>AAD_TENANT_DOMAIN</td><td>Ihr AAD-Domänenname. Dieser sollte "mydomain.onmicrosoft.com" entsprechen.</td>
    </tr>
    <tr>
    <td>AAD_GROUP_ID</td><td>Die Gruppen-ID, die Sie im vorhergehenden Abschnitt für die Gruppe "Sales" notiert haben.</td>
    </tr>
    </table><br/>

 
    ![](./media/mobile-services-generate-aad-app-registration-access-key-rbac/aad-app-settings.png)
  

