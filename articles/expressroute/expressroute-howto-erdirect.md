---
title: 'Azure ExpressRoute: Konfigurera ExpressRoute Direct'
description: Den här sidan hjälper dig att konfigurera ExpressRoute Direct.
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 01/22/2020
ms.author: jaredro
ms.openlocfilehash: 2722a852b1119ef619bc414bce5cb3a8ff6f8f00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77031620"
---
# <a name="how-to-configure-expressroute-direct"></a>Konfigurera ExpressRoute Direct

ExpressRoute Direct ger dig möjlighet att ansluta direkt till Microsofts globala nätverk på peering-platser som är strategiskt fördelade över hela världen. Mer information finns i [Om ExpressRoute Direct](expressroute-erdirect-about.md).

## <a name="create-the-resource"></a><a name="resources"></a>Skapa resursen

1. Logga in på Azure och välj prenumerationen. ExpressRoute Direct-resursen och ExpressRoute-kretsarna måste finnas i samma prenumeration.

   ```powershell
   Connect-AzAccount 

   Select-AzSubscription -Subscription "<SubscriptionID or SubscriptionName>"
   ```
   
2. Registrera din prenumeration på Microsoft.Network för att komma åt api:erna för expressrouteportslocation och expressrouteport.

   ```powershell
   Register-AzResourceProvider -ProviderNameSpace "Microsoft.Network"
   ```   
3. Lista alla platser där ExpressRoute Direct stöds.
  
   ```powershell
   Get-AzExpressRoutePortsLocation
   ```

   **Exempel på utdata**
  
   ```powershell
   Name                : Equinix-Ashburn-DC2
   Id                  : /subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Ashburn-D
                        C2
   ProvisioningState   : Succeeded
   Address             : 21715 Filigree Court, DC2, Building F, Ashburn, VA 20147
   Contact             : support@equinix.com
   AvailableBandwidths : []

   Name                : Equinix-Dallas-DA3
   Id                  : /subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Dallas-DA
                        3
   ProvisioningState   : Succeeded
   Address             : 1950 N. Stemmons Freeway, Suite 1039A, DA3, Dallas, TX 75207
   Contact             : support@equinix.com
   AvailableBandwidths : []

   Name                : Equinix-San-Jose-SV1
   Id                  : /subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-San-Jose-
                        SV1
   ProvisioningState   : Succeeded
   Address             : 11 Great Oaks Blvd, SV1, San Jose, CA 95119
   Contact             : support@equinix.com
   AvailableBandwidths : []
   ```
4. Ta reda på om en plats som anges ovan har tillgänglig bandbredd

   ```powershell
   Get-AzExpressRoutePortsLocation -LocationName "Equinix-San-Jose-SV1"
   ```

   **Exempel på utdata**

   ```powershell
   Name                : Equinix-San-Jose-SV1
   Id                  : /subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-San-Jose-
                        SV1
   ProvisioningState   : Succeeded
   Address             : 11 Great Oaks Blvd, SV1, San Jose, CA 95119
   Contact             : support@equinix.com
   AvailableBandwidths : [
                          {
                            "OfferName": "100 Gbps",
                            "ValueInGbps": 100
                          }
                        ]
   ```
