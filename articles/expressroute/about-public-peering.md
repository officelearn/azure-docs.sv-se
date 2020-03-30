---
title: Create and manage Azure ExpressRoute public peering
description: Lär dig mer om och hantera offentlig Azure-peering
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: cherylmc
ms.openlocfilehash: 8c1afac834fb9abb2cbf82f16f046a1624b251f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481141"
---
# <a name="create-and-manage-expressroute-public-peering"></a>Skapa och hantera ExpressRoute offentlig peering

> [!div class="op_single_selector"]
> * [Artikel - Offentlig peering](about-public-peering.md)
> * [Video - Offentlig peering](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Artikel - Microsoft peering](expressroute-circuit-peerings.md#microsoftpeering)
>

Den här artikeln hjälper dig att skapa och hantera konfiguration av offentliga peering-routning för en ExpressRoute-krets. Du kan också kontrollera status, uppdatera eller ta bort och avetablera peerings. Den här artikeln gäller resurser hanterare som skapades innan offentlig peering avbröts. Om du har en tidigare befintlig krets (skapad innan offentlig peering är föråldrad) kan du hantera/konfigurera offentlig peering med [Azure PowerShell,](#powershell) [Azure CLI](#cli)och [Azure-portalen](#portal).

>[!NOTE]
>Offentlig peering är föråldrad. Du kan inte skapa offentlig peering på nya ExpressRoute-kretsar. Om du har en ny ExpressRoute-krets använder du i stället [Microsoft-peering](expressroute-circuit-peerings.md#microsoftpeering) för dina Azure-tjänster.
>

## <a name="connectivity"></a>Anslutning

Anslutningen initieras alltid från WAN till Microsoft Azure-tjänster. Microsoft Azure-tjänster kan inte initiera anslutningar till nätverket via den här routningsdomänen. Om din ExpressRoute-krets är aktiverad för offentlig Azure-peering kan du komma åt de [offentliga IP-intervall som används i Azure](../virtual-network/virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) via kretsen.

När offentlig peering är aktiverad kan du ansluta till de flesta Azure-tjänster. Vi tillåter dig inte att selektivt välja tjänster som vi annonserar vägar till.

* Tjänster som Azure Storage, SQL-databaser och webbplatser erbjuds på offentliga IP-adresser.
* Via den offentliga routningsdomänen för peering kan du privat ansluta till tjänster som finns på offentliga IP-adresser, inklusive VIP-adresser för dina molntjänster.
* Du kan ansluta den offentliga peering-domänen till din DMZ och ansluta till alla Azure-tjänster på deras offentliga IP-adresser från wan utan att behöva ansluta via internet.

## <a name="services"></a><a name="services"></a>Tjänster

I det här avsnittet visas de tjänster som är tillgängliga via offentlig peering. Eftersom offentlig peering är inaktuell finns det ingen plan för att lägga till nya eller ytterligare tjänster till offentlig peering. Om du använder offentlig peering och den tjänst du vill använda bara stöds via Microsoft-peering måste du växla till Microsoft-peering. Se [Microsoft-peering](expressroute-faqs.md#microsoft-peering) för en lista över tjänster som stöds.

**Stöds:**

* Power BI
* De flesta Azure-tjänster stöds. Kontrollera direkt med den tjänst som du vill använda för att verifiera supporten.

**Stöds inte:**
  * CDN
  * Azure Front Door
  * Multifaktorautentiseringsserver (äldre)
  * Traffic Manager

Om du vill verifiera tillgängligheten för en viss tjänst kan du läsa dokumentationen för den tjänsten för att se om det finns ett reserverat intervall som publicerats för den tjänsten. Sedan kan du slå upp IP-intervallen för måltjänsten och jämföra med intervallen i [Azure IP Ranges and Service Tags – Public Cloud XML-fil](https://www.microsoft.com/download/details.aspx?id=56519). Alternativt kan du öppna en supportbiljett för tjänsten i fråga för förtydligande.

## <a name="peering-comparison"></a><a name="compare"></a>Jämförelse av peering

[!INCLUDE [peering comparison](../../includes/expressroute-peering-comparison.md)]

> [!NOTE]
> Offentlig Azure-peering har 1 NAT IP-adress som är associerad med varje BGP-session. För större än 2 NAT IP-adresser, flytta till Microsoft peering. Med Microsoft-peering kan du konfigurera dina egna NAT-allokeringar samt använda flödesfilter för selektiva prefixannonser. Mer information finns i [Flytta till Microsoft-peering](https://docs.microsoft.com/azure/expressroute/how-to-move-peering).
>

## <a name="custom-route-filters"></a>Anpassade flödesfilter

Du kan definiera anpassade flödesfilter i nätverket så att endast de vägar du behöver förbrukas. På sidan [Routning](expressroute-routing.md) finns detaljerad information om routningskonfigurationen.

## <a name="azure-powershell-steps"></a><a name="powershell"></a>Azure PowerShell-steg


[!INCLUDE [CloudShell](../../includes/expressroute-cloudshell-powershell-about.md)]

Eftersom offentlig peering är inaktuell kan du inte konfigurera offentlig peering på en ny ExpressRoute-krets.

1. Kontrollera att du har en ExpressRoute-krets som är etablerad och även aktiverad. Använd följande exempel:

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
2. Konfigurera Azures offentliga peering för kretsen. Se till att du har följande information innan du går vidare.

   * Ett /30 undernät för den primära länken. Detta måste vara ett giltigt offentligt IPv4-prefix.
   * Ett /30 undernät för den sekundära länken. Detta måste vara ett giltigt offentligt IPv4-prefix.
   * Ett giltigt VLAN-ID att upprätta denna peering på. Se till att ingen annan peering i kretsen använder samma VLAN-ID.
   * AS-tal för peering. Du kan använda både 2 byte och 4 byte som AS-tal.
   * Valfritt:
   * En MD5-hash om du väljer att använda en.

   Kör följande exempel för att konfigurera offentlig Azure-peering för din krets

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

### <a name="to-get-azure-public-peering-details"></a><a name="getpublic"></a>Så här hämtar du information om offentlig Azure-peering

Du kan få konfigurationsinformation med följande cmdlet:

```azurepowershell-interactive
  $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

  Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt
  ```

### <a name="to-update-azure-public-peering-configuration"></a><a name="updatepublic"></a>Så här uppdaterar du konfigurationen av Azures offentliga peering

Du kan uppdatera alla delar av konfigurationen med hjälp av följande exempel. I det här exemplet uppdateras VLAN-ID:t för kretsen från 200 till 600.

```azurepowershell-interactive
Set-AzExpressRouteCircuitPeeringConfig  -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 600

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-delete-azure-public-peering"></a><a name="deletepublic"></a>Så här tar du bort Azures offentliga peering

Du kan ta bort peering-konfigurationen genom att köra följande exempel:

```azurepowershell-interactive
Remove-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="azure-cli-steps"></a><a name="cli"></a>Azure CLI-steg


[!INCLUDE [CloudShell](../../includes/expressroute-cloudshell-powershell-about.md)]

1. Kontrollera ExpressRoute-kretsen för att säkerställa att den är etablerad och även aktiverad. Använd följande exempel:

   ```azurecli-interactive
   az network express-route list
   ```

   Svaret liknar följande exempel:

   ```output
   "allowClassicOperations": false,
   "authorizations": [],
   "circuitProvisioningState": "Enabled",
   "etag": "W/\"1262c492-ffef-4a63-95a8-a6002736b8c4\"",
   "gatewayManagerEtag": null,
   "id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit",
   "location": "westus",
   "name": "MyCircuit",
   "peerings": [],
   "provisioningState": "Succeeded",
   "resourceGroup": "ExpressRouteResourceGroup",
   "serviceKey": "1d05cf70-1db5-419f-ad86-1ca62c3c125b",
   "serviceProviderNotes": null,
   "serviceProviderProperties": {
    "bandwidthInMbps": 200,
    "peeringLocation": "Silicon Valley",
    "serviceProviderName": "Equinix"
   },
   "serviceProviderProvisioningState": "Provisioned",
   "sku": {
    "family": "UnlimitedData",
    "name": "Standard_MeteredData",
    "tier": "Standard"
   },
   "tags": null,
   "type": "Microsoft.Network/expressRouteCircuits]
   ```

2. Konfigurera Azures offentliga peering för kretsen. Se till att du har följande information innan du går vidare.

   * Ett /30 undernät för den primära länken. Detta måste vara ett giltigt offentligt IPv4-prefix.
   * Ett /30 undernät för den sekundära länken. Detta måste vara ett giltigt offentligt IPv4-prefix.
   * Ett giltigt VLAN-ID att upprätta denna peering på. Se till att ingen annan peering i kretsen använder samma VLAN-ID.
   * AS-tal för peering. Du kan använda både 2 byte och 4 byte som AS-tal.
   * **Valfritt -** En MD5-hash om du väljer att använda en.

   Kör följande exempel för att konfigurera offentlig Azure-peering för din krets:

   ```azurecli-interactive
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 12.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 12.0.0.4/30 --vlan-id 200 --peering-type AzurePublicPeering
   ```

   Om du väljer att använda en MD5-hash använder du följande exempel:

   ```azurecli-interactive
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 12.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 12.0.0.4/30 --vlan-id 200 --peering-type AzurePublicPeering --SharedKey "A1B2C3D4"
   ```

   > [!IMPORTANT]
   > Kontrollera att du anger AS-talet som peering-ASN, inte kund-ASN.

### <a name="to-view-azure-public-peering-details"></a><a name="getpublic"></a>Så här visar du Azures offentliga peering-information

Du kan få konfigurationsinformation med hjälp av följande exempel:

```azurecli
az network express-route peering show -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

Utdata ser ut ungefär så här:

```output
{
  "azureAsn": 12076,
  "etag": "W/\"2e97be83-a684-4f29-bf3c-96191e270666\"",
  "gatewayManagerEtag": "18",
  "id": "/subscriptions/9a0c2943-e0c2-4608-876c-e0ddffd1211b/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/peerings/AzurePublicPeering",
  "lastModifiedBy": "Customer",
  "microsoftPeeringConfig": null,
  "name": "AzurePublicPeering",
  "peerAsn": 7671,
  "peeringType": "AzurePublicPeering",
  "primaryAzurePort": "",
  "primaryPeerAddressPrefix": "",
  "provisioningState": "Succeeded",
  "resourceGroup": "ExpressRouteResourceGroup",
  "routeFilter": null,
  "secondaryAzurePort": "",
  "secondaryPeerAddressPrefix": "",
  "sharedKey": null,
  "state": "Enabled",
  "stats": null,
  "vlanId": 100
}
```

### <a name="to-update-azure-public-peering-configuration"></a><a name="updatepublic"></a>Så här uppdaterar du konfigurationen av Azures offentliga peering

Du kan uppdatera alla delar av konfigurationen med hjälp av följande exempel. I det här exemplet uppdateras VLAN-ID:t för kretsen från 200 till 600.

```azurecli-interactive
az network express-route peering update --vlan-id 600 -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

### <a name="to-delete-azure-public-peering"></a><a name="deletepublic"></a>Så här tar du bort Azures offentliga peering

Du kan ta bort peering-konfigurationen genom att köra följande exempel:

```azurecli-interactive
az network express-route peering delete -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

## <a name="azure-portal-steps"></a><a name="portal"></a>Azure-portalens steg

Om du vill konfigurera peering använder du powershell- eller CLI-stegen i den här artikeln. Om du vill hantera en peering kan du använda avsnitten nedan. De här stegen påminner om att hantera en [Microsoft-peering i portalen](expressroute-howto-routing-portal-resource-manager.md#msft).

### <a name="to-view-azure-public-peering-details"></a><a name="get"></a>Så här visar du Azures offentliga peering-information

Visa egenskaperna för offentlig Azure-peering genom att välja peering i portalen.

### <a name="to-update-azure-public-peering-configuration"></a><a name="update"></a>Så här uppdaterar du konfigurationen av Azures offentliga peering

Markera raden för peering och ändra sedan peering-egenskaperna.

### <a name="to-delete-azure-public-peering"></a><a name="delete"></a>Så här tar du bort Azures offentliga peering

Ta bort peering-konfigurationen genom att välja borttagningsikonen.

## <a name="next-steps"></a>Nästa steg

Nästa steg, [Länka ett virtuellt nätverk till en ExpressRoute-krets](expressroute-howto-linkvnet-arm.md).

* Mer information om ExpressRoute-arbetsflöden finns i [ExpressRoute-arbetsflöden](expressroute-workflows.md).
* Mer information om krets-peering finns i [ExpressRoute-kretsar och routningsdomäner](expressroute-circuit-peerings.md).
* Mer information om hur du arbetar med virtuella nätverk finns i [Översikt över virtuella nätverk](../virtual-network/virtual-networks-overview.md).