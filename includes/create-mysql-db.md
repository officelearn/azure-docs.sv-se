Dieses Handbuch zeige Ihnen, wie Sie [ClearDB] zum Erstellen einer MySQL-Datenbank aus der [Azure Store] und eine MySQL-Datenbank als verknüpfte Ressource erstellen, bei der Erstellung einer [Azure-Website][waws] . [ClearDB] ist ein fehlertolerante Datenbank-as-a-Service-Anbieter, mit dem Sie ausführen und Verwalten von MySQL-Datenbanken in Azure-Datencentern und von jeder Anwendung verknüpfen.  

> [AZURE.NOTE] Wenn Sie eine MySQL-Datenbank als Teil der Erstellungsprozess der Website erstellen, können Sie nur eine kostenlose Datenbank erstellen. Wenn Sie eine MySQL-Datenbank über den Azure Store erstellen, können Sie eine kostenlose Datenbank erstellen oder unter bezahlten Optionen wählen.

## Erstellen einer MySQL-Datenbank über den Azure Store

Zum Erstellen einer MySQL-Datenbank aus der [Azure Store], gehen Sie folgendermaßen vor:

1. Melden Sie sich auf die [Azure-Verwaltungsportal][portal].
2. Klicken Sie auf **+ NEW** am unteren Rand der Seite, und wählen Sie dann **MARKETPLACE**.

    ![Add-On im Store auswählen](./media/create-mysql-db/select-store.png)

3. Wählen Sie **ClearDB MySQL-Datenbank**, klicken Sie dann auf den Pfeil am unteren Rand des Frames.

    ![ClearDB MySQL-Datenbank auswählen](./media/create-mysql-db/select-cleardb-mysql.png)

4. Wählen Sie einen Plan, geben Sie einen Datenbanknamen ein, wählen Sie eine Region aus, und klicken Sie unten im Fenster auf den Pfeil.

    ![MySQL-Datenbank im Store kaufen](./media/create-mysql-db/purchase-mysql.png)

5. Klicken Sie auf das Häkchen, um den Kauf abzuschließen.

    ![Kauf prüfen und abschließen](./media/create-mysql-db/complete-mysql-purchase.png)

6. Nachdem die Datenbank erstellt wurde, können Sie ihn aus verwalten die **ADD-ONS** Registerkarte im Verwaltungsportal.

    ![MySQL-Datenbank im Azure-Portal verwalten](./media/create-mysql-db/manage-mysql-add-on.png)

7. Sie können die Datenbankverbindungsinformationen abrufen, indem Sie auf **VERBINDUNGSINFORMATIONEN** am unteren Rand der Seite (siehe oben).

    ![MySQL-Verbindungsinformationen](./media/create-mysql-db/mysql-conn-info.png) 


## Erstellen einer MySQL-Datenbank als verknüpfte Ressource für eine Azure-Website

Eine MySQL-Datenbank als verknüpfte Ressource erstellen, bei der Erstellung einer [Azure-Website][waws], gehen Sie folgendermaßen vor:

1. Melden Sie sich auf die [Azure-Verwaltungsportal][portal].
2. Klicken Sie auf **+ NEW** am unteren Rand der Seite, und wählen Sie dann **COMPUTE**, **WEBSITE**, und **CREATE WITH DATABASE**.

    ![Website mit Datenbank erstellen](./media/create-mysql-db/custom_create.png)

3. Bieten eine **URL** für die Website auswählen die **REGION** für Ihre Website, und wählen Sie **Erstellen Sie eine neue MySQL-Datenbank** aus der **Datenbank** Dropdown-Liste. Optional können Sie den Standardnamen für die Verbindungszeichenfolge ersetzen. Klicken Sie auf den Pfeil unten auf der Seite.

    ![Websitedetails angeben](./media/create-mysql-db/provide-website-details.png) 

4. Bereitstellen einer Datenbank **Namen**, wählen die **REGION** für die Datenbank (Dies sollte die Region für Ihre Website übereinstimmen), stimmen Sie den ClearDB Bedingungen, und klicken Sie auf das Häkchen am unteren Rand des Frames.

    ![MySQL-Details angeben](./media/create-mysql-db/provide-mysql-details.png)

5. Klicken Sie nach Erstellung der Website auf deren Namen, um zum Dashboard Ihrer Website zu gelangen.

    ![Zum Website-Dashboard navigieren](./media/create-mysql-db/go-to-website-dashboard.png)

6. Klicken Sie auf **konfigurieren**.

    ![Zur Registerkarte "Konfigurieren" navigieren](./media/create-mysql-db/go-to-configure-tab.png)

7. Führen Sie einen Bildlauf nach unten, um die **Verbindungszeichenfolgen** hinzu, und klicken Sie auf **Verbindungszeichenfolgen anzeigen**. 

    ![Verbindungszeichenfolge anzeigen](./media/create-mysql-db/show-conn-string.png)

8. Kopieren Sie Verbindungszeichenfolge zur Verwendung in Ihrer Anwendung.

    ![Verbindungszeichenfolge anzeigen](./media/create-mysql-db/shown-conn-string.png)

> [AZURE.NOTE] Verbindungszeichenfolgen, die Ihre Website-Anwendung über den Namen der Zeichenfolge zugegriffen werden. In .NET-Anwendungen stehen Verbindungszeichenfolgen zur Verfügung, in dem **ConnectionStrings** Objekt. In anderen Programmiersprachen stehen Verbindungszeichenfolgen als Umgebungsvariablen zur Verfügung. Weitere Informationen finden Sie unter [Konfigurieren von Websites][configure].

[ClearDB]: http://www.cleardb.com/
[waws]: /documentation/services/web-sites/
[Azure Store]: ../articles/store.md
[portal]: http://manage.windowsazure.com
[configure]: ../article/app-service-web/web-sites-configure.md