5. Skapa en ExpressRoute Direct-resurs baserat på den plats som valts ovan

   ExpressRoute Direct stöder både QinQ och Dot1Q inkapsling. Om QinQ väljs tilldelas varje ExpressRoute-krets dynamiskt en S-Tag och kommer att vara unik i hela ExpressRoute Direct-resursen. Varje C-Tag på kretsen måste vara unik på banan, men inte över ExpressRoute Direct.  

   Om Dot1Q-inkapsling är markerat måste du hantera unikheten för C-Tag (VLAN) över hela ExpressRoute Direct-resursen.  

   > [!IMPORTANT]
   > ExpressRoute Direct kan bara vara en inkapslingstyp. Inkapsling kan inte ändras när ExpressRoute Direct har skapats.
   > 
 
   ```powershell 
   $ERDirect = New-AzExpressRoutePort -Name $Name -ResourceGroupName $ResourceGroupName -PeeringLocation $PeeringLocationName -BandwidthInGbps 100.0 -Encapsulation QinQ | Dot1Q -Location $AzureRegion
   ```

   > [!NOTE]
   > Attributet Inkapsling kan också ställas in på Dot1Q. 
   >

   **Exempel på utdata:**

   ```powershell
   Name                       : Contoso-Direct
   ResourceGroupName          : Contoso-Direct-rg
   Location                   : westcentralus
   Id                         : /subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/exp
                               ressRoutePorts/Contoso-Direct
   Etag                       : W/"<etagnumber> "
   ResourceGuid               : <number>
   ProvisioningState          : Succeeded
   PeeringLocation            : Equinix-Seattle-SE2
   BandwidthInGbps            : 100
   ProvisionedBandwidthInGbps : 0
   Encapsulation              : QinQ
   Mtu                        : 1500
   EtherType                  : 0x8100
   AllocationDate             : Saturday, September 1, 2018
   Links                      : [
                                 {
                                   "Name": "link1",
                                   "Etag": "W/\"<etagnumber>\"",
                                   "Id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.
                               Network/expressRoutePorts/Contoso-Direct/links/link1",
                                   "RouterName": "tst-09xgmr-cis-1",
                                   "InterfaceName": "HundredGigE2/2/2",
                                   "PatchPanelId": "PPID",
                                   "RackId": "RackID",
                                   "ConnectorType": "SC",
                                   "AdminState": "Disabled",
                                   "ProvisioningState": "Succeeded"
                                 },
                                 {
                                   "Name": "link2",
                                   "Etag": "W/\"<etagnumber>\"",
                                   "Id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.
                               Network/expressRoutePorts/Contoso-Direct/links/link2",
                                   "RouterName": "tst-09xgmr-cis-2",
                                   "InterfaceName": "HundredGigE2/2/2",
                                   "PatchPanelId": "PPID",
                                   "RackId": "RackID",
                                   "ConnectorType": "SC",
                                   "AdminState": "Disabled",
                                   "ProvisioningState": "Succeeded"
                                 }
                               ]
   Circuits                   : []
   ```

## <a name="change-admin-state-of-links"></a><a name="state"></a>Ändra admintillstånd för länkar

  Den här processen bör användas för att utföra ett Layer 1-test, så att varje korsanslutning är korrekt korrigerad i varje router för primär och sekundär.
1. Få Information om ExpressRoute Direct.

   ```powershell
   $ERDirect = Get-AzExpressRoutePort -Name $Name -ResourceGroupName $ResourceGroupName
   ```
