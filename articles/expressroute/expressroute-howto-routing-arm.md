---
title: 'Så här konfigurerar du routning (peering) för en ExpressRoute-krets: Resource Manager: PowerShell: Azure | Microsoft Docs'
description: Den här artikeln vägleder dig genom stegen för att skapa och etablera privat, offentlig och Microsoft-peering av en ExpressRoute-krets. I artikeln får du även se hur man kontrollerar status, uppdaterar eller tar bort peerings för din krets.
documentationcenter: na
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: article
ms.date: 10/23/2018
ms.author: jaredro
ms.openlocfilehash: 3395c0f7498ab69a08745a7b3222135cb5e7292e
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2018
ms.locfileid: "50249203"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit-using-powershell"></a>Skapa och ändra peering för en ExpressRoute-krets med PowerShell

Den här artikeln hjälper dig att skapa och hantera routningskonfiguration för en ExpressRoute-krets i Resource Manager-distributionsmodellen med hjälp av PowerShell. Du kan också kontrollera status, uppdatera eller ta bort och Avetablerar peerings för en ExpressRoute-krets. Om du vill använda en annan metod för att arbeta med din krets väljer du en artikel i listan nedan:

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Azure CLI](howto-routing-cli.md)
> * [Video - privat peering](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Video - offentlig peering](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Video - Microsoft-peering](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (klassisk)](expressroute-howto-routing-classic.md)
> 


Dessa anvisningar gäller endast för kretsar som skapats med tjänstleverantörer som erbjuder tjänster för Layer 2-anslutning. Om du använder en leverantör av tjänster som erbjuder hanterade Layer 3-tjänster (vanligtvis en IPVPN, t.ex. MPLS), anslutningsleverantören konfigurerar och hanterar routning åt dig.

> [!IMPORTANT]
> Vi gör för närvarande inte reklam för peerings som konfigurerats av tjänstleverantörer via tjänsthanteringsportalen. Vi arbetar på att kunna aktivera den här funktionen snart. Kontrollera med din tjänstleverantör innan du konfigurerar BGP-peerings.
> 
> 

Du kan konfigurera en, två eller alla tre peerings (Azure privat, Azure offentlig och Microsoft) för en ExpressRoute-krets. Du kan konfigurera peerings i valfri ordning. Dock måste du se till att du slutför konfigurationen av en peering i taget. Läs mer om routning domäner och peerings [ExpressRoute-routningsdomäner](expressroute-circuit-peerings.md).

## <a name="configuration-prerequisites"></a>Förutsättningar för konfiguration

* Kontrollera att du har granskat sidorna [Förutsättningar](expressroute-prerequisites.md), [Routningskrav](expressroute-routing.md) sidan och [Arbetsflöden](expressroute-workflows.md) sidan innan du påbörjar konfigurationen.
* Du måste ha en aktiv ExpressRoute-krets. Följ anvisningarna för att [Skapa en ExpressRoute-krets](expressroute-howto-circuit-arm.md) och aktivera kretsen av anslutningsprovidern innan du fortsätter. ExpressRoute-kretsen måste vara i ett etablerat och aktiverat tillstånd att kunna köra cmdletarna i den här artikeln.

### <a name="working-with-azure-powershell"></a>Arbeta med Azure PowerShell

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="msft"></a>Microsoft-peering

Det här avsnittet hjälper dig att skapa, hämta, uppdatera och ta bort Microsofts peeringskonfiguration för en ExpressRoute-krets.

> [!IMPORTANT]
> Microsoft-peering av ExpressRoute-kretsar som konfigurerades före den 1 augusti 2017 kommer att ha alla service-prefix som annonseras via Microsoft-peering, även om flödesfilter inte har definierats. Microsoft-peering av ExpressRoute-kretsar som är konfigurerade på eller efter den 1 augusti 2017 har inte alla prefix annonseras förrän ett flödesfilter är kopplad till kretsen. Mer information finns i [konfigurera ett flödesfilter för Microsoft-peering](how-to-routefilter-powershell.md).
> 
> 

### <a name="to-create-microsoft-peering"></a>Så här skapar du Microsoft-peering

1. Logga in och välj din prenumeration.

  Om du har installerat PowerShell lokalt måste du logga in. Om du använder Azure Cloud Shell kan du hoppa över det här steget.

  ```azurepowershell
  Connect-AzureRmAccount
  ```

  Välj den prenumeration som du vill skapa ExpressRoute-krets.

  ```azurepowershell-interactive
Select-AzureRmSubscription -SubscriptionId "<subscription ID>"
  ```
