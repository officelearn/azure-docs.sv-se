---
title: 'Azure ExpressRoute: Konfigurera global räckvidd'
description: Den här artikeln hjälper dig att länka ExpressRoute-kretsar tillsammans för att skapa ett privat nätverk mellan dina lokala nätverk och aktivera Global Reach.
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: jaredro
ms.openlocfilehash: 587a17659a412d6f894faf5a744a7d9c444935c8
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656750"
---
# <a name="configure-expressroute-global-reach"></a>Konfigurera ExpressRoute Global Reach

Den här artikeln hjälper dig att konfigurera ExpressRoute Global Reach med PowerShell. Mer information finns i [ExpressRouteRoute Global Reach](expressroute-global-reach.md).

 ## <a name="before-you-begin"></a>Innan du börjar

Innan du startar konfigurationen bekräftar du följande:

* Du förstår ExpressRoute-kretsettablering [arbetsflöden](expressroute-workflows.md).
* Dina ExpressRoute-kretsar är i ett etablerat tillstånd.
* Azure private peering är konfigurerad på dina ExpressRoute-kretsar.
* Om du vill köra PowerShell lokalt kontrollerar du att den senaste versionen av Azure PowerShell är installerad på datorn.

### <a name="working-with-azure-powershell"></a>Arbeta med Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="identify-circuits"></a>Identifiera kretsar

1. Om du vill starta konfigurationen loggar du in på ditt Azure-konto och väljer den prenumeration som du vill använda.

   [!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]
2. Identifiera de ExpressRoute-kretsar som du vill använda. Du kan aktivera ExpressRoute Global Reach mellan den privata peering av två ExpressRoute-kretsar, så länge de finns i de länder/regioner som stöds och har skapats på olika peering-platser. 

   * Om din prenumeration äger båda kretsarna kan du välja vilken krets som helst för att köra konfigurationen i följande avsnitt.
   * Om de två kretsarna finns i olika Azure-prenumerationer behöver du auktorisering från en Azure-prenumeration. Sedan skickar du in auktoriseringsnyckeln när du kör konfigurationskommandot i den andra Azure-prenumerationen.

## <a name="enable-connectivity"></a>Aktivera anslutning

Aktivera anslutning mellan lokala nätverk. Det finns separata uppsättningar instruktioner för kretsar som finns i samma Azure-prenumeration och kretsar som är olika prenumerationer.

### <a name="expressroute-circuits-in-the-same-azure-subscription"></a>ExpressRoute-kretsar i samma Azure-prenumeration

1. Använd följande kommandon för att få krets 1 och krets 2. De två kretsarna är i samma abonnemang.

   ```azurepowershell-interactive
   $ckt_1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
   $ckt_2 = Get-AzExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
   ```
2. Kör följande kommando mot krets 1 och passera i det privata peering-ID:et för krets 2. När du kör kommandot bör du tänka på följande:

   * Det privata peering-ID:t liknar följande exempel: 

     ```
     /subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}/peerings/AzurePrivatePeering
     ```
   * *-AddressPrefix* måste vara ett IPv4-undernät /29, till exempel "10.0.0.0/29". Vi använder IP-adresser i det här undernätet för att upprätta anslutning mellan de två ExpressRoute-kretsarna. Du bör inte använda adresserna i det här undernätet i dina virtuella Azure-nätverk eller i ditt lokala nätverk.

     ```azurepowershell-interactive
     Add-AzExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering $ckt_2.Peerings[0].Id -AddressPrefix '__.__.__.__/29'
     ```
3. Spara konfigurationen på krets 1 enligt följande:

   ```azurepowershell-interactive
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
   ```

När den föregående åtgärden är klar har du anslutning mellan dina lokala nätverk på båda sidor via dina två ExpressRoute-kretsar.

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>ExpressRoute-kretsar i olika Azure-prenumerationer

Om de två kretsarna inte finns i samma Azure-prenumeration behöver du auktorisering. I följande konfiguration genereras auktorisering i krets 2-prenumerationen och auktoriseringsnyckeln skickas till krets 1.

1. Generera en auktoriseringsnyckel.

   ```azurepowershell-interactive
   $ckt_2 = Get-AzExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
   Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $ckt_2 -Name "Name_for_auth_key"
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_2
   ```

   Anteckna det privata peering-ID:et för krets 2, samt auktoriseringsnyckeln.
2. Kör följande kommando mot krets 1. Passera i det privata peering-ID:et för krets 2 och auktoriseringsnyckeln.

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering "circuit_2_private_peering_id" -AddressPrefix '__.__.__.__/29' -AuthorizationKey '########-####-####-####-############'
   ```
3. Spara konfigurationen på krets 1.

   ```azurepowershell-interactive
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
   ```

När den föregående åtgärden är klar har du anslutning mellan dina lokala nätverk på båda sidor via dina två ExpressRoute-kretsar.

## <a name="verify-the-configuration"></a>Kontrollera konfigurationen

Använd följande kommando för att verifiera konfigurationen på kretsen där konfigurationen gjordes (till exempel krets 1 i föregående exempel).
```azurepowershell-interactive
$ckt1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
```

Om du bara kör *$ckt1* i PowerShell visas *CircuitConnectionStatus* i utdata. Den talar om för dig om anslutningen är etablerad, "Ansluten" eller "Frånkopplad". 

## <a name="disable-connectivity"></a>Inaktivera anslutningar

Om du vill inaktivera anslutningen mellan dina lokala nätverk kör du kommandona mot den krets där konfigurationen gjordes (till exempel krets 1 i föregående exempel).

```azurepowershell-interactive
$ckt1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
Remove-AzExpressRouteCircuitConnectionConfig -Name "Your_connection_name" -ExpressRouteCircuit $ckt_1
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

Du kan köra åtgärden Hämta för att verifiera statusen.

När den föregående åtgärden är klar har du inte längre någon anslutning mellan det lokala nätverket via expressroutekretsarna.

## <a name="next-steps"></a>Nästa steg
1. [Läs mer om ExpressRoute Global Reach](expressroute-global-reach.md)
2. [Verifiera ExpressRoute-anslutning](expressroute-troubleshooting-expressroute-overview.md)
3. [Länka en ExpressRoute-krets till ett virtuellt Azure-nätverk](expressroute-howto-linkvnet-arm.md)
