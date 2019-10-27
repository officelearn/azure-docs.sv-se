---
title: 'Konfigurera peering för en krets-ExpressRoute: PowerShell: Azure | Microsoft Docs'
description: Den här artikeln vägleder dig genom stegen för att skapa och etablera privat, offentlig och Microsoft-peering av en ExpressRoute-krets. I artikeln får du även se hur man kontrollerar status, uppdaterar eller tar bort peerings för din krets.
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: jaredro
ms.custom: seodec18
ms.openlocfilehash: 3f2c768fe7998cc166e3102619e7183c96d1ccbb
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/27/2019
ms.locfileid: "72965353"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit-using-powershell"></a>Skapa och ändra peering för en ExpressRoute-krets med PowerShell

Den här artikeln hjälper dig att skapa och hantera konfiguration av routning för en ExpressRoute-krets i distributions modellen för Resource Manager med hjälp av PowerShell. Du kan också kontrol lera status, uppdatera eller ta bort och avetablera peer-kopplingar för en ExpressRoute-krets. Om du vill använda en annan metod för att arbeta med din krets väljer du en artikel i följande lista:

> [!div class="op_single_selector"]
> * [Azure-portalen](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Azure CLI](howto-routing-cli.md)
> * [Video-privat peering](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Video – offentlig peering](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Video – Microsoft-peering](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (klassisk)](expressroute-howto-routing-classic.md)
> 


Dessa anvisningar gäller endast för kretsar som skapats med tjänstleverantörer som erbjuder tjänster för Layer 2-anslutning. Om du använder en tjänst leverantör som erbjuder hanterade Layer 3-tjänster (vanligt vis en IPVPN, t. ex. MPLS), kommer anslutnings leverantören konfigurera och hantera routning åt dig.

> [!IMPORTANT]
> Vi gör för närvarande inte reklam för peerings som konfigurerats av tjänstleverantörer via tjänsthanteringsportalen. Vi arbetar på att kunna aktivera den här funktionen snart. Kontakta tjänst leverantören innan du konfigurerar BGP-peering.
> 
> 

Du kan konfigurera en, två eller alla tre peerings (Azure privat, Azure offentlig och Microsoft) för en ExpressRoute-krets. Du kan konfigurera peerings i valfri ordning. Dock måste du se till att du slutför konfigurationen av en peering i taget. Mer information om routningsdomäner och peering-domäner finns i [ExpressRoute](expressroute-circuit-peerings.md).

## <a name="configuration-prerequisites"></a>Förutsättningar för konfiguration

* Kontrollera att du har granskat sidorna [Förutsättningar](expressroute-prerequisites.md), [Routningskrav](expressroute-routing.md) sidan och [Arbetsflöden](expressroute-workflows.md) sidan innan du påbörjar konfigurationen.
* Du måste ha en aktiv ExpressRoute-krets. Följ anvisningarna för att [Skapa en ExpressRoute-krets](expressroute-howto-circuit-arm.md) och aktivera kretsen av anslutningsprovidern innan du fortsätter. ExpressRoute-kretsen måste vara i ett tillhandahållet och aktiverat tillstånd för att du ska kunna köra cmdletarna i den här artikeln.

### <a name="working-with-azure-powershell"></a>Arbeta med Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="msft"></a>Microsoft-peering

Det här avsnittet hjälper dig att skapa, Hämta, uppdatera och ta bort Microsofts peering-konfiguration för en ExpressRoute-krets.

> [!IMPORTANT]
> Microsoft-peering av ExpressRoute-kretsar som har kon figurer ATS före den 1 augusti 2017 kommer att ha alla tjänst prefix som annonseras via Microsoft-peering, även om det inte finns några väg filter definierade. Microsoft-peering av ExpressRoute-kretsar som är konfigurerade på eller efter den 1 augusti 2017 har inga prefix som annonseras förrän ett flödes filter är kopplat till kretsen. Mer information finns i [Konfigurera ett flödes filter för Microsoft-peering](how-to-routefilter-powershell.md).
> 
> 

### <a name="to-create-microsoft-peering"></a>Så här skapar du Microsoft-peering

