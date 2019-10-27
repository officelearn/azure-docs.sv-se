---
title: 'Återställa krets-peering – ExpressRoute: Azure | Microsoft Docs'
description: Så här inaktiverar och aktiverar du ExpressRoute-krets-peering.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: 841c68b43e7f31693863268c3a7b2bd544c5e7ae
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/27/2019
ms.locfileid: "72965337"
---
# <a name="reset-expressroute-circuit-peerings"></a>Återställ ExpressRoute krets-peering

Den här artikeln beskriver hur du inaktiverar och aktiverar peering för en ExpressRoute-krets med hjälp av PowerShell. När du inaktiverar en peering stängs BGP-sessionen på både den primära anslutningen och den sekundära anslutningen av ExpressRoute-kretsen. Du förlorar anslutningen genom denna peering till Microsoft. När du aktiverar en peering skapas BGP-sessionen både på den primära anslutningen och den sekundära anslutningen av ExpressRoute-kretsen. Du kommer att återfå anslutningen genom denna peering till Microsoft. Du kan aktivera och inaktivera Microsoft-peering och Azures privata peering på en ExpressRoute-krets oberoende av varandra. När du först konfigurerar peer-kopplingarna på ExpressRoute-kretsen är peer-kopplingarna aktiverade som standard.

Det finns ett par scenarier där du kan få hjälp att återställa dina ExpressRoute-peer-datorer.
* Testa din design och implementering av haveri beredskap. Du kan till exempel ha två ExpressRoute-kretsar. Du kan inaktivera peering-kopplingar för en krets och tvinga nätverks trafiken att växla över till den andra kretsen.
* Aktivera identifiering av dubbelriktad vidarebefordran (BFD) på Azures privata peering eller Microsoft-peering av din ExpressRoute-krets. BFD är aktiverat som standard på Azures privata peering om din ExpressRoute-krets skapas efter 1 2018 och på Microsoft-peering om din ExpressRoute-krets skapas efter 1 2019 oktober. Om din krets skapades tidigare var BFD inte aktiverat. Du kan aktivera BFD genom att inaktivera peering och återaktivera den. 

### <a name="working-with-azure-powershell"></a>Arbeta med Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="reset-a-peering"></a>Återställa en peer koppling

1. Om du kör PowerShell lokalt öppnar du PowerShell-konsolen med utökade privilegier och ansluter till ditt konto. Använd följande exempel för att ansluta:

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
5. Identifiera peering som du vill inaktivera eller aktivera. *Peering* är en matris. I följande exempel är peering [0] Azures privata peering och peering [1] Microsoft-peering.

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
6. Kör följande kommandon för att ändra peering-status.

   ```azurepowershell-interactive
   $ckt.Peerings[0].State = "Disabled"
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```
   Peer-kopplingen ska vara i ett tillstånd som du anger. 

## <a name="next-steps"></a>Nästa steg
Om du behöver hjälp med att felsöka ett ExpressRoute-problem kan du läsa följande artiklar:
* [Verifiera ExpressRoute-anslutning](expressroute-troubleshooting-expressroute-overview.md)
* [Felsöka nätverksprestanda](expressroute-troubleshooting-network-performance.md)