2. Ange länk till Aktiverad. Upprepa det här steget för att ställa in varje länk som aktiverad.

   Länkar[0] är den primära porten och Länkar[1] är den sekundära porten.

   ```powershell
   $ERDirect.Links[0].AdminState = "Enabled"
   Set-AzExpressRoutePort -ExpressRoutePort $ERDirect
   $ERDirect = Get-AzExpressRoutePort -Name $Name -ResourceGroupName $ResourceGroupName
   $ERDirect.Links[1].AdminState = "Enabled"
   Set-AzExpressRoutePort -ExpressRoutePort $ERDirect
   ```
   **Exempel på utdata:**

   ```powershell
   Name                       : Contoso-Direct
   ResourceGroupName          : Contoso-Direct-rg
   Location                   : westcentralus
   Id                         : /subscriptions/<number>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/exp
                             ressRoutePorts/Contoso-Direct
   Etag                       : W/"<etagnumber> "
   ResourceGuid               : <number>
   ProvisioningState          : Succeeded
   PeeringLocation            : Equinix-Seattle-SE2
   BandwidthInGbps            : 100
   ProvisionedBandwidthInGbps : 0
   Encapsulation              : QinQ
   Mtu                        : 1500
   EtherType                  : 0x8100
   AllocationDate             : Saturday, September 1, 2018
   Links                      : [
                               {
                                 "Name": "link1",
                                 "Etag": "W/\"<etagnumber>\"",
                                 "Id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.
                             Network/expressRoutePorts/Contoso-Direct/links/link1",
                                 "RouterName": "tst-09xgmr-cis-1",
                                 "InterfaceName": "HundredGigE2/2/2",
                                 "PatchPanelId": "PPID",
                                 "RackId": "RackID",
                                 "ConnectorType": "SC",
                                 "AdminState": "Enabled",
                                 "ProvisioningState": "Succeeded"
                               },
                               {
                                 "Name": "link2",
                                 "Etag": "W/\"<etagnumber>\"",
                                 "Id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.
                             Network/expressRoutePorts/Contoso-Direct/links/link2",
                                 "RouterName": "tst-09xgmr-cis-2",
                                 "InterfaceName": "HundredGigE2/2/2",
                                 "PatchPanelId": "PPID",
                                 "RackId": "RackID",
                                 "ConnectorType": "SC",
                                 "AdminState": "Enabled",
                                 "ProvisioningState": "Succeeded"
                               }
                             ]
   Circuits                   : []
   ```

   Använd samma procedur med `AdminState = "Disabled"` för att stänga av portarna.

## <a name="create-a-circuit"></a><a name="circuit"></a>Skapa en krets

Som standard kan du skapa 10 kretsar i prenumerationen där ExpressRoute Direct-resursen finns. Detta kan ökas med stöd. Du är ansvarig för att spåra både etablerad och utnyttjad bandbredd. Etablerad bandbredd är summan av bandbredden för alla kretsar på ExpressRoute Direct-resursen och den utnyttjade bandbredden är den fysiska användningen av de underliggande fysiska gränssnitten.

Det finns ytterligare kretsbandbredder som kan användas på ExpressRoute Direct endast för att stödja de scenarier som beskrivs ovan. Dessa är: 40Gbps och 100Gbps.

**SkuTier** kan vara lokal, standard eller Premium.

**SkuFamily** får endast vara MeteredData eftersom obegränsad inte stöds på ExpressRoute Direct.

Skapa en krets på ExpressRoute Direct-resursen.

  ```powershell
  New-AzExpressRouteCircuit -Name $Name -ResourceGroupName $ResourceGroupName -ExpressRoutePort $ERDirect -BandwidthinGbps 100.0  -Location $AzureRegion -SkuTier Premium -SkuFamily MeteredData 
  ```

  Andra bandbredder inkluderar: 5.0, 10.0 och 40.0

  **Exempel på utdata:**

  ```powershell
  Name                             : ExpressRoute-Direct-ckt
  ResourceGroupName                : Contoso-Direct-rg
  Location                         : westcentralus
  Id                               : /subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Netwo
                                   rk/expressRouteCircuits/ExpressRoute-Direct-ckt
  Etag                             : W/"<etagnumber>"
  ProvisioningState                : Succeeded
  Sku                              : {
                                     "Name": "Premium_MeteredData",
                                     "Tier": "Premium",
                                     "Family": "MeteredData"
                                   }
  CircuitProvisioningState         : Enabled
  ServiceProviderProvisioningState : Provisioned
  ServiceProviderNotes             : 
    ServiceProviderProperties        : null
  ExpressRoutePort                 : {
                                     "Id": "/subscriptions/<subscriptionID>n/resourceGroups/Contoso-Direct-rg/providers/Micros
                                   oft.Network/expressRoutePorts/Contoso-Direct"
                                   }
  BandwidthInGbps                  : 10
  Stag                             : 2
  ServiceKey                       : <number>
  Peerings                         : []
  Authorizations                   : []
  AllowClassicOperations           : False
  GatewayManagerEtag     
  ```

## <a name="next-steps"></a>Nästa steg

Mer information om ExpressRoute Direct finns i [översikten](expressroute-erdirect-about.md).