1. Logga in och välj din prenumeration.

   Om du har installerat PowerShell lokalt loggar du in. Om du använder Azure Cloud Shell kan du hoppa över det här steget.

   ```azurepowershell
   Connect-AzAccount
   ```

   Välj den prenumeration som du vill skapa ExpressRoute-kretsen.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId "<subscription ID>"
   ```
2. Skapa en ExpressRoute-krets.

   Följ anvisningarna för att skapa en [ExpressRoute-krets](expressroute-howto-circuit-arm.md) och etablera den med anslutningsprovidern. f din anslutnings leverantör erbjuder hanterade Layer 3-tjänster, du kan be din anslutnings leverantör att aktivera Microsoft-peering åt dig. I så fall behöver du inte följa anvisningarna i nästa avsnitt. Om din anslutnings leverantör inte hanterar routning åt dig, kan du, när du har skapat din krets, fortsätta med konfigurationen med hjälp av nästa steg. 

3. Kontrol lera ExpressRoute-kretsen för att se till att den är etablerad och också aktive rad. Använd följande exempel:

   ```azurepowershell-interactive
   Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
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

   * Ett/30-eller/126-undernät för den primära länken. Detta måste vara ett giltigt offentligt IPv4-eller IPv6-prefix som du äger och registrerat i en RIR/IR.
   * Ett/30-eller/126-undernät för den sekundära länken. Detta måste vara ett giltigt offentligt IPv4-eller IPv6-prefix som du äger och registrerat i en RIR/IR.
   * Ett giltigt VLAN-ID att upprätta denna peering på. Se till att ingen annan peering i kretsen använder samma VLAN-ID.
   * AS-tal för peering. Du kan använda både 2 byte och 4 byte som AS-tal.
   * Annonserade prefix: Du måste ange en lista över alla prefix som du planerar att annonsera i BGP-sessionen. Endast offentliga IP-adressprefix accepteras. Om du planerar att skicka en uppsättning prefix kan du skicka en kommaavgränsad lista. Dessa prefix måste vara registrerade åt dig i ett RIR/IR. IPv4 BGP-sessioner kräver IPv4-annonserade prefix och IPv6 BGP-sessioner kräver IPv6-annonserade prefix. 
   * Routningens registernamn: Du kan ange den RIR/IR mot vilken AS-numret och prefixet är registrerade.
   * Valfritt:
     * Kund-ASN: Om du har reklamprefix som inte är registrerade på peeringens AS-nummer, kan du ange det AS-nummer som de är registrerade på.
     * En MD5-hash om du väljer att använda en.

> [!IMPORTANT]
> Microsoft kontrollerar om de angivna annonserade offentliga prefixen och peer-ASN (eller kund-ASN) är tilldelade till dig i Internet-routningstabellen. Om du hämtar de offentliga prefixen från en annan entitet och om tilldelningen inte är registrerad i routningstabellen, slutförs inte den automatiska verifieringen och kräver manuell verifiering. Om den automatiska valideringen Miss lyckas visas "AdvertisedPublicPrefixesState" som "verifiering krävs" i resultatet av "Get-AzExpressRouteCircuitPeeringConfig" (se avsnittet "Hämta information om Microsoft-peering" nedan). 
> 
> Om du ser meddelandet "validering krävs" samlar du in dokumenten som visar att de offentliga prefixen har tilldelats till din organisation av den entitet som anges som ägare till prefixen i routningstabellen och skickar dessa dokument för manuell verifiering med öppna ett support ärende som visas nedan. 
> 
>

   Använd följande exempel för att konfigurera Microsoft-peering för din krets:

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv4 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "123.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

   Add-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv6 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "3FFE:FFFF:0:CD31::/120" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

### <a name="getmsft"></a>För att hämta information om Microsofts peering

Du kan hämta konfigurations information med hjälp av följande exempel:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

Get-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt
```

### <a name="updatemsft"></a>Så här uppdaterar du Microsofts peering-konfiguration

Du kan uppdatera valfri del av konfigurationen med hjälp av följande exempel:

```azurepowershell-interactive
Set-AzExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv4 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "124.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

Set-AzExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv6 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "3FFE:FFFF:0:CD31::/120" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="deletemsft"></a>Ta bort Microsoft-peering

Du kan ta bort peering-konfigurationen genom att köra följande cmdlet:

