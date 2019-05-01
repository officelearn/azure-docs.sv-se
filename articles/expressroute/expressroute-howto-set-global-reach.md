---
title: 'Konfigurera Global räckvidd - ExpressRoute: Azure | Microsoft Docs'
description: Den här artikeln hjälper dig att länka ExpressRoute-kretsar tillsammans för att göra ett privat nätverk mellan ditt lokala nätverk och aktivera Global räckvidd.
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: jaredro
ms.custom: seodec18
ms.openlocfilehash: de9cbd9cfac766e2a67274684d3fb6b447e45200
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64572750"
---
# <a name="configure-expressroute-global-reach"></a>Konfigurera ExpressRoute Global Reach

Den här artikeln hjälper dig att konfigurera ExpressRoute Global räckvidd med hjälp av PowerShell. Mer information finns i [ExpressRouteRoute Global räckvidd](expressroute-global-reach.md).

 ## <a name="before-you-begin"></a>Innan du börjar

Innan du börjar konfigurationen måste du kontrollera följande:

* Du förstår ExpressRoute kretsetablering [arbetsflöden](expressroute-workflows.md).
* ExpressRoute-kretsar är i ett etablerat tillstånd.
* Azures privata peering har konfigurerats på ExpressRoute-kretsar.
* Om du vill köra PowerShell lokalt måste du kontrollera att den senaste versionen av Azure PowerShell har installerats på datorn.

### <a name="working-with-azure-powershell"></a>Arbeta med Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="identify-circuits"></a>Identifiera kretsar

1. Logga in på ditt Azure-konto och välj den prenumeration som du vill använda för att starta konfigurationen.

   [!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]
2. Identifiera ExpressRoute-kretsar som du vill använda. Du kan aktivera ExpressRoute Global räckvidd mellan två ExpressRoute-kretsar så länge de befinner dig i de länder/regionerna och har skapats på olika peering-platser. 

   * Om din prenumeration äger både kretsar kan välja du antingen krets för att köra konfigurationen i följande avsnitt.
   * Om två kretsar finns i olika Azure-prenumerationer, behöver tillstånd från en Azure-prenumeration. Sedan skickar du in auktoriseringsnyckeln när du kör kommandot konfiguration i andra Azure-prenumeration.

## <a name="enable-connectivity"></a>Aktivera anslutning

Aktivera anslutning mellan ditt lokala nätverk. Det finns olika uppsättningar med instruktioner för kretsar som finns i samma Azure-prenumeration och kretsar som är olika prenumerationer.

### <a name="expressroute-circuits-in-the-same-azure-subscription"></a>ExpressRoute-kretsar i samma Azure-prenumeration

1. Använd följande kommandon för att hämta krets 1 och 2-kretsen. Två kretsar är i samma prenumeration.

   ```azurepowershell-interactive
   $ckt_1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
   $ckt_2 = Get-AzExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
   ```
2. Kör följande kommando mot krets 1 och skicka i privata peering-ID för kretsen 2. Tänk på följande när du kör kommandot:

   * Privat peering ID ska se ut ungefär som i följande exempel: 

     ```
     /subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}/peerings/AzurePrivatePeering
     ```
   * *-AddressPrefix* måste vara ett/29 IPv4-undernät, till exempel ”10.0.0.0/29”. Vi använder IP-adresser i det här undernätet för att upprätta en anslutning mellan de två ExpressRoute-kretsarna. Du bör inte använda adresserna i det här undernätet i din Azure-nätverk eller i ditt lokala nätverk.

     ```azurepowershell-interactive
     Add-AzExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering $ckt_2.Peerings[0].Id -AddressPrefix '__.__.__.__/29'
     ```
3. Spara konfigurationen på kretsen 1 på följande sätt:

   ```azurepowershell-interactive
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
   ```

När den föregående åtgärden har slutförts ska har du anslutning mellan ditt lokala nätverk på båda sidorna via två ExpressRoute-kretsarna.

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>ExpressRoute-kretsar i olika Azure-prenumerationer

Om två kretsar inte finns i samma Azure-prenumeration, måste auktorisering. I följande konfiguration auktorisering genereras i kretsen 2-prenumerationen och auktoriseringsnyckeln skickas till krets 1.

1. Generera en auktoriseringsnyckel.

   ```azurepowershell-interactive
   $ckt_2 = Get-AzExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
   Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $ckt_2 -Name "Name_for_auth_key"
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_2
   ```

   Anteckna privat peering ID för kretsen 2, samt auktoriseringsnyckeln.
2. Kör följande kommando mot krets 1. Skicka i privata peering-ID för kretsen 2 och auktoriseringsnyckeln för.

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering "circuit_2_private_peering_id" -AddressPrefix '__.__.__.__/29' -AuthorizationKey '########-####-####-####-############'
   ```
3. Spara konfigurationen på kretsen 1.

   ```azurepowershell-interactive
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
   ```

När den föregående åtgärden har slutförts ska har du anslutning mellan ditt lokala nätverk på båda sidorna via två ExpressRoute-kretsarna.

## <a name="verify-the-configuration"></a>Kontrollera konfigurationen

Använder du följande kommando för att kontrollera konfigurationen på kretsen där konfigurationen gjordes (till exempel krets 1 i exemplet ovan).
```azurepowershell-interactive
$ckt1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
```

Om du kör bara *$ckt1* i PowerShell kan du se *CircuitConnectionStatus* i utdata. Du kan ta reda om anslutningen har upprättats, ”ansluten” eller ”frånkopplad”. 

## <a name="disable-connectivity"></a>Inaktivera anslutning

Att inaktivera anslutningen mellan ditt lokala nätverk, köra kommandon mot kretsen där konfigurationen gjordes (till exempel krets 1 i exemplet ovan).

```azurepowershell-interactive
$ckt1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
Remove-AzExpressRouteCircuitConnectionConfig -Name "Your_connection_name" -ExpressRouteCircuit $ckt_1
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

Du kan köra Get-åtgärden för att kontrollera statusen.

När den föregående åtgärden är klar kan har du inte längre anslutning mellan ditt lokala nätverk via ExpressRoute-kretsar.

## <a name="next-steps"></a>Nästa steg
1. [Mer information om ExpressRoute Global räckvidd](expressroute-global-reach.md)
2. [Kontrollera ExpressRoute-anslutningen](expressroute-troubleshooting-expressroute-overview.md)
3. [Länka en ExpressRoute-krets till ett Azure-nätverk](expressroute-howto-linkvnet-arm.md)
