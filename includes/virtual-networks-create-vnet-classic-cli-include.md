## Erstellen eines klassischen VNet über die Azure-Befehlszeilenschnittstelle

Sie können die Azure-Befehlszeilenschnittstelle verwenden, um Ihre Azure-Ressourcen über die Eingabeaufforderung eines beliebigen Computers mit Windows, Linux oder OSX zu verwalten. Führen Sie zum Erstellen eines VNet mithilfe der Azure-Befehlszeilenschnittstelle die folgenden Schritte aus.

1. Wenn Sie Azure-Befehlszeilenschnittstelle noch nie verwendet haben, finden Sie unter [Installieren und Konfigurieren der Azure-Befehlszeilenschnittstelle](xplat-cli-install.md) und führen Sie die Schritte bis zu dem Zeitpunkt, in dem Sie Ihr Azure-Konto und Ihr Abonnement auswählen.
2. Führen Sie die **Azure-Netzwerk Vnet erstellen** Befehl zum Erstellen von einem VNet und einem Subnetz, wie unten dargestellt. In der nach der Ausgabe angezeigten Liste werden die verwendeten Parameter erläutert.

            azure network vnet create --vnet TestVNet -e 192.168.0.0 -i 16 -n FrontEnd -p 192.168.1.0 -r 24 -l "Central US"
    
    Erwartete Ausgabe:

            info:    Executing command network vnet create
            + Looking up network configuration
            + Looking up locations
            + Setting network configuration
            info:    network vnet create command OK

    - **--Vnet**. Name des zu erstellenden VNet. In diesem Szenario *TestVNet*
    - **-e (oder--Adressraum)**. VNet-Adressraum. In diesem Szenario *192.168.0.0*
    - **-i (oder - Cidr)**. Netzwerkmaske im CIDR-Format. In diesem Szenario *16*.
    - **-n (oder--Subnet-Name**). Name des ersten Subnetzes. In diesem Szenario *Front-End-*.
    - **-p (oder--Subnet-Start-Ip)**. IP-Startadresse für das Subnetz oder Subnetzadressraum. In diesem Szenario *192.168.1.0*.
    - **-R (oder--Subnet-Cidr)**. Netzwerkmaske im CIDR-Format für das Subnetz. In diesem Szenario *24*.
    - **-l (oder--Location)**. Azure-Region, in der das VNet erstellt wird. In diesem Szenario *USA*.

3. Führen Sie die **Netzwerk in Azure-Vnet-Subnetz erstellen** Befehl aus, um ein Subnetz zu erstellen, wie unten dargestellt. In der nach der Ausgabe angezeigten Liste werden die verwendeten Parameter erläutert.

            azure network vnet subnet create -t TestVNet -n BackEnd -a 192.168.2.0/24
    
    Hier ist die erwartete Ausgabe des obigen Befehls:

            info:    Executing command network vnet subnet create
            + Looking up network configuration
            + Creating subnet "BackEnd"
            + Setting network configuration
            + Looking up the subnet "BackEnd"
            + Looking up network configuration
            data:    Name                            : BackEnd
            data:    Address prefix                  : 192.168.2.0/24
            info:    network vnet subnet create command OK

    - **-t (oder--Vnet-Name**. Name des VNet, in dem das Subnetz erstellt wird. In diesem Szenario *TestVNet*.
    - **-n (oder--Name)**. Name des neuen Subnetzes. In diesem Szenario *Back-End-*.
    - **– ein (oder---Adresspräfix)**. Subnetz-CIDR-Block. Vier unser Szenario *192.168.2.0/24*.

4. Führen Sie die **Azure Network Vnet Show** Befehl aus, um die Eigenschaften der neuen Vnet anzeigen, wie unten dargestellt.

            azure network vnet show

    Hier ist die erwartete Ausgabe des obigen Befehls:

            info:    Executing command network vnet show
            Virtual network name: TestVNet
            + Looking up the virtual network sites
            data:    Name                            : TestVNet
            data:    Location                        : Central US
            data:    State                           : Created
            data:    Address space                   : 192.168.0.0/16
            data:    Subnets:
            data:      Name                          : FrontEnd
            data:      Address prefix                : 192.168.1.0/24
            data:
            data:      Name                          : BackEnd
            data:      Address prefix                : 192.168.2.0/24
            data:
            info:    network vnet show command OK