```azurepowershell-interactive
Remove-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="private"></a>Azure privat peering

Det här avsnittet hjälper dig att skapa, Hämta, uppdatera och ta bort Azures konfiguration för privata peering för en ExpressRoute-krets.

### <a name="to-create-azure-private-peering"></a>Så här skapar du Azures privata peering

1. Importera PowerShell-modulen för ExpressRoute.

   Du måste installera den senaste versionen av PowerShell-installeraren från [PowerShell-galleriet](https://www.powershellgallery.com/) och importera modulerna i Azure Resource Manager i PowerShell-sessionen för att kunna börja använda ExpressRoute-cmdletar. Du måste köra PowerShell som administratör.

   ```azurepowershell-interactive
   Install-Module Az
   ```

   Importera alla AZ.\*-moduler inom det kända semantiska versions intervallet.

   ```azurepowershell-interactive
   Import-Module Az
   ```

   Du kan också importera en SELECT-modul inom det kända semantiska versions intervallet.

   ```azurepowershell-interactive
   Import-Module Az.Network 
   ```

   Logga in på ditt konto.

   ```azurepowershell-interactive
   Connect-AzAccount
   ```

   Välj den prenumeration som du vill skapa ExpressRoute-kretsen.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId "<subscription ID>"
   ```
2. Skapa en ExpressRoute-krets.

   Följ anvisningarna för att skapa en [ExpressRoute-krets](expressroute-howto-circuit-arm.md) och etablera den med anslutningsprovidern. Om din anslutnings leverantör erbjuder hanterade Layer 3-tjänster kan du be din anslutnings leverantör att aktivera Azures privata peering åt dig. I så fall behöver du inte följa anvisningarna i nästa avsnitt. Om din anslutnings leverantör inte hanterar routning åt dig, kan du, när du har skapat din krets, fortsätta med konfigurationen med hjälp av nästa steg.

3. Kontrol lera ExpressRoute-kretsen för att se till att den är etablerad och också aktive rad. Använd följande exempel:

   ```azurepowershell-interactive
   Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
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

   * Ett /30 undernät för den primära länken. Under nätet får inte ingå i något adress utrymme som är reserverat för virtuella nätverk.
   * Ett /30 undernät för den sekundära länken. Under nätet får inte ingå i något adress utrymme som är reserverat för virtuella nätverk.
   * Ett giltigt VLAN-ID att upprätta denna peering på. Se till att ingen annan peering i kretsen använder samma VLAN-ID.
   * AS-tal för peering. Du kan använda både 2 byte och 4 byte som AS-tal. Du kan använda ett privat AS-tal för den här peeringen. Se till att du inte använder 65515.
   * Valfritt:
     * En MD5-hash om du väljer att använda en.

   Använd följande exempel för att konfigurera Azures privata peering för din krets:

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

   Om du väljer att använda en MD5-hash använder du följande exempel:

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200  -SharedKey "A1B2C3D4"
   ```

   > [!IMPORTANT]
   > Kontrollera att du anger AS-talet som peering-ASN, inte kund-ASN.
   > 
   >

### <a name="getprivate"></a>Så här hämtar du information om Azure privat peering

Du kan hämta konfigurations information med hjälp av följande exempel:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt
```

### <a name="updateprivate"></a>Så här uppdaterar du Azures konfiguration för privat peering

Du kan uppdatera valfri del av konfigurationen med hjälp av följande exempel. I det här exemplet uppdateras VLAN-ID: t för kretsen från 100 till 500.

```azurepowershell-interactive
Set-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="deleteprivate"></a>Ta bort privat Azure-peering

Du kan ta bort peering-konfigurationen genom att köra följande exempel:

> [!WARNING]
> Du måste se till att alla virtuella nätverk och ExpressRoute Global Reach-anslutningar tas bort innan du kör det här exemplet. 
> 
> 