2. Skapa en ExpressRoute-krets.

  Följ anvisningarna för att skapa en [ExpressRoute-krets](expressroute-howto-circuit-arm.md) och etablera den med anslutningsprovidern. f din anslutningsleverantör erbjuder hanteringstjänster för Layer 3, kan du be din anslutningsleverantör för att Microsoft peering åt dig. I så fall behöver du inte följa anvisningarna i nästa avsnitt. Om din anslutningsleverantör inte kan hantera routning åt dig, när du har skapat kretsen, fortsätta konfigurationen med nästa steg. 

3. Kontrollera ExpressRoute-krets för att kontrollera att den etablerats och aktiverats. Använd följande exempel:

  ```azurepowershell-interactive
  Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
  ```

  Svaret liknar följande exempel:

  ```
  Name                             : ExpressRouteARMCircuit
  ResourceGroupName                : ExpressRouteResourceGroup
  Location                         : westus
  Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
  Etag                             : W/"################################"
  ProvisioningState                : Succeeded
  Sku                              : {
                                       "Name": "Standard_MeteredData",
                                       "Tier": "Standard",
                                       "Family": "MeteredData"
                                     }
  CircuitProvisioningState         : Enabled
  ServiceProviderProvisioningState : Provisioned
  ServiceProviderNotes             : 
  ServiceProviderProperties        : {
                                       "ServiceProviderName": "Equinix",
                                       "PeeringLocation": "Silicon Valley",
                                       "BandwidthInMbps": 200
                                     }
  ServiceKey                       : **************************************
  Peerings                         : []
  ```
4. Konfigurera Microsoft-peering för kretsen. Kontrollera att du har följande information innan du fortsätter.

  * Ett/30 eller/126-undernätet för den primära länken. Detta måste vara ett giltigt offentligt IPv4- eller IPv6-prefix som du äger och registrerad i en RIR / IRR.
  * Ett/30 eller/126-undernätet för den sekundära länken. Detta måste vara ett giltigt offentligt IPv4- eller IPv6-prefix som du äger och registrerad i en RIR / IRR.
  * Ett giltigt VLAN-ID att upprätta denna peering på. Se till att ingen annan peering i kretsen använder samma VLAN-ID.
  * AS-tal för peering. Du kan använda både 2 byte och 4 byte som AS-tal.
  * Annonserade prefix: Du måste ange en lista över alla prefix som du planerar att annonsera i BGP-sessionen. Endast offentliga IP-adressprefix accepteras. Om du planerar att skicka en uppsättning prefix, kan du skicka en kommaavgränsad lista. Dessa prefix måste vara registrerade åt dig i ett RIR/IR. IPv4-BGP-sessioner kräver IPv4 annonserade prefix och IPv6-BGP-sessioner kräver IPv6 annonserade prefix. 
  * Routningens registernamn: Du kan ange den RIR/IR mot vilken AS-numret och prefixet är registrerade.
  * Valfritt:
    * Kund-ASN: Om du har reklamprefix som inte är registrerade på peeringens AS-nummer, kan du ange det AS-nummer som de är registrerade på.
    * En MD5-hash om du väljer att använda en.

  Använd följande exempel för att konfigurera Microsoft-peering för din krets:

  ```azurepowershell-interactive
  Add-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv4 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "123.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

  Add-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv6 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "3FFE:FFFF:0:CD31::/120" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

  Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
  ```

### <a name="getmsft"></a>Att hämta Microsofts peering-information

Du kan få information om konfigurationen med följande exempel:

```azurepowershell-interactive
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

Get-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt
```

### <a name="updatemsft"></a>Att uppdatera konfigurationen för Microsoft-peering

Du kan uppdatera någon del av konfigurationen med följande exempel:

```azurepowershell-interactive
Set-AzureRmExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv4 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "124.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

Set-AzureRmExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv6 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "3FFE:FFFF:0:CD31::/120" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="deletemsft"></a>Ta bort Microsoft-peering

Du kan ta bort peering-konfigurationen genom att köra följande cmdlet:

```azurepowershell-interactive
Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="private"></a>Azures privata peering

Det här avsnittet hjälper dig att skapa, hämta, uppdatera och ta bort Azures privata peeringskonfiguration för en ExpressRoute-krets.

### <a name="to-create-azure-private-peering"></a>Så här skapar du Azures privata peering

