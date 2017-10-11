---
title: "Konfigurera routning (peering) för en ExpressRoute-krets: hanteraren för filserverresurser: PowerShell: Azure | Microsoft Docs"
description: "Den här artikeln vägleder dig genom stegen för att skapa och etablera privat, offentlig och Microsoft-peering av en ExpressRoute-krets. I artikeln får du även se hur man kontrollerar status, uppdaterar eller tar bort peerings för din krets."
documentationcenter: na
services: expressroute
author: ganesr
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 0a036d51-77ae-4fee-9ddb-35f040fbdcdf
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/31/2017
ms.author: ganesr;cherylmc
ms.openlocfilehash: af68955b78239832e413e1b59e033d7d3da8d599
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2017
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit-using-powershell"></a>Skapa och ändra peering för en ExpressRoute-krets med hjälp av PowerShell

Den här artikeln hjälper dig att skapa och hantera routningskonfiguration för en ExpressRoute-krets i Resource Manager-distributionsmodellen med hjälp av PowerShell. Du kan också kontrollera status, uppdatera eller ta bort och ta bort etableringen av peerkopplingar för en ExpressRoute-krets. Om du vill använda en annan metod för att arbeta med kretsen väljer du en artikel från följande lista:

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Azure CLI](howto-routing-cli.md)
> * [Video - privat peering](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Video - offentlig peering](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Video - Microsoft-peering](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (klassisk)](expressroute-howto-routing-classic.md)
> 

## <a name="configuration-prerequisites"></a>Förutsättningar för konfiguration

* Du behöver den senaste versionen av Azure Resource Manager PowerShell-cmdlets. Mer information finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/overview). 
* Kontrollera att du har granskat sidorna [Förutsättningar](expressroute-prerequisites.md), [Routningskrav](expressroute-routing.md) sidan och [Arbetsflöden](expressroute-workflows.md) sidan innan du påbörjar konfigurationen.
* Du måste ha en aktiv ExpressRoute-krets. Följ anvisningarna för att [Skapa en ExpressRoute-krets](expressroute-howto-circuit-arm.md) och aktivera kretsen av anslutningsprovidern innan du fortsätter. ExpressRoute-kretsen måste vara i ett etablerad och aktiverade tillstånd att kunna köra cmdlets i den här artikeln.

Dessa anvisningar gäller endast för kretsar som skapats med tjänstleverantörer som erbjuder tjänster för Layer 2-anslutning. Om du använder en tjänstprovider som erbjuder hanterade Layer 3-tjänster (vanligtvis en IPVPN, t.ex. MPLS), anslutningsleverantören kan konfigurera och hantera routning av du.

> [!IMPORTANT]
> Vi gör för närvarande inte reklam för peerings som konfigurerats av tjänstleverantörer via tjänsthanteringsportalen. Vi arbetar på att kunna aktivera den här funktionen snart. Kontrollera med leverantören innan du konfigurerar BGP peerkopplingar.
> 
> 

Du kan konfigurera en, två eller alla tre peerings (Azure privat, Azure offentlig och Microsoft) för en ExpressRoute-krets. Du kan konfigurera peerings i valfri ordning. Dock måste du se till att du slutför konfigurationen av en peering i taget. 

## <a name="azure-private-peering"></a>Azures privata peering

Det här avsnittet hjälper dig att skapa, hämta, uppdatera och ta bort Azure privat peering konfigurationen för en ExpressRoute-krets.

### <a name="to-create-azure-private-peering"></a>Så här skapar du Azures privata peering

