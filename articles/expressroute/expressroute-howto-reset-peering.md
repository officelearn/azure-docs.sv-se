---
title: 'Azure ExpressRoute: Återställ krets peering'
description: Så här inaktiverar och aktiverar du ExpressRoute-krets peerings.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: conceptual
ms.date: 01/13/2018
ms.author: charwen
ms.openlocfilehash: 9f32eb439872de9e4687d046745c03bafd86b2fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75941741"
---
# <a name="reset-expressroute-circuit-peerings"></a>Återställa ExpressRoute-krets peerings

I den här artikeln beskrivs hur du inaktiverar och aktiverar peerings av en ExpressRoute-krets med PowerShell. När du inaktiverar en peering stängs BGP-sessionen på både den primära anslutningen och den sekundära anslutningen av ExpressRoute-kretsen av. Du kommer att förlora anslutningen via den här peering till Microsoft. När du aktiverar en peering tas BGP-sessionen på både den primära anslutningen och den sekundära anslutningen av ExpressRoute-kretsen upp. Du kommer att återfå anslutningen via den här peering till Microsoft. Du kan aktivera och inaktivera Microsoft Peering och Azure Private Peering på en ExpressRoute-krets oberoende av dem. När du först konfigurerar peerings på din ExpressRoute-krets aktiveras peerings som standard.

Det finns ett par scenarier där du kan finna det bra att återställa dina ExpressRoute-peerings.
* Testa din katastrofåterställningsdesign och implementering. Du har till exempel två ExpressRoute-kretsar. Du kan inaktivera peerings av en krets och tvinga nätverkstrafiken att växla över till den andra kretsen.
* Aktivera BFD (Bidirectional Forwarding Detection) på Azure Private Peering eller Microsoft Peering på din ExpressRoute-krets. BFD är aktiverat som standard på Azure Private Peering om din ExpressRoute-krets skapas efter den 1 augusti 2018 och på Microsoft Peering om din ExpressRoute-krets skapas efter den 10 januari 2020. Om din krets skapades innan dess var BFD inte aktiverat. Du kan aktivera BFD genom att inaktivera peering och aktivera den igen. 

### <a name="working-with-azure-powershell"></a>Arbeta med Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="reset-a-peering"></a>Återställa en peering

1. Om du kör PowerShell lokalt öppnar du PowerShell-konsolen med förhöjda privilegier och ansluter till ditt konto. Använd följande exempel för att ansluta:

   ```azurepowershell
   Connect-AzAccount
   ```
2. Om du har flera Azure-prenumerationer anger du prenumerationerna för kontot.

   ```azurepowershell-interactive
   Get-AzSubscription
   ```
3. Ange den prenumeration som du vill använda.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
   ```
4. Kör följande kommandon för att hämta din ExpressRoute-krets.

   ```azurepowershell-interactive
   $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   ```
5. Identifiera den peering som du vill inaktivera eller aktivera. *Peerings* är en matris. I följande exempel är Peerings[0] Azure Private Peering och Peerings[1] Microsoft Peering.

   ```azurepowershell-interactive
   Name                             : ExpressRouteARMCircuit
   ResourceGroupName                : ExpressRouteResourceGroup
   Location                         : westus
   Id                               : /subscriptions/########-####-####-####-############/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
   Etag                             : W/"cd011bef-dc79-49eb-b4c6-81fb6ea5d178"
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
                                     "ServiceProviderName": "Coresite",
                                     "PeeringLocation": "Los Angeles",
                                     "BandwidthInMbps": 50
                                   }
   ServiceKey                       : ########-####-####-####-############
   Peerings                         : [
                                     {
                                       "Name": "AzurePrivatePeering",
                                       "Etag": "W/\"cd011bef-dc79-49eb-b4c6-81fb6ea5d178\"",
                                       "Id": "/subscriptions/########-####-####-####-############/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit/peerings/AzurePrivatePeering",
                                       "PeeringType": "AzurePrivatePeering",
                                       "State": "Enabled",
                                       "AzureASN": 12076,
                                       "PeerASN": 123,
                                       "PrimaryPeerAddressPrefix": "10.0.0.0/30",
                                       "SecondaryPeerAddressPrefix": "10.0.0.4/30",
                                       "PrimaryAzurePort": "",
                                       "SecondaryAzurePort": "",
                                       "VlanId": 789,
                                       "MicrosoftPeeringConfig": {
                                         "AdvertisedPublicPrefixes": [],
                                         "AdvertisedCommunities": [],
                                         "AdvertisedPublicPrefixesState": "NotConfigured",
                                         "CustomerASN": 0,
                                         "LegacyMode": 0,
                                         "RoutingRegistryName": "NONE"
                                       },
                                       "ProvisioningState": "Succeeded",
                                       "GatewayManagerEtag": "",
                                       "LastModifiedBy": "Customer",
                                       "Connections": []
                                     },
                                     {
                                       "Name": "MicrosoftPeering",
                                       "Etag": "W/\"cd011bef-dc79-49eb-b4c6-81fb6ea5d178\"",
                                       "Id": "/subscriptions/########-####-####-####-############/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit/peerings/MicrosoftPeering",
                                       "PeeringType": "MicrosoftPeering",
                                       "State": "Enabled",
                                       "AzureASN": 12076,
                                       "PeerASN": 123,
                                       "PrimaryPeerAddressPrefix": "3.0.0.0/30",
                                       "SecondaryPeerAddressPrefix": "3.0.0.4/30",
                                       "PrimaryAzurePort": "",
                                       "SecondaryAzurePort": "",
                                       "VlanId": 345,
                                       "MicrosoftPeeringConfig": {
                                         "AdvertisedPublicPrefixes": [
                                           "3.0.0.3/32"
                                         ],
                                         "AdvertisedCommunities": [],
                                         "AdvertisedPublicPrefixesState": "ValidationNeeded",
                                         "CustomerASN": 0,
                                         "LegacyMode": 0,
                                         "RoutingRegistryName": "NONE"
                                       },
                                       "ProvisioningState": "Succeeded",
                                       "GatewayManagerEtag": "",
                                       "LastModifiedBy": "Customer",
                                       "Connections": []
                                     }
                                   ]
   Authorizations                   : []
   AllowClassicOperations           : False
   GatewayManagerEtag               :
   ```
6. Kör följande kommandon för att ändra peering-läget.

   ```azurepowershell-interactive
   $ckt.Peerings[0].State = "Disabled"
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```
   Peering bör vara i ett tillstånd som du anger. 

## <a name="next-steps"></a>Nästa steg
Om du behöver hjälp med att felsöka ett ExpressRoute-problem kan du läsa följande artiklar:
* [Verifiera ExpressRoute-anslutning](expressroute-troubleshooting-expressroute-overview.md)
* [Felsöka nätverksprestanda](expressroute-troubleshooting-network-performance.md)
