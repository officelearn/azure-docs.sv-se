## Erstellen von VNets mithilfe der Azure-Befehlszeilenschnittstelle

Sie können die Azure-Befehlszeilenschnittstelle verwenden, um Ihre Azure-Ressourcen über die Eingabeaufforderung eines beliebigen Computers mit Windows, Linux oder OSX zu verwalten. Führen Sie zum Erstellen eines VNet mithilfe der Azure-Befehlszeilenschnittstelle die folgenden Schritte aus.

1. Wenn Sie die Azure-Befehlszeilenschnittstelle noch nie verwendet haben, finden Sie unter [Installieren und Konfigurieren der Azure-Befehlszeilenschnittstelle](xplat-cli-install.md) und führen Sie die Schritte bis zu dem Zeitpunkt, in dem Sie Ihr Azure-Konto und Ihr Abonnement auswählen.
2. Führen Sie die **Azure Config Mode** Befehl aus, um die Ressourcen-Manager-Modus wechseln, wie unten dargestellt.

        azure config mode arm

    Hier ist die erwartete Ausgabe des obigen Befehls:

        info:    New mode is arm

3. Führen Sie bei Bedarf die **Azure Gruppe erstellen** eine neue Ressourcengruppe erstellen, wie unten dargestellt. Beachten Sie die Ausgabe des Befehls. In der nach der Ausgabe angezeigten Liste werden die verwendeten Parameter erläutert. Weitere Informationen zu Ressourcengruppen finden Sie auf [Übersicht über Azure Resource Manager](resource-group-overview.md/#resource-groups).

        azure group create -n TestRG -l centralus

    Hier ist die erwartete Ausgabe des obigen Befehls:

        info:    Executing command group create
        + Getting resource group TestRG
        + Creating resource group TestRG
        info:    Created resource group TestRG
        data:    Id:                  /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG
        data:    Name:                TestRG
        data:    Location:            centralus
        data:    Provisioning State:  Succeeded
        data:    Tags: null
        data:
        info:    group create command OK

    - **-n (oder--Name)**. Name für die neue Ressourcengruppe. In diesem Szenario *TestRG*.
    - **-l (oder--Location)**. Azure-Region, in der die neue Ressourcengruppe erstellt wird. In diesem Szenario *Centralus*.

4. Führen Sie die **Azure-Netzwerk Vnet erstellen** Befehl zum Erstellen von einem VNet und einem Subnetz, wie unten dargestellt. 

        azure network vnet create -g TestRG -n TestVNet -a 192.168.0.0/16 -l centralus

    Hier ist die erwartete Ausgabe des obigen Befehls:

        info:    Executing command network vnet create
        + Looking up virtual network "TestVNet"
        + Creating virtual network "TestVNet"
        + Loading virtual network state
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet2
        data:    Name                            : TestVNet
        data:    Type                            : Microsoft.Network/virtualNetworks
        data:    Location                        : centralus
        data:    ProvisioningState               : Succeeded
        data:    Address prefixes:
        data:      192.168.0.0/16
        info:    network vnet create command OK

    - **-g (oder--Resource-Group)**. Name der Ressourcengruppe, in der das neue VNet erstellt wird. In diesem Szenario *TestRG*.
    - **-n (oder--Name)**. Name des zu erstellenden VNet. In diesem Szenario *TestVNet*
    - **– ein (oder---Adresspräfixe)**. Liste der für den VNet-Adressraum verwendeten CIDR-Blöcke. In diesem Szenario *192.168.0.0/16*
    - **-l (oder--Location)**. Azure-Region, in der das VNet erstellt wird. In diesem Szenario *Centralus*.

5. Führen Sie die **Netzwerk in Azure-Vnet-Subnetz erstellen** Befehl aus, um ein Subnetz zu erstellen, wie unten dargestellt. Beachten Sie die Ausgabe des Befehls. In der nach der Ausgabe angezeigten Liste werden die verwendeten Parameter erläutert.

        azure network vnet subnet create -g TestRG -e TestVNet -n FrontEnd -a 192.168.1.0/24

    Hier ist die erwartete Ausgabe des obigen Befehls:

        info:    Executing command network vnet subnet create
        + Looking up the subnet "FrontEnd"
        + Creating subnet "FrontEnd"
        + Looking up the subnet "FrontEnd"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
        data:    Type                            : Microsoft.Network/virtualNetworks/subnets
        data:    ProvisioningState               : Succeeded
        data:    Name                            : FrontEnd
        data:    Address prefix                  : 192.168.1.0/24
        data:
        info:    network vnet subnet create command OK

    - **-e (oder--Vnet-Name**. Name des VNet, in dem das Subnetz erstellt wird. In diesem Szenario *TestVNet*.
    - **-n (oder--Name)**. Name des neuen Subnetzes. In diesem Szenario *Front-End-*.
    - **– ein (oder---Adresspräfix)**. Subnetz-CIDR-Block. Vier unser Szenario *192.168.1.0/24*.

6. Wiederholen Sie bei Bedarf Schritt 5, um weitere Subnetze zu erstellen. In diesem Szenario führen Sie den folgenden Befehl zum Erstellen der *Back-End-* Subnetz.

        azure network vnet subnet create -g TestRG -e TestVNet -n BackEnd -a 192.168.2.0/24

4. Führen Sie die **Azure Network Vnet Show** Befehl aus, um die Eigenschaften der neuen Vnet anzeigen, wie unten dargestellt.

        azure network vnet show -g TestRG -n TestVNet

    Hier ist die erwartete Ausgabe des obigen Befehls:

        info:    Executing command network vnet show
        + Looking up virtual network "TestVNet"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        data:    Name                            : TestVNet
        data:    Type                            : Microsoft.Network/virtualNetworks
        data:    Location                        : centralus
        data:    ProvisioningState               : Succeeded
        data:    Address prefixes:
        data:      192.168.0.0/16
        data:    Subnets:
        data:      Name                          : FrontEnd
        data:      Address prefix                : 192.168.1.0/24
        data:
        data:      Name                          : BackEnd
        data:      Address prefix                : 192.168.2.0/24
        data:
        info:    network vnet show command OK


