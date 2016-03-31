Nachdem die Datensätze für Ihren Domänennamen weitergegeben wurden, sollten Sie den Browser verwenden können, um zu überprüfen, dass Ihr benutzerdefinierter Domänenname für den Zugriff auf die Web-App in Azure App Service verwendet werden kann.

> [AZURE.NOTE] Es dauert einige Zeit Ihr CNAME über das DNS-System weitergegeben. Mithilfe eines Diensts wie <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> können Sie überprüfen, ob der CNAME verfügbar ist.

Wenn Sie Ihre Web-App noch nicht als Traffic Manager-Endpunkt hinzugefügt haben, müssen Sie dies durchführen, bevor die Namensauflösung funktionieren kann, da der benutzerdefinierte Domänenname an Traffic Manager geleitet wird. Traffic Manager leitet dann zu Ihrer Web-App weiter. Verwenden Sie die Informationen im [Hinzufügen oder Löschen von Endpunkten](../traffic-manager/traffic-manager-endpoints.md) Ihrer Web-app als Endpunkt in Traffic Manager-Profil hinzufügen.

> [AZURE.NOTE] Wenn Ihre Web-app beim Hinzufügen eines Endpunkts nicht aufgeführt ist, stellen Sie sicher, dass er konfiguriert ist **Standard** App Service-Modus. Verwenden Sie **Standard** Modus für Ihre Web-app, um mit Traffic Manager arbeiten zu können.

1. Öffnen Sie in Ihrem Browser die [Azure-Portal](https://portal.azure.com).

2. In der **Web-Apps** Registerkarte, klicken Sie auf den Namen der Web-app, wählen **Einstellungen**, und wählen Sie dann **benutzerdefinierte Domänen und SSL**

    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)

3. In der **benutzerdefinierte Domänen und SSL** Blatt, klicken Sie auf **schalten externe Domänen**.

    ![](./media/custom-dns-web-site/dncmntask-cname-7.png)

4. Verwenden der **DOMÄNENNAMEN** Textfelder ein, und geben den Traffic Manager-Domänennamen (contoso.trafficmanager.net) dieser Web-app zuzuordnen.

    ![](./media/custom-dns-web-site/dncmntask-cname-8.png)

5. Klicken Sie auf **Speichern** um die domänennamenkonfiguration zu speichern.

    Nach Abschluss der Konfiguration wird der benutzerdefinierte Domänenname in Liste der **Domänennamen** Abschnitt der Web-app.

Jetzt sollten Sie den Traffic Manager-Domänennamen (contoso.trafficmanager.net) in Ihren Browser eingeben können und auf diese Weise erfolgreich zu Ihrer Web-App gelangen.


