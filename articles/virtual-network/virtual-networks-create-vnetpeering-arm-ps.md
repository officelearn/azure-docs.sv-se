<properties
   pageTitle="Skapa VNet-peering med Powershell-cmdletar | Microsoft Azure"
   description="Lär dig hur du skapar ett virtuellt nätverk med Azure Portal i Resource Manager."
   services="virtual-network"
   documentationCenter=""
   authors="NarayanAnnamalai"
   manager="jefco"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/14/2016"
   ms.author="narayanannamalai; annahar"/>


# Skapa VNet-peering med Powershell-cmdletar

[AZURE.INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnetpeering-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnetpeering-intro-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-basic-include](../../includes/virtual-networks-create-vnetpeering-scenario-basic-include.md)]

Följ stegen nedan för att skapa en VNet-peering med PowerShell:

1. Om du aldrig använt Azure PowerShell tidigare, se [Installera och konfigurera Azure PowerShell](../powershell-install-configure.md) och följ instruktionerna till slutet för att logga in på Azure och välja din prenumeration.

        > [AZURE.NOTE] PowerShell-cmdleten för att hantera VNet-peering medföljer [Azure PowerShell 1.6.](http://www.powershellgallery.com/packages/Azure/1.6.0)

2. Läsa virtuella nätverksobjekt:

        $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet1
        $vnet2 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet2

3. För att etablera VNet.peering, måste du skapa två länkar, en för varje riktning. Följ nedanstående steg för att först skapa en VNet-peeringlänk för VNet1 till VNet2:

        Add-AzureRmVirtualNetworkPeering -name LinkToVNet2 -VirtualNetwork $vnet1 -RemoteVirtualNetworkId $vnet2.id

    Utdata visar:

        Name            : LinkToVNet2
        Id: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName   : vnet101
        VirtualNetworkName  : vnet1
        ProvisioningState       : Succeeded
        RemoteVirtualNetwork    : {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2"
                                        }
        AllowVirtualNetworkAccess   : True
        AllowForwardedTraffic   : False
        AllowGatewayTransit : False
        UseRemoteGateways   : False
        RemoteGateways      : null
        RemoteVirtualNetworkAddressSpace : null

4. Det här steget skapar en VNet-peeringlänk för VNet2 till VNet1:

        Add-AzureRmVirtualNetworkPeering -name LinkToVNet1 -VirtualNetwork $vnet2 -RemoteVirtualNetworkId $vnet1.id

    Utdata visar:

        Name            : LinkToVNet1
        Id              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2/virtualNetworkPeerings/LinkToVNet1
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName   : vnet101
        VirtualNetworkName  : vnet2
        ProvisioningState       : Succeeded
        RemoteVirtualNetwork    : {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1"
                                        }
        AllowVirtualNetworkAccess   : True
        AllowForwardedTraffic   : False
        AllowGatewayTransit : False
        UseRemoteGateways   : False
        RemoteGateways      : null
        RemoteVirtualNetworkAddressSpace : null

5. När VNet-peeringlänken har skapats kan du se länkstatusen nedan:

        Get-AzureRmVirtualNetworkPeering -VirtualNetworkName vnet1 -ResourceGroupName vnet101 -Name linktovnet2

    Utdata visar:

        Name            : LinkToVNet2
        Id              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName   : vnet101
        VirtualNetworkName  : vnet1
        ProvisioningState       : Succeeded
        RemoteVirtualNetwork    : {
                                             "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2"
                                        }
        AllowVirtualNetworkAccess   : True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null

    Det finns några konfigurerbara egenskaper för VNet-peering:

  	|Alternativ|Beskrivning|Standard|
  	|:-----|:----------|:------|
  	|AllowVirtualNetworkAccess|Om adressrymden för peer-VNet som ska tas med som en del av taggen Virtual_network|Ja|
  	|AllowForwardedTraffic|Tillåter att trafik som inte kommer från ett peer-kopplat VNet godkänns eller utelämnas|Nej|
  	|AllowGatewayTransit|Tillåter att ett peer VNet använder din VNet-gateway|Nej|
  	|UseRemoteGateways|Använd din peers VNet-gateway. En gateway måste konfigureras för det peer-kopplade virtuella nätverket och AllowGatewayTransit måste väljas. Du kan inte använda det här alternativet om du har en konfigurerad gateway|Nej|

    Varje länk i VNet-peering har den uppsättning egenskaper som visas ovan. Du kan till exempel ställa in AllowVirtualNetworkAccess till True för VNet-peeringlänken VNet1 till VNet2 och ställa in den till False för VNet-peeringlänken i den andra riktningen.

        $LinktoVNet2 = Get-AzureRmVirtualNetworkPeering -VirtualNetworkName vnet1 -ResourceGroupName vnet101 -Name LinkToVNet2
        $LinktoVNet2.AllowForwardedTraffic = $true
        Set-AzureRmVirtualNetworkPeering -VirtualNetworkPeering $LinktoVNet2

    Du kan köra Get-AzureRmVirtualNetworkPeering för att dubbelkontrollera egenskapsvärdet efter ändringen.  Från utdatan kan du se AllowForwardedTraffic-förändringar som är inställda på Sant efter att ovanstående cmdlets har körts.

        Name            : LinkToVNet2
        Id          : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName   : vnet101
        VirtualNetworkName  : vnet1
        ProvisioningState   : Succeeded
        RemoteVirtualNetwork    : {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2"
                                        }
        AllowVirtualNetworkAccess   : True
        AllowForwardedTraffic       : True
        AllowGatewayTransit     : False
        UseRemoteGateways       : False
        RemoteGateways      : null
        RemoteVirtualNetworkAddressSpace : null

    När peering har upprättats i det här scenariot, bör du kunna initiera anslutningarna från en valfri virtuell dator till en annan valfri virtuell dator för båda VNets. Som standard är AllowVirtualNetworkAccess inställt på True och VNet-peering etablerar rätt ACL:er för att tillåta kommunikation mellan VNets. Du kan fortfarande tillämpa gruppregler för nätverkssäkerhet för att blockera anslutningen mellan specifika undernät eller virtuella datorer för att få noggrann kontroll över åtkomsten mellan två virtuella nätverk.  Mer information om hur du skapar gruppregler för nätverkssäkerhet finns i denna [artikel](virtual-networks-create-nsg-arm-ps.md).

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-crosssub-include](../../includes/virtual-networks-create-vnetpeering-scenario-crosssub-include.md)]

