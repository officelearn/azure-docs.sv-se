Nachdem die Datensätze für Ihren Domänennamen weitergegeben wurden, müssen Sie sie Ihrer Web-App zuweisen. Verwenden Sie die folgenden Schritte, um die Domänennamen mit dem Webbrowser zu aktivieren.

> [AZURE.NOTE] Es dauert einige Zeit für CNAME-Datensätze, die in den vorherigen Schritten über das DNS-System verteilt wurde erstellt. Sie können den Domänennamen Ihrer Web-App erst dann hinzufügen, nachdem CNAME weitergegeben wurde. Wenn Sie einen A-Datensatz verwenden, kann nicht den A-Datensatz-Domänennamen zur Web-app bis zum Hinzufügen der **Awverify** im vorherigen Schritt erstellte CNAME-Datensatz weitergegeben wurde.
>
> Mithilfe eines Diensts wie <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> können Sie überprüfen, ob der CNAME verfügbar ist.

1. Öffnen Sie in Ihrem Browser die [Azure-Verwaltungsportal](https://portal.azure.com).

2. In der **Web-Apps** Registerkarte, klicken Sie auf den Namen der Web-app, wählen **Einstellungen**, und wählen Sie dann **benutzerdefinierte Domänen und SSL**

    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)

3. In der **benutzerdefinierte Domänen und SSL** Blatt, klicken Sie auf **schalten externe Domänen**.

    ![](./media/custom-dns-web-site/dncmntask-cname-7.png)

4. Verwenden der **DOMÄNENNAMEN** Textfelder ein, und geben den Domänennamen mit dieser Web-app zuordnen.

    ![](./media/custom-dns-web-site/dncmntask-cname-8.png)

5. Klicken Sie auf **Speichern** um die domänennamenkonfiguration zu speichern.

    Nach Abschluss der Konfiguration wird der benutzerdefinierte Domänenname in Liste der **Domänennamen** Abschnitt der Web-app.

Jetzt sollten Sie den benutzerdefinierten Domänennamen in Ihren Browser eingeben können und und auf diese Weise erfolgreich zu Ihrer Web-App gelangen.