1. Importera PowerShell-modulen för ExpressRoute.

  Du måste installera den senaste versionen av PowerShell-installeraren från [PowerShell-galleriet](http://www.powershellgallery.com/) och importera modulerna i Azure Resource Manager i PowerShell-sessionen för att kunna börja använda ExpressRoute-cmdletar. Du måste köra PowerShell som administratör.

  ```azurepowershell-interactive
  Install-Module AzureRM
  Install-AzureRM
  ```

  Importera alla moduler som AzureRM.* inom det kända semantiska versionsintervallet.

  ```azurepowershell-interactive
  Import-AzureRM
  ```

  Du kan också importera en vald modul inom det kända semantiska versionsintervallet.

  ```azurepowershell-interactive
  Import-Module AzureRM.Network 
  ```

  Logga in på ditt konto.

  ```azurepowershell-interactive
  Connect-AzureRmAccount
  ```

  Välj den prenumeration som du vill skapa ExpressRoute-krets.

  ```azurepowershell-interactive
  Select-AzureRmSubscription -SubscriptionId "<subscription ID>"
  ```
2. Skapa en ExpressRoute-krets.

  Följ anvisningarna för att skapa en [ExpressRoute-krets](expressroute-howto-circuit-arm.md) och etablera den med anslutningsprovidern. Om din anslutningsleverantör erbjuder hanteringstjänster till Layer 3, kan du be anslutningsleverantören aktiverar Azures privata peering åt dig. I så fall behöver du inte följa anvisningarna i nästa avsnitt. Om din anslutningsleverantör inte kan hantera routning åt dig, när du har skapat kretsen, fortsätta konfigurationen med nästa steg.

3. Kontrollera ExpressRoute-krets för att kontrollera att den etablerats och aktiverats. Använd följande exempel:

  ```azurepowershell-interactive
  Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
  ```

  Svaret liknar följande exempel:

  ```
  Name                             : ExpressRouteARMCircuit
  ResourceGroupName                : ExpressRouteResourceGroup
  Location                         : westus
  Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
  Etag                             : W/"################################"
  ProvisioningState                : Succeeded
  Sku                              : {
                                       "Name": "Standard_MeteredData",
                                       "Tier": "Standard",
                                       "Family": "MeteredData"
                                     }
  CircuitProvisioningState         : Enabled
  ServiceProviderProvisioningState : Provisioned
  ServiceProviderNotes             : 
  ServiceProviderProperties        : {
                                       "ServiceProviderName": "Equinix",
                                       "PeeringLocation": "Silicon Valley",
                                       "BandwidthInMbps": 200
                                     }
  ServiceKey                       : **************************************
  Peerings                         : []
  ```
4. Konfigurera Azures privata peering för kretsen. Kontrollera att du har följande objekt innan du fortsätter med nästa steg:

  * Ett /30 undernät för den primära länken. Undernätet får inte vara en del av något adressutrymme som reserverats för virtuella nätverk.
  * Ett /30 undernät för den sekundära länken. Undernätet får inte vara en del av något adressutrymme som reserverats för virtuella nätverk.
  * Ett giltigt VLAN-ID att upprätta denna peering på. Se till att ingen annan peering i kretsen använder samma VLAN-ID.
  * AS-tal för peering. Du kan använda både 2 byte och 4 byte som AS-tal. Du kan använda ett privat AS-tal för den här peeringen. Se till att du inte använder 65515.
  * Valfritt:
    * En MD5-hash om du väljer att använda en.

  Använd följande exempel för att konfigurera Azures privata peering för din krets:

  ```azurepowershell-interactive
  Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

  Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
  ```

  Om du väljer att använda en MD5-hash, använder du exemplet nedan:

  ```azurepowershell-interactive
  Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200  -SharedKey "A1B2C3D4"
  ```

  > [!IMPORTANT]
  > Kontrollera att du anger AS-talet som peering-ASN, inte kund-ASN.
  > 
  >

### <a name="getprivate"></a>Att hämta Azures privata peering-information

Du kan hämta information om konfigurationen med hjälp av följande exempel:

```azurepowershell-interactive
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt
```

### <a name="updateprivate"></a>Att uppdatera konfigurationen av Azures privata peering

Du kan uppdatera någon del av konfigurationen med följande exempel. I det här exemplet uppdateras VLAN-ID för kretsen från 100 till 500.

```azurepowershell-interactive
Set-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="deleteprivate"></a>Att ta bort Azures privata peering

Du kan ta bort peering-konfigurationen genom att köra följande exempel:

> [!WARNING]
> Du måste se till att alla virtuella nätverk och ExpressRoute Global räckvidd anslutningar tas bort innan du kör det här exemplet. 
> 
> 

```azurepowershell-interactive
Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="public"></a>Azures offentliga peering

Det här avsnittet hjälper dig att skapa, hämta, uppdatera och ta bort Azures offentliga peeringskonfiguration för en ExpressRoute-krets.

### <a name="to-create-azure-public-peering"></a>Så här skapar du Azures offentliga peering

1. Importera PowerShell-modulen för ExpressRoute.

  Du måste installera den senaste versionen av PowerShell-installeraren från [PowerShell-galleriet](http://www.powershellgallery.com/) och importera modulerna i Azure Resource Manager i PowerShell-sessionen för att kunna börja använda ExpressRoute-cmdletar. Du måste köra PowerShell som administratör.

  ```azurepowershell-interactive
  Install-Module AzureRM

  Install-AzureRM
```

  Importera alla moduler som AzureRM.* inom det kända semantiska versionsintervallet.

  ```azurepowershell-interactive
  Import-AzureRM
  ```

  Du kan också importera en vald modul inom det kända semantiska versionsintervallet.

  ```azurepowershell-interactive
  Import-Module AzureRM.Network
```

  Logga in på ditt konto.

  ```azurepowershell-interactive
  Connect-AzureRmAccount
  ```

  Välj den prenumeration som du vill skapa ExpressRoute-krets.

  ```azurepowershell-interactive
  Select-AzureRmSubscription -SubscriptionId "<subscription ID>"
  ```
2. Skapa en ExpressRoute-krets.

  Följ anvisningarna för att skapa en [ExpressRoute-krets](expressroute-howto-circuit-arm.md) och etablera den med anslutningsprovidern. Om din anslutningsleverantör erbjuder hanteringstjänster till Layer 3, kan du be anslutningsleverantören aktiverar Azures offentliga peering åt dig. I så fall behöver du inte följa anvisningarna i nästa avsnitt. Om din anslutningsleverantör inte kan hantera routning åt dig, när du har skapat kretsen, fortsätta konfigurationen med nästa steg.

3. Kontrollera ExpressRoute-kretsen för att den har etablerats och aktiverats. Använd följande exempel:

  ```azurepowershell-interactive
  Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
  ```

  Svaret liknar följande exempel:

  ```
  Name                             : ExpressRouteARMCircuit
  ResourceGroupName                : ExpressRouteResourceGroup
  Location                         : westus
  Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
  Etag                             : W/"################################"
  ProvisioningState                : Succeeded
  Sku                              : {
                                      "Name": "Standard_MeteredData",
                                       "Tier": "Standard",
                                       "Family": "MeteredData"
                                     }
  CircuitProvisioningState         : Enabled
  ServiceProviderProvisioningState : Provisioned
  ServiceProviderNotes             : 
  ServiceProviderProperties        : {
                                       "ServiceProviderName": "Equinix",
                                       "PeeringLocation": "Silicon Valley",
                                       "BandwidthInMbps": 200
                                     }
  ServiceKey                       : **************************************
  Peerings                         : []
  ```
4. Konfigurera Azures offentliga peering för kretsen. Se till att du har följande information innan du forsätter.

  * Ett /30 undernät för den primära länken. Detta måste vara ett giltigt offentligt IPv4-prefix.
  * Ett /30 undernät för den sekundära länken. Detta måste vara ett giltigt offentligt IPv4-prefix.
  * Ett giltigt VLAN-ID att upprätta denna peering på. Se till att ingen annan peering i kretsen använder samma VLAN-ID.
  * AS-tal för peering. Du kan använda både 2 byte och 4 byte som AS-tal.
  * Valfritt:
    * En MD5-hash om du väljer att använda en.

  Kör följande exempel för att konfigurera Azures offentliga peering för din krets

  ```azurepowershell-interactive
  Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100

  Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
  ```

  Om du väljer att använda en MD5-hash, använder du exemplet nedan:

  ```azurepowershell-interactive
  Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100  -SharedKey "A1B2C3D4"

  Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
  ```

  > [!IMPORTANT]
  > Kontrollera att du anger AS-talet som peering-ASN, inte kund-ASN.
  > 
  >

### <a name="getpublic"></a>Att hämta Azures offentliga peering-information

Du kan få information om konfigurationen med följande cmdlet:

```azurepowershell-interactive
  $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

  Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt
  ```

### <a name="updatepublic"></a>Att uppdatera konfigurationen av Azures offentliga peering

Du kan uppdatera någon del av konfigurationen med följande exempel. I det här exemplet uppdateras VLAN-ID för kretsen från 200 till 600.

```azurepowershell-interactive
Set-AzureRmExpressRouteCircuitPeeringConfig  -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 600

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="deletepublic"></a>Att ta bort Azures offentliga peering

Du kan ta bort peering-konfigurationen genom att köra följande exempel:

```azurepowershell-interactive
Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="next-steps"></a>Nästa steg

Nästa steg [Länka ett VNet till en ExpressRoute-krets](expressroute-howto-linkvnet-arm.md).

* Mer information om ExpressRoute-arbetsflöden finns i [ExpressRoute-arbetsflöden](expressroute-workflows.md).
* Mer information om krets-peering finns i [ExpressRoute-kretsar och routningsdomäner](expressroute-circuit-peerings.md).
* Mer information om hur du arbetar med virtuella nätverk finns i [Översikt över virtuella nätverk](../virtual-network/virtual-networks-overview.md).