Följ stegen nedan för att skapa VNet-peering inom prenumerationerna med PowerShell:

1. Logga in till Azure med privilegierad användare A:s konto för prenumeration A och kör följande cmdlet:

        New-AzureRmRoleAssignment -SignInName <UserB ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-A-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetworks/VNet5

    Detta är inget krav, peering kan upprättas även om användarna skickar peering-begäranden individuellt för sina respektive virtuella nätverk, så länge begärandena matchar. Om du lägger en privilegierad användare av det andra virtuella nätverket som användare i det lokala virtuella nätverket blir det lättare att konfigurera inställningen.

2. Logga in till Azure med privilegierad användare B:s konto för prenumeration B och kör följande cmdlet:

        New-AzureRmRoleAssignment -SignInName <UserA ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-B-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetworks/VNet3

3. I användare A:s inloggningssession kör du cmdleten nedan:

        $vnet3 = Get-AzureRmVirtualNetwork -ResourceGroupName hr-vnets -Name vnet3

        Add-AzureRmVirtualNetworkPeering -name LinkToVNet5 -VirtualNetwork $vnet3 -RemoteVirtualNetworkId "/subscriptions/<Subscriptoin-B-Id>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/VNet5" -BlockVirtualNetworkAccess

4. I användare B:s inloggningssession kör du cmdleten nedan:

        $vnet5 = Get-AzureRmVirtualNetwork -ResourceGroupName vendor-vnets -Name vnet5

        Add-AzureRmVirtualNetworkPeering -name LinkToVNet3 -VirtualNetwork $vnet5 -RemoteVirtualNetworkId "/subscriptions/<Subscriptoin-A-Id>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/VNet3" -BlockVirtualNetworkAccess

5. När peering har upprättats ska alla virtuella datorer i VNet3 kunna kommunicera med valfri virtuell dator i VNet5.

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-transit-include](../../includes/virtual-networks-create-vnetpeering-scenario-transit-include.md)]

