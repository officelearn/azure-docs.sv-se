## Einrichten einer Speicherverbindungszeichenfolge

Die Azure-Speicherclientbibliothek für .NET unterstützt die Verwendung einer Speicherverbindungszeichenfolge zum Konfigurieren von Endpunkten und Anmeldeinformationen für den Zugriff auf Speicherdienste. Es wird empfohlen, dass Sie Ihre Speicherverbindungszeichenfolge in einer Konfigurationsdatei ablegen, statt sie fest in Code zu programmieren. Zum Speichern der Verbindungszeichenfolge stehen Ihnen zwei Optionen zur Verfügung:

- Wenn Ihre Anwendung in einem Azure-Clouddienst ausgeführt wird, speichern Sie die Verbindungszeichenfolge mithilfe des Azure-Dienstkonfigurationssystems (`*.csdef`- und `*.cscfg`-Dateien). Finden Sie unter [Erstellen und Bereitstellen eines Clouddiensts](../articles/cloud-services/cloud-services-how-to-create-deploy.md) für Informationen zur Konfiguration des Azure-Cloud-Dienst.
- Wenn Ihre Anwendung auf virtuellen Azure-Computern ausgeführt wird oder Sie .NET-Anwendungen erstellen, die außerhalb von Azure ausgeführt werden, speichern Sie die Verbindungszeichenfolge mithilfe des .NET-Konfigurationssystems (z. B. `web.config`- oder `app.config`-Dateien).

Weiter unten in diesem Leitfaden erfahren Sie, wie Sie die Verbindungszeichenfolge aus Ihrem Code abrufen können.

### Konfigurieren der Verbindungszeichenfolge aus einem Azure-Clouddienst

Die Dienstkonfiguration ist in Azure-Clouddiensten eindeutig und ermöglicht es Ihnen, Konfigurationseinstellungen im Azure-Verwaltungsportal dynamisch zu ändern, ohne die Anwendung erneut bereitzustellen.

So konfigurieren Sie die Verbindungszeichenfolge in der Azure-Dienstkonfiguration:

1.  Im Projektmappen-Explorer von Visual Studio in der **Rollen**
    des Azure-Bereitstellungsprojekts mit der rechten Maustaste auf die
    Webrolle oder workerrolle, und klicken Sie auf **Eigenschaften**.  
    ![Wählen Sie die Eigenschaften für eine Clouddienstrolle in Visual Studio][connection-string1]

2.  Klicken Sie auf die **Einstellungen** Registerkarte, und drücken Sie die **Einstellung hinzufügen** Schaltfläche.  
    ![Clouddiensteinstellung in visual Studio hinzufügen][connection-string2]

    Ein neues **Setting1** Eintrag werden dann in der Einstellungstabelle angezeigt.

3.  In den **Typ** Dropdown-Liste des neuen **Setting1** Eintrag auswählen
    **Verbindungszeichenfolge**.  
    ![Verbindungszeichenfolgentyp festlegen][connection-string3]

4.  Klicken Sie auf die **...** Schaltfläche am rechten Ende der **Setting1** Eintrag.
    Die **Verbindungszeichenfolge für Speicherkonto** Dialogfeld wird geöffnet.

5.  Wählen Sie, ob Sie das Ziel der Speicheremulator (Microsoft möchten
    Azure-Speicher, simuliert auf Ihrem lokalen Computer) oder ein Speicherkonto
    in der Cloud verwendet werden soll. Der Code in diesem Leitfaden kann mit beiden Optionen verwendet
    werden. 

    > [AZURE.NOTE] Sie können den Speicheremulator, um zu vermeiden, jegliche Kosten im Zusammenhang mit Azure Storage abzielen. Wenn jedoch ein Azure-Speicherkonto in der Cloud das Ziel sein soll, sind die Kosten für das Ausführen dieses Lernprogramms vernachlässigbar.

    Wenn Sie ein Speicherkonto in der Cloud als Ziel verwenden, geben Sie den primären Zugriffsschlüssel für dieses Speicherkonto ein. Gewusst wie: Kopieren des Primärschlüssels über das Azure-Verwaltungsportal finden Sie unter [anzeigen, kopieren und erneutes Generieren von speicherzugriffsschlüsseln](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).

    > [AZURE.NOTE] Den Schlüssel Ihres Speicherkontos ähnelt das Stammkennwort für das Speicherkonto. Achten Sie darauf, Ihren Schlüssel zu schützen. Geben Sie ihn nicht an andere Benutzer weiter, und speichern Sie ihn nicht in einer Nur-Text-Datei, auf die andere Benutzer zugreifen können. Generieren Sie Ihren Schlüssel mithilfe des Verwaltungsportals erneut, wenn Sie der Meinung sind, dass er nicht mehr sicher ist.
    
    ![Zielumgebung auswählen][connection-string4]

6.  Ändern Sie den Eintrag **Name** aus **Setting1** in einen benutzerfreundlicheren Namen
    wie **StorageConnectionString**. Sie werden später
    in diesem Leitfaden auf diese Verbindungszeichenfolge verweisen.  
    ![Namen der Verbindungszeichenfolge ändern][connection-string5]
    
### Konfigurieren der Verbindungszeichenfolge in der .NET-Konfiguration

Wenn Sie eine Anwendung schreiben, bei der es sich nicht um einen Azure-Clouddienst handelt (siehe vorheriger Abschnitt), empfiehlt es sich, das .NET-Konfigurationssystem zu verwenden (z. B. `web.config` oder `app.config`). Dies umfasst Azure-Websites oder virtuelle Azure-Computer sowie Anwendungen, die für die Ausführung außerhalb von Azure konzipiert sind. Sie speichern die Verbindungszeichenfolge mit dem `<appSettings>`-Element: Ersetzen Sie `account-name` durch den Namen Ihres Speicherkontos und `account-key` durch den Zugriffsschlüssel des Kontos:

    <configuration>
        <appSettings>
            <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key" />
        </appSettings>
    </configuration>

Die Konfigurationseinstellung in Ihrer Konfigurationsdatei könnte beispielsweise folgendermaßen aussehen:

    <configuration>
        <appSettings>
            <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=nYV0gln9fT7bvY+rxu2iWAEyzPNITGkhM88J8HUoyofpK7C8fHcZc2kIZp6cKgYRUM74lHI84L50Iau1+9hPjB==" />
        </appSettings>
    </configuration>

Sie sind nun bereit für die Gewusst-wie-Aufgaben in diesem Leitfaden.

[connection-string1]: ./media/storage-configure-connection-string-include/connection-string1.png
[connection-string2]: ./media/storage-configure-connection-string-include/connection-string2.png
[connection-string3]: ./media/storage-configure-connection-string-include/connection-string3.png
[connection-string4]: ./media/storage-configure-connection-string-include/connection-string4.png
[connection-string5]: ./media/storage-configure-connection-string-include/connection-string5.png

[Configuring Connection Strings]: http://msdn.microsoft.com/library/azure/ee758697.aspx