```azurepowershell-interactive
Remove-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="public"></a>Offentlig Azure-peering

Det här avsnittet hjälper dig att skapa, Hämta, uppdatera och ta bort den offentliga Azure-peering-konfigurationen för en ExpressRoute-krets.

> [!Note]
> Offentlig Azure-peering är inaktuell för nya kretsar. Mer information finns i [ExpressRoute-peering](expressroute-circuit-peerings.md).
>

### <a name="to-create-azure-public-peering"></a>Så här skapar du Azures offentliga peering

1. Importera PowerShell-modulen för ExpressRoute.

   Du måste installera den senaste versionen av PowerShell-installeraren från [PowerShell-galleriet](https://www.powershellgallery.com/) och importera modulerna i Azure Resource Manager i PowerShell-sessionen för att kunna börja använda ExpressRoute-cmdletar. Du måste köra PowerShell som administratör.

   ```azurepowershell-interactive
   Install-Module Az
   ```

   Importera alla AZ.\*-moduler inom det kända semantiska versions intervallet.

   ```azurepowershell-interactive
   Import-Module Az
   ```

   Du kan också importera en SELECT-modul inom det kända semantiska versions intervallet.

   ```azurepowershell-interactive
   Import-Module Az.Network
   ```

   Logga in på ditt konto.

   ```azurepowershell-interactive
   Connect-AzAccount
   ```

   Välj den prenumeration som du vill skapa ExpressRoute-kretsen.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId "<subscription ID>"
   ```
2. Skapa en ExpressRoute-krets.

   Följ anvisningarna för att skapa en [ExpressRoute-krets](expressroute-howto-circuit-arm.md) och etablera den med anslutningsprovidern. Om din anslutnings leverantör erbjuder hanterade Layer 3-tjänster kan du be anslutnings leverantören att aktivera Azures offentliga peering åt dig. I så fall behöver du inte följa anvisningarna i nästa avsnitt. Om din anslutnings leverantör inte hanterar routning åt dig, kan du, när du har skapat din krets, fortsätta med konfigurationen med hjälp av nästa steg.

3. Kontrol lera ExpressRoute-kretsen för att säkerställa att den är etablerad och också aktive rad. Använd följande exempel:

   ```azurepowershell-interactive
   Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
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
4. Konfigurera Azures offentliga peering för kretsen. Se till att du har följande information innan du fortsätter.

   * Ett /30 undernät för den primära länken. Detta måste vara ett giltigt offentligt IPv4-prefix.
   * Ett /30 undernät för den sekundära länken. Detta måste vara ett giltigt offentligt IPv4-prefix.
   * Ett giltigt VLAN-ID att upprätta denna peering på. Se till att ingen annan peering i kretsen använder samma VLAN-ID.
   * AS-tal för peering. Du kan använda både 2 byte och 4 byte som AS-tal.
   * Valfritt:
     * En MD5-hash om du väljer att använda en.

   Kör följande exempel för att konfigurera Azures offentliga peering för din krets

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

   Om du väljer att använda en MD5-hash använder du följande exempel:

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100  -SharedKey "A1B2C3D4"

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

   > [!IMPORTANT]
   > Kontrollera att du anger AS-talet som peering-ASN, inte kund-ASN.
   > 
   >

### <a name="getpublic"></a>Så här hämtar du information om offentliga Azure-peering

Du kan hämta konfigurations information med följande cmdlet:

```azurepowershell-interactive
  $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

  Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt
  ```

### <a name="updatepublic"></a>Så här uppdaterar du Azures offentliga peering-konfiguration

Du kan uppdatera valfri del av konfigurationen med hjälp av följande exempel. I det här exemplet uppdateras VLAN-ID: t för kretsen från 200 till 600.

```azurepowershell-interactive
Set-AzExpressRouteCircuitPeeringConfig  -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 600

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="deletepublic"></a>Ta bort offentlig Azure-peering

Du kan ta bort peering-konfigurationen genom att köra följande exempel:

```azurepowershell-interactive
Remove-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="next-steps"></a>Nästa steg

Nästa steg [Länka ett VNet till en ExpressRoute-krets](expressroute-howto-linkvnet-arm.md).

* Mer information om ExpressRoute-arbetsflöden finns i [ExpressRoute-arbetsflöden](expressroute-workflows.md).
* Mer information om krets-peering finns i [ExpressRoute-kretsar och routningsdomäner](expressroute-circuit-peerings.md).
* Mer information om hur du arbetar med virtuella nätverk finns i [Översikt över virtuella nätverk](../virtual-network/virtual-networks-overview.md).
