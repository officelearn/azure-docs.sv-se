## Erstellen eines VNet mithilfe einer Netzwerkkonfigurationsdatei von PowerShell

Azure verwendet eine XML-Datei, um alle für ein Abonnement verfügbaren VNets zu definieren. Sie können diese Datei herunterladen und bearbeiten, um vorhandene VNets zu ändern oder zu löschen und neue zu erstellen. In diesem Dokument erfahren Sie, wie Sie die Datei, die auch als Netzwerkkonfigurationsdatei oder netcgf-Datei bezeichnet wird, herunterladen und bearbeiten können, um ein neues VNet zu erstellen. Überprüfen Sie die [Konfigurationsschema für virtuelle Azure-Netzwerk](https://msdn.microsoft.com/library/azure/jj157100.aspx) Weitere Informationen zu Netzwerk-Konfigurationsdatei.

Führen Sie zum Erstellen eines VNet unter Verwendung einer netcfg-Datei mithilfe von PowerShell die folgenden Schritte aus.

1. Wenn Sie Azure PowerShell noch nie verwendet haben, finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](powershell-install-configure.md) und gehen Sie zum Ende bei Azure anmelden, und wählen Sie Ihr Abonnement.
2. Verwenden Sie in der Azure-PowerShell-Konsole die **Get-AzureVnetConfig** Cmdlet zum Herunterladen von Netzwerk-Konfigurationsdatei durch den folgenden Befehl ausführen. 

        Get-AzureVNetConfig -ExportToFile c:\NetworkConfig.xml

    Erwartete Ausgabe:

        XMLConfiguration                                                                                                     
        ----------------                                                                                                     
        <?xml version="1.0" encoding="utf-8"?>...  

3. Öffnen Sie die Datei, die Sie in Schritt 2 oben mit einer beliebigen XML- oder Text-Editor-Anwendung gespeichert haben, und suchen Sie nach der **<VirtualNetworkSites>** Element. Wenn Sie alle Netzwerke, die bereits erstellt haben, wird jedes Netzwerk als eigene angezeigt **<VirtualNetworkSite>** Element.
4. Um das virtuelle Netzwerk in diesem Szenario beschriebenen zu erstellen, fügen Sie den folgenden XML-Code direkt unter dem **<VirtualNetworkSites>** Element:

        <VirtualNetworkSite name="TestVNet" Location="Central US">
          <AddressSpace>
            <AddressPrefix>192.168.0.0/16</AddressPrefix>
          </AddressSpace>
          <Subnets>
            <Subnet name="FrontEnd">
              <AddressPrefix>192.168.1.0/24</AddressPrefix>
            </Subnet>
            <Subnet name="BackEnd">
              <AddressPrefix>192.168.2.0/24</AddressPrefix>
            </Subnet>
          </Subnets>
        </VirtualNetworkSite>

9.  Speichern Sie die Netzwerkkonfigurationsdatei.
10. Verwenden Sie in der Azure-PowerShell-Konsole die **Set AzureVnetConfig** Cmdlet, um die Netzwerk-Konfigurationsdatei hochladen, indem Sie den folgenden Befehl ausführen. Beachten Sie die Ausgabe unter dem Befehl sollte **erfolgreich** unter **OperationStatus**. Ist dies nicht der Fall, überprüfen Sie die XML-Datei auf Fehler.

        Set-AzureVNetConfig -ConfigurationPath c:\NetworkConfig.xml

    Hier ist die erwartete Ausgabe des obigen Befehls:

        OperationDescription OperationId                          OperationStatus
        -------------------- -----------                          ---------------
        Set-AzureVNetConfig  49579cb9-3f49-07c3-ada2-7abd0e28c4e4 Succeeded 
    
11. Verwenden Sie in der Azure-PowerShell-Konsole die **Get-AzureVnetSite** Cmdlet, um sicherzustellen, dass das neue Netzwerk hinzugefügt wurde, indem Sie den folgenden Befehl ausführen. 

        Get-AzureVNetSite -VNetName TestVNet

    Hier ist die erwartete Ausgabe des obigen Befehls:

        AddressSpacePrefixes : {192.168.0.0/16}
        Location             : Central US
        AffinityGroup        : 
        DnsServers           : {}
        GatewayProfile       : 
        GatewaySites         : 
        Id                   : b953f47b-fad9-4075-8cfe-73ff9c98278f
        InUse                : False
        Label                : 
        Name                 : TestVNet
        State                : Created
        Subnets              : {FrontEnd, BackEnd}
        OperationDescription : Get-AzureVNetSite
        OperationId          : 3f35d533-1f38-09c0-b286-3d07cd0904d8
        OperationStatus      : Succeeded