1. Importera PowerShell-modulen för ExpressRoute.

  Du måste installera den senaste versionen av PowerShell-installeraren från [PowerShell-galleriet](http://www.powershellgallery.com/) och importera modulerna i Azure Resource Manager i PowerShell-sessionen för att kunna börja använda ExpressRoute-cmdletar. Du måste köra PowerShell som administratör.

  ```powershell
  Install-Module AzureRM
  Install-AzureRM
  ```

  Importera alla AzureRM.* modulerna inom intervallet semantiska versionen.

  ```powershell
  Import-AzureRM
  ```

  Du kan också importera en väljer modul inom intervallet för semantisk versionen.

  ```powershell
  Import-Module AzureRM.Network 
  ```

  Logga in på ditt konto.

  ```powershell
  Login-AzureRmAccount
  ```

  Välj den prenumeration som du vill skapa ExpressRoute-kretsen.

  ```powershell
  Select-AzureRmSubscription -SubscriptionId "<subscription ID>"
  ```
2. Skapa en ExpressRoute-krets.

  Följ anvisningarna för att skapa en [ExpressRoute-krets](expressroute-howto-circuit-arm.md) och etablera den med anslutningsprovidern.

  Om din anslutningsleverantör erbjuder hanteringstjänster för Layer 3, kan du begära att anslutningsleverantören aktiverar Azures privata peering åt dig. I så fall behöver du inte följa anvisningarna i nästa avsnitt. Om anslutningsleverantören inte kan hantera routning av du, när du har skapat din krets fortsätta konfigurationen med nästa steg.
3. Kontrollera ExpressRoute-krets och kontrollera att den är etablerad och aktiverats. Använd följande exempel:

  ```powershell
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

  * Ett /30 undernät för den primära länken. Undernätet får inte vara en del av alla adressutrymme som är reserverade för virtuella nätverk.
  * Ett /30 undernät för den sekundära länken. Undernätet får inte vara en del av alla adressutrymme som är reserverade för virtuella nätverk.
  * Ett giltigt VLAN-ID att upprätta denna peering på. Se till att ingen annan peering i kretsen använder samma VLAN-ID.
  * AS-tal för peering. Du kan använda både 2 byte och 4 byte som AS-tal. Du kan använda ett privat AS-tal för den här peeringen. Se till att du inte använder 65515.
  * **Valfritt -** en MD5-hash om du väljer att använda en.

  Använd följande exempel för att konfigurera privat Azure-peering för kretsen:

  ```powershell
  Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

  Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
  ```

  Om du väljer att använda en MD5-hash, Använd följande exempel:

  ```powershell
  Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200  -SharedKey "A1B2C3D4"
  ```

  > [!IMPORTANT]
  > Kontrollera att du anger AS-talet som peering-ASN, inte kund-ASN.
  > 
  >

### <a name="to-view-azure-private-peering-details"></a>Så här visar du Azures privata peering-information

Du kan få konfigurationsinformation genom att använda följande exempel:

```powershell
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -Circuit $ckt
```

### <a name="to-update-azure-private-peering-configuration"></a>Uppdatera konfigurationen av Azures privata peering

Du kan uppdatera någon del av konfigurationen med följande exempel. I det här exemplet uppdateras VLAN-ID för kretsen från 100 till 500.

```powershell
Set-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-delete-azure-private-peering"></a>Så här tar du bort Azures privata peering

Du kan ta bort peering konfigurationen genom att köra följande exempel:

> [!WARNING]
> Du måste se till att alla virtuella nätverk avlänkas från ExpressRoute-kretsen innan du kör det här exemplet. 
> 
> 

```powershell
Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="azure-public-peering"></a>Azures offentliga peering

Det här avsnittet hjälper dig att skapa, hämta, uppdatera och ta bort Azure offentlig peering konfigurationen för en ExpressRoute-krets.

### <a name="to-create-azure-public-peering"></a>Så här skapar du Azures offentliga peering

1. Importera PowerShell-modulen för ExpressRoute.

  Du måste installera den senaste versionen av PowerShell-installeraren från [PowerShell-galleriet](http://www.powershellgallery.com/) och importera modulerna i Azure Resource Manager i PowerShell-sessionen för att kunna börja använda ExpressRoute-cmdletar. Du måste köra PowerShell som administratör.

  ```powershell
  Install-Module AzureRM

  Install-AzureRM
```

  Importera alla AzureRM.* modulerna inom intervallet semantiska versionen.

  ```powershell
  Import-AzureRM
  ```

  Du kan också importera en väljer modul inom intervallet för semantisk versionen.

  ```powershell
  Import-Module AzureRM.Network
```

  Logga in på ditt konto.

  ```powershell
  Login-AzureRmAccount
  ```

  Välj den prenumeration som du vill skapa ExpressRoute-kretsen.

  ```powershell
  Select-AzureRmSubscription -SubscriptionId "<subscription ID>"
  ```
2. Skapa en ExpressRoute-krets.

  Följ anvisningarna för att skapa en [ExpressRoute-krets](expressroute-howto-circuit-arm.md) och etablera den med anslutningsprovidern.

  Om din anslutningsleverantör erbjuder hanteringstjänster för Layer 3, kan du begära att anslutningsleverantören aktiverar Azures privata peering åt dig. I så fall behöver du inte följa anvisningarna i nästa avsnitt. Om anslutningsleverantören inte kan hantera routning av du, när du har skapat din krets fortsätta konfigurationen med nästa steg.
3. Kontrollera ExpressRoute-kretsen om du vill att den är etablerad och aktiverats. Använd följande exempel:

  ```powershell
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
4. Konfigurera Azures offentliga peering för kretsen. Kontrollera att du har följande information innan du ska kunna fortsätta.

  * Ett /30 undernät för den primära länken. Detta måste vara ett giltigt offentligt IPv4-prefix.
  * Ett /30 undernät för den sekundära länken. Detta måste vara ett giltigt offentligt IPv4-prefix.
  * Ett giltigt VLAN-ID att upprätta denna peering på. Se till att ingen annan peering i kretsen använder samma VLAN-ID.
  * AS-tal för peering. Du kan använda både 2 byte och 4 byte som AS-tal.
  * **Valfritt -** en MD5-hash om du väljer att använda en.

  Kör följande exempel för att konfigurera offentlig Azure-peering för kretsen

  ```powershell
  Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100

  Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
  ```

  Om du väljer att använda en MD5-hash, Använd följande exempel:

  ```powershell
  Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100  -SharedKey "A1B2C3D4"

  Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
  ```

  > [!IMPORTANT]
  > Kontrollera att du anger AS-talet som peering-ASN, inte kund-ASN.
  > 
  >

### <a name="to-view-azure-public-peering-details"></a>Så här visar du Azures offentliga peering-information

Du kan hämta konfigurationsinformation med följande cmdlet:

```powershell
  $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

  Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt
  ```

### <a name="to-update-azure-public-peering-configuration"></a>Så här uppdaterar du konfigurationen av Azures offentliga peering

Du kan uppdatera någon del av konfigurationen med följande exempel. I det här exemplet uppdateras VLAN-ID för kretsen från 200 till 600.

```powershell
Set-AzureRmExpressRouteCircuitPeeringConfig  -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 600

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-delete-azure-public-peering"></a>Så här tar du bort Azures offentliga peering

Du kan ta bort peering konfigurationen genom att köra följande exempel:

```powershell
Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="microsoft-peering"></a>Microsoft-peering

Det här avsnittet hjälper dig att skapa, hämta, uppdatera och ta bort Microsoft peering konfigurationen för en ExpressRoute-krets.

> [!IMPORTANT]
> Microsoft-peering i ExpressRoute-kretsar som konfigurerades före den 1 augusti 2017 kommer att ha alla service prefix annonserade via Microsoft peering, även om filter routning inte har definierats. Microsoft-peering i ExpressRoute-kretsar som är konfigurerade på eller efter den 1 augusti 2017 har inte alla prefix annonseras förrän ett filter för flödet är kopplat till kretsen. Mer information finns i [konfigurera route filter för Microsoft-peering](how-to-routefilter-powershell.md).
> 
> 

### <a name="to-create-microsoft-peering"></a>Så här skapar du Microsoft-peering

1. Importera PowerShell-modulen för ExpressRoute.

  Du måste installera den senaste versionen av PowerShell-installeraren från [PowerShell-galleriet](http://www.powershellgallery.com/) och importera modulerna i Azure Resource Manager i PowerShell-sessionen för att kunna börja använda ExpressRoute-cmdletar. Du måste köra PowerShell som administratör.

  ```powershell
  Install-Module AzureRM

  Install-AzureRM
  ```

  Importera alla AzureRM.* modulerna inom intervallet semantiska versionen.

  ```powershell
  Import-AzureRM
  ```

  Du kan också importera en väljer modul inom intervallet för semantisk versionen.

  ```powershell
  Import-Module AzureRM.Network
  ```

  Logga in på ditt konto.

  ```powershell
  Login-AzureRmAccount
  ```

  Välj den prenumeration som du vill skapa ExpressRoute-kretsen.

  ```powershell
Select-AzureRmSubscription -SubscriptionId "<subscription ID>"
  ```
2. Skapa en ExpressRoute-krets.

  Följ anvisningarna för att skapa en [ExpressRoute-krets](expressroute-howto-circuit-arm.md) och etablera den med anslutningsprovidern.

  Om din anslutningsleverantör erbjuder hanteringstjänster för Layer 3, kan du begära att anslutningsleverantören aktiverar Azures privata peering åt dig. I så fall behöver du inte följa anvisningarna i nästa avsnitt. Om anslutningsleverantören inte kan hantera routning av du, när du har skapat din krets fortsätta konfigurationen med nästa steg.
3. Kontrollera ExpressRoute-krets och kontrollera att den är etablerad och aktiverats. Använd följande exempel:

  ```powershell
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

  * Ett /30 undernät för den primära länken. Detta måste vara ett giltigt offentligt IPv4-prefix som du äger och har registrerat i en RIR/IR.
  * Ett /30 undernät för den sekundära länken. Detta måste vara ett giltigt offentligt IPv4-prefix som du äger och har registrerat i en RIR/IR.
  * Ett giltigt VLAN-ID att upprätta denna peering på. Se till att ingen annan peering i kretsen använder samma VLAN-ID.
  * AS-tal för peering. Du kan använda både 2 byte och 4 byte som AS-tal.
  * Annonserade prefix: Du måste ange en lista över alla prefix som du planerar att annonsera i BGP-sessionen. Endast offentliga IP-adressprefix accepteras. Om du planerar att skicka en uppsättning prefix, kan du skicka en kommaavgränsad lista. Dessa prefix måste vara registrerade åt dig i ett RIR/IR.
  * **Valfritt -** kunden ASN: Om du är reklam prefix som inte har registrerats peering som tal, kan du ange numret som de har registrerats.
  * Routningens registernamn: Du kan ange den RIR/IR mot vilken AS-numret och prefixet är registrerade.
  * **Valfritt -** en MD5-hash om du väljer att använda en.

   Använd följande exempel för att konfigurera Microsoft-peering för kretsen:

  ```powershell
  Add-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "123.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

  Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
  ```

### <a name="to-get-microsoft-peering-details"></a>Så här hämtar du Microsofts peering-information

Du kan hämta konfigurationsinformation enligt följande exempel:

```powershell
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

Get-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt
```

### <a name="to-update-microsoft-peering-configuration"></a>Så här uppdaterar du Microsofts peering-konfiguration

Du kan uppdatera någon del av konfigurationen med följande exempel:

```powershell
Set-AzureRmExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "124.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-delete-microsoft-peering"></a>Så här tar du bort Microsoft-peering

Du kan ta bort peering konfigurationen genom att köra följande cmdlet:

```powershell
Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="next-steps"></a>Nästa steg

Nästa steg [Länka ett VNet till en ExpressRoute-krets](expressroute-howto-linkvnet-arm.md).

* Mer information om ExpressRoute-arbetsflöden finns i [ExpressRoute-arbetsflöden](expressroute-workflows.md).
* Mer information om krets-peering finns i [ExpressRoute-kretsar och routningsdomäner](expressroute-circuit-peerings.md).
* Mer information om hur du arbetar med virtuella nätverk finns i [Översikt över virtuella nätverk](../virtual-network/virtual-networks-overview.md).
