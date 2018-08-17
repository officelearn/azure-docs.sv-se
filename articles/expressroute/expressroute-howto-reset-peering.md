---
title: Återställa Azure ExpressRoute-peerkopplingar | Microsoft Docs
description: Så här inaktiverar och aktiverar peeerings för en ExpressRoute-krets.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/15/2018
ms.author: charwen
ms.openlocfilehash: b8c9bc1944e9ed0281616062a84958c953d08694
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2018
ms.locfileid: "40180898"
---
# <a name="reset-expressroute-peerings"></a>Återställa ExpressRoute-peerkopplingar

Den här artikeln beskriver hur du inaktiverar och aktiverar peerings för en ExpressRoute-krets med hjälp av PowerShell. När du inaktiverar en peering stängs BGP-sessionen på både den primära anslutningen och den sekundära anslutningen av ExpressRoute-kretsen av. Du kommer att förlora anslutningen via denna peering till Microsoft. När du aktiverar en peer-koppling, kommer BGP-sessionen på både den primära anslutningen och den sekundära anslutningen av ExpressRoute-kretsen göras tillgänglig. Du ska återställa anslutningen via denna peering till Microsoft. Du kan aktivera och inaktivera Microsoft-Peering och privat Peering i Azure på en ExpressRoute-krets oberoende av varandra. När du först konfigurera peerings på ExpressRoute-kretsen är peerings aktiverade som standard. 

Det finns ett par scenarier där du kan det vara bra när du återställer din ExpressRoute-peerkopplingar.
* Testa dina disaster recovery design och implementering. Exempel: du har två ExpressRoute-kretsar. Du kan inaktivera peerings för en krets och tvinga trafik på nätverket för att växla över till andra kretsen.
* Aktivera dubbelriktad vidarebefordran identifiering (BFD) på Azure privat Peering i ExpressRoute-kretsen. BFD är aktiverad som standard om ExpressRoute-kretsen har skapats efter den 1 augusti 2018. Om din krets skapades före som, har inte BFD aktiverats. Du kan aktivera BFD genom att inaktivera peer-kopplingen och återaktivering av den. Det bör noteras att BFD endast stöds på Azure privat Peering.


## <a name="reset-a-peering"></a>Återställa en peer-koppling

1. Installera den senaste versionen av Azure Resource Managers PowerShell-cmdletar. Mer information finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/install-azurerm-ps).

2. Öppna PowerShell-konsolen med utökad behörighet och anslut till ditt konto. Använd följande exempel för att ansluta:

  ```powershell
  Connect-AzureRmAccount
  ```
3. Om du har flera Azure-prenumerationer anger du prenumerationerna för kontot.

  ```powershell
  Get-AzureRmSubscription
  ```
4. Ange den prenumeration som du vill använda.

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
  ```
5. Kör följande kommandon för att hämta ExpressRoute-kretsen.

  ```powershell
  $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
  ```
6. Identifiera den peering som du vill inaktivera eller aktivera. *Peer-kopplingar* är en matris. I följande exempel är peer-kopplingar [0] Azure privat Peering och Microsoft-Peering peer-kopplingar [1].

  ```powershell
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
7. Kör följande kommandon för att ändra tillståndet för peer-kopplingen.

  ```powershell
  $ckt.Peerings[0].State = "Disabled"
  Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
  ```
Peer-kopplingen ska vara i ett tillstånd som du anger. 

## <a name="next-steps"></a>Nästa steg
Om du behöver hjälp att felsöka ett problem med ExpressRoute kan du kolla i följande artiklar:
* [Verifiera ExpressRoute-anslutning](expressroute-troubleshooting-expressroute-overview.md)
* [Felsökning av nätverksprestanda](expressroute-troubleshooting-network-performance.md)
