Sie müssen einen Endpunkt mit Lastenausgleich für jeden virtuellen Computer mit einem Azure-Replikat erstellen. Wenn Sie Replikate in mehreren Regionen haben, muss sich jedes Replikat für diese Region im selben Clouddienst im selben VNet befinden. Das Erstellen von Verfügbarkeitsgruppenreplikaten, die sich über mehrere Azure-Regionen erstrecken, erfordert die Konfiguration mehrerer VNets. Weitere Informationen zum Konfigurieren von VNet-übergreifenden Verbindungen finden Sie unter  [Konfigurieren von VNet-zu-VNet-Konnektivität](../articles/vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

1. Navigieren Sie im Azure-Portal zu jedem virtuellen Computer, der ein Replikat hostet, und zeigen Sie die Details an.

1. Klicken Sie auf die **Endpunkte** Registerkarte für jeden virtuellen Computer.

1. Überprüfen Sie, ob die **Namen** und **öffentlicher Port** des Listeners Endpunkt zu verwenden ist nicht bereits verwendet. Im folgenden Beispiel ist der Name "MyEndpoint" und der Port "1433".

1. Auf den lokalen Client herunterladen und installieren [aktuellen PowerShell-Modul](http://azure.microsoft.com/downloads/).

1. Starten Sie **Azure PowerShell**. Eine neue PowerShell-Sitzung wird geöffnet, und die Azure-Verwaltungsmodule werden geladen.

1. Führen Sie **Get-AzurePublishSettingsFile**. Dieses Cmdlet leitet Sie zu einem Browser weiter, damit Sie eine Datei mit Veröffentlichungseinstellungen in ein lokales Verzeichnis herunterladen können. Möglicherweise werden Sie aufgefordert, die Anmeldeinformationen für Ihr Azure-Abonnement einzugeben.

1. Führen Sie die **Import-AzurePublishSettingsFile** Befehl durch den Pfad der Datei mit den veröffentlichungseinstellungen, die Sie heruntergeladen haben:

        Import-AzurePublishSettingsFile -PublishSettingsFile <PublishSettingsFilePath>

    Nachdem die Datei mit Veröffentlichungseinstellungen importiert wurde, können Sie Ihr Azure-Abonnement in der PowerShell-Sitzung verwalten.