1. I det här scenariot kan du köra de PowerShell-cmdletar som visas nedan för att upprätta VNet-peeringen.  Du måste ange egenskapen AllowForwardedTraffic till True och länka VNET1 till HubVnet, vilket tillåter inkommande trafik från utanför peering-VNet-adressutrymmet.

        $hubVNet = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name HubVNet
        $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet1

        Add-AzureRmVirtualNetworkPeering -name LinkToHub -VirtualNetwork $vnet1 -RemoteVirtualNetworkId $HubVNet.id -AllowForwardedTraffic

        Add-AzureRmVirtualNetworkPeering -name LinkToVNet1 -VirtualNetwork $HubVNet -RemoteVirtualNetworkId $vnet1.id

2. När peering har upprättats kan du referera till denna [artikel](virtual-network-create-udr-arm-ps.md) och definiera en användardefinierad väg (UDR) för att omdirigera VNet1-trafik via en virtuell installation för att använda dess funktioner. När du anger ”nästa hopp”-adressen i vägen kan du ange den till IP-adressen för en virtuell enhet i det virtuella peer-nätverket för HubVNet. Nedan ges ett exempel:

        $route = New-AzureRmRouteConfig -Name TestNVA -AddressPrefix 10.3.0.0/16 -NextHopType VirtualAppliance -NextHopIpAddress 192.0.1.5

        $routeTable = New-AzureRmRouteTable -ResourceGroupName VNet101 -Location brazilsouth -Name TestRT -Route $route

        $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName VNet101 -Name VNet1

        Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet1 -Name subnet-1 -AddressPrefix 10.1.1.0/24 -RouteTable $routeTable

        Set-AzureRmVirtualNetwork -VirtualNetwork $vnet1

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-asmtoarm-include](../../includes/virtual-networks-create-vnetpeering-scenario-asmtoarm-include.md)]

Följ stegen nedan om du vill skapa en VNet-peering mellan ett klassiskt virtuellt nätverk och ett Azure Resource Manager virtuellt nätverk i PowerShell:

1. Läs virtuellt nätverksobjekt för **VNET1**, Azure Resource Manager virtuellt nätverk som följer:      $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet1

2. Endast en länk krävs för att etablera VNet-peering i detta scenario, nämligen en länk från **VNET1** till **VNET2**. Det här steget kräver att man vet sitt klassiska VNet:s resurs-ID. Resursgruppens ID-format ser ut så här: /subscriptions/SubscriptionID/resourceGroups/ResourceGroupName/providers/Microsoft.ClassicNetwork/virtualNetworks/VirtualNetworkName

    Se till att ersätta SubscriptionID, ResourceGroupName och VirtualNetworkName med rätt namn.

    Detta kan åstadkommas med följande:

        Add-AzureRmVirtualNetworkPeering -name LinkToVNet2 -VirtualNetwork $vnet1 -RemoteVirtualNetworkId /subscriptions/xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.ClassicNetwork/virtualNetworks/VNET2

3. När VNet-peeringlänken har skapats kan du se länkstatus enligt utdatan nedan:

        Name                             : LinkToVNet2
        Id                               : /subscriptions/xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.Network/virtualNetworks/VNET1/virtualNetworkPeerings/LinkToVNet2
        Etag                             : W/"acecbd0f-766c-46be-aa7e-d03e41c46b16"
        ResourceGroupName                : MyResourceGroup
        VirtualNetworkName               : VNET1
        PeeringState                     : Connected
        ProvisioningState                : Succeeded
        RemoteVirtualNetwork             : {
                                         "Id": "/subscriptions/xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.ClassicNetwork/virtualNetworks/VNET2"
                                       }
        AllowVirtualNetworkAccess        : True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null

## Ta bort VNet-peering

1.  För att ta bort VNet-peering, måste du köra följande cmdlet:

        Remove-AzureRmVirtualNetworkPeering  

        remove both links, as shown below:

        Remove-AzureRmVirtualNetworkPeering -ResourceGroupName vnet101 -VirtualNetworkName vnet1 -Name linktovnet2
        Remove-AzureRmVirtualNetworkPeering -ResourceGroupName vnet101 -VirtualNetworkName vnet1 -Name linktovnet2

2. När du tar bort en länk i en VNET-peering ändras peer-länkens status till frånkopplad. I det här tillståndet kan du inte återskapa länken förrän peer-länkens status ändras till Initierad. Vi rekommenderar att du tar bort båda länkarna innan du återskapar VNET-peeringen.



<!--HONumber=Sep16_HO3-->


