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
ms.openlocfilehash: aff283da27197b11ee496faecdd8b69571d3547e
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56821344"
---
# <a name="configure-expressroute-global-reach"></a>Konfigurera ExpressRoute Global Reach
Den här artikeln hjälper dig att konfigurera ExpressRoute Global räckvidd med hjälp av PowerShell. Mer information finns i [ExpressRouteRoute Global räckvidd](expressroute-global-reach.md).

Innan du börjar konfigurationen måste du kontrollera följande:

* Att du har installerat den senaste versionen av Azure PowerShell. Mer information finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps).
* Att du förstår ExpressRoute kretsetablering [arbetsflöden](expressroute-workflows.md).
* När är din ExpressRoute-kretsar i ett etablerat tillstånd.
* Att Azures privata peering har konfigurerats på ExpressRoute-kretsar.  

### <a name="sign-in-to-your-azure-account"></a>Logga in på ditt Azure-konto
Starta konfigurationen genom att logga in på ditt Azure-konto. 

Öppna PowerShell-konsolen med förhöjd behörighet och anslut sedan till ditt konto. Kommandot uppmanar dig logga in autentiseringsuppgifterna för ditt Azure-konto.  

```powershell
Connect-AzureRmAccount
```

Om du har flera Azure-prenumerationer anger du prenumerationerna för kontot.

```powershell
Get-AzureRmSubscription
```

Ange den prenumeration som du vill använda.

```powershell
Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
```

### <a name="identify-your-expressroute-circuits-for-configuration"></a>Identifiera din ExpressRoute-kretsar för konfiguration
Du kan aktivera ExpressRoute Global räckvidd mellan två ExpressRoute-kretsar så länge de befinner dig i de länder/regioner som stöds och har skapats på olika peering-platser. Om din prenumeration äger både kretsar kan välja du antingen krets för att köra konfigurationen i följande avsnitt. 

Om två kretsar finns i olika Azure-prenumerationer, behöver tillstånd från en Azure-prenumeration. Sedan skickar du in auktoriseringsnyckeln när du kör kommandot konfiguration i andra Azure-prenumeration.

## <a name="enable-connectivity-between-your-on-premises-networks"></a>Aktivera anslutning mellan ditt lokala nätverk

Använd följande kommandon för att hämta krets 1 och 2-kretsen. Två kretsar är i samma prenumeration.

```powershell
$ckt_1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
$ckt_2 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
```

Kör följande kommando mot krets 1 och skicka i privata peering-ID för kretsen 2. Tänk på följande när du kör kommandot:

* Privat peering ID ska se ut ungefär som i följande exempel: 

  ```
  /subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}/peerings/AzurePrivatePeering
  ```
* *-AddressPrefix* måste vara ett/29 IPv4-undernät, till exempel ”10.0.0.0/29”. Vi använder IP-adresser i det här undernätet för att upprätta en anslutning mellan de två ExpressRoute-kretsarna. Du bör inte använda adresserna i det här undernätet i din Azure-nätverk eller i ditt lokala nätverk.

```powershell
Add-AzureRmExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering $ckt_2.Peerings[0].Id -AddressPrefix '__.__.__.__/29'
```

Spara konfigurationen på kretsen 1 på följande sätt:
```powershell
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

När den föregående åtgärden har slutförts bör du ha anslutning mellan ditt lokala nätverk på båda sidorna via två ExpressRoute-kretsarna.

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>ExpressRoute-kretsar i olika Azure-prenumerationer

Om två kretsar inte finns i samma Azure-prenumeration, måste auktorisering. I följande konfiguration auktorisering genereras i kretsen 2-prenumerationen och auktoriseringsnyckeln skickas till krets 1.

Generera en auktoriseringsnyckel. 
```powershell
$ckt_2 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $ckt_2 -Name "Name_for_auth_key"
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_2
```
Anteckna privat peering ID för kretsen 2, samt auktoriseringsnyckeln.

Kör följande kommando mot krets 1. Skicka i privata peering-ID för kretsen 2 och auktoriseringsnyckeln för.
```powershell
Add-AzureRmExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering "circuit_2_private_peering_id" -AddressPrefix '__.__.__.__/29' -AuthorizationKey '########-####-####-####-############'
```

Spara konfigurationen på kretsen 1.
```powershell
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

När den föregående åtgärden har slutförts bör du ha anslutning mellan ditt lokala nätverk på båda sidorna via två ExpressRoute-kretsarna.

## <a name="get-and-verify-the-configuration"></a>Hämta och verifiera konfigurationen

Använder du följande kommando för att kontrollera konfigurationen på kretsen där konfigurationen gjordes (till exempel krets 1 i exemplet ovan).

```powershell
$ckt1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
```

Om du kör bara *$ckt1* i PowerShell kan du se *CircuitConnectionStatus* i utdata. Du kan ta reda om anslutningen har upprättats, ”ansluten” eller ”frånkopplad”. 

## <a name="disable-connectivity-between-your-on-premises-networks"></a>Inaktivera anslutning mellan ditt lokala nätverk

Om du vill inaktivera anslutning kör du kommandon mot kretsen där konfigurationen gjordes (till exempel krets 1 i exemplet ovan).

```powershell
$ckt1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
Remove-AzureRmExpressRouteCircuitConnectionConfig -Name "Your_connection_name" -ExpressRouteCircuit $ckt_1
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

Du kan köra Get-åtgärden för att kontrollera statusen. 

När den föregående åtgärden är klar kan har du inte längre anslutning mellan ditt lokala nätverk via ExpressRoute-kretsar. 


## <a name="next-steps"></a>Nästa steg
1. [Mer information om ExpressRoute Global räckvidd](expressroute-global-reach.md)
2. [Kontrollera ExpressRoute-anslutningen](expressroute-troubleshooting-expressroute-overview.md)
3. [Länka en ExpressRoute-krets till ett Azure-nätverk](expressroute-howto-linkvnet-arm.md)


