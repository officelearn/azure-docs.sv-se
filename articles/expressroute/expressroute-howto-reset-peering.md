---
title: 'Azure-ExpressRoute: återställa krets-peering'
description: Så här inaktiverar och aktiverar du ExpressRoute-krets peerings.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: conceptual
ms.date: 01/13/2018
ms.author: charwen
ms.openlocfilehash: 9f32eb439872de9e4687d046745c03bafd86b2fa
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2020
ms.locfileid: "75941741"
---
# <a name="reset-expressroute-circuit-peerings"></a>Återställa ExpressRoute-krets peerkopplingar

Den här artikeln beskriver hur du inaktiverar och aktiverar peerings för en ExpressRoute-krets med hjälp av PowerShell. När du inaktiverar en peering stängs BGP-sessionen på både den primära anslutningen och den sekundära anslutningen av ExpressRoute-kretsen av. Du kommer att förlora anslutningen via denna peering till Microsoft. När du aktiverar en peer-koppling, kommer BGP-sessionen på både den primära anslutningen och den sekundära anslutningen av ExpressRoute-kretsen göras tillgänglig. Du ska återställa anslutningen via denna peering till Microsoft. Du kan aktivera och inaktivera Microsoft-Peering och privat Peering i Azure på en ExpressRoute-krets oberoende av varandra. När du först konfigurera peerings på ExpressRoute-kretsen är peerings aktiverade som standard.

Det finns ett par scenarier där du kan det vara bra när du återställer din ExpressRoute-peerkopplingar.
* Testa dina disaster recovery design och implementering. Exempel: du har två ExpressRoute-kretsar. Du kan inaktivera peerings för en krets och tvinga trafik på nätverket för att växla över till andra kretsen.
* Aktivera identifiering av dubbelriktad vidarebefordran (BFD) på Azures privata peering eller Microsoft-peering av din ExpressRoute-krets. BFD är aktiverat som standard på Azures privata peering om din ExpressRoute-krets skapas efter 1 2018 och på Microsoft-peering om din ExpressRoute-krets har skapats efter 10 2020 januari. Om din krets skapades före som, har inte BFD aktiverats. Du kan aktivera BFD genom att inaktivera peer-kopplingen och återaktivering av den. 

### <a name="working-with-azure-powershell"></a>Arbeta med Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="reset-a-peering"></a>Återställa en peer-koppling

1. Om du kör PowerShell lokalt, öppna PowerShell-konsolen med förhöjd behörighet och Anslut till ditt konto. Använd följande exempel för att ansluta:

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
4. Kör följande kommandon för att hämta ExpressRoute-kretsen.

   ```azurepowershell-interactive
   $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   ```
5. Identifiera den peering som du vill inaktivera eller aktivera. *Peer-kopplingar* är en matris. I följande exempel är peer-kopplingar [0] Azure privat Peering och Microsoft-Peering peer-kopplingar [1].

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
6. Kör följande kommandon för att ändra tillståndet för peer-kopplingen.

   ```azurepowershell-interactive
   $ckt.Peerings[0].State = "Disabled"
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```
   Peer-kopplingen ska vara i ett tillstånd som du anger. 

## <a name="next-steps"></a>Nästa steg
Om du behöver hjälp att felsöka ett problem med ExpressRoute kan du kolla i följande artiklar:
* [Verifiera ExpressRoute-anslutning](expressroute-troubleshooting-expressroute-overview.md)
* [Felsöka nätverksprestanda](expressroute-troubleshooting-network-performance.md)
