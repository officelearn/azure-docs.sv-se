---
title: 'Konfigurera Global räckvidd - ExpressRoute: Azure | Microsoft Docs'
description: Den här artikeln hjälper dig att länka ExpressRoute-kretsar tillsammans för att göra ett privat nätverk mellan ditt lokala nätverk och aktivera Global räckvidd.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: mialdrid
ms.custom: seodec18
ms.openlocfilehash: dca2001fda7658b422bbceb14612dec1f7be6cd2
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53140905"
---
# <a name="configure-expressroute-global-reach-preview"></a>Konfigurera ExpressRoute Global räckvidd (förhandsversion)
Den här artikeln hjälper dig att konfigurera ExpressRoute Global räckvidd med hjälp av PowerShell. Mer information finns i [ExpressRouteRoute Global räckvidd](expressroute-global-reach.md).
 
## <a name="before-you-begin"></a>Innan du börjar
> [!IMPORTANT]
> Den offentliga förhandsversionen tillhandahålls utan serviceavtal och bör inte användas för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller har begränsad funktionalitet, eller så är de inte tillgängliga på alla Azure-platser. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
> 


Innan du börjar konfigurationen måste du kontrollera följande krav.

* Installera den senaste versionen av Azure PowerShell. Se [installera och konfigurera Azure PowerShell](/powershell/azure/install-azurerm-ps).
* Förstå ExpressRoute kretsetablering [arbetsflöden](expressroute-workflows.md).
* Kontrollera att din ExpressRoute-kretsar har statusen etablerad.
* Kontrollera att Azures privata peering har konfigurerats på ExpressRoute-kretsar.  

### <a name="log-into-your-azure-account"></a>Logga in på ditt Azure-konto
Om du vill starta konfigurationen, måste du logga in ditt Azure-konto. 

Öppna PowerShell-konsolen med utökad behörighet och anslut till ditt konto. Kommandot uppmanar dig för autentiseringsuppgifter för inloggning för dina Azure-konto.  

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
Du kan aktivera ExpressRoute Global räckvidd mellan två ExpressRoute-kretsar så länge som de finns i de länder/regioner som stöds och de skapas vid en annan peering-platser. Om din prenumeration äger både kretsar kan du välja antingen krets för att köra konfigurationen i avsnitten nedan. Om två kretsar finns i olika Azure-prenumerationer, kommer du behöver tillstånd från en Azure-prenumeration och skicka in auktoriseringsnyckeln när du kör kommandot konfiguration i andra Azure-prenumeration.

## <a name="enable-connectivity-between-your-on-premises-networks"></a>Aktivera anslutning mellan ditt lokala nätverk

Använd följande kommandon för att hämta krets 1 och 2-kretsen. Två kretsar är i samma prenumeration.

```powershell
$ckt_1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
$ckt_2 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
```

Kör följande kommando mot krets 1 och skicka in krets 2 privat peering's-Id.

> [!NOTE]
> Privat peering är Id ut */subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}/peerings/AzurePrivatePeering*
> 
>

```powershell
Add-AzureRmExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering $ckt_2.Peerings[0].Id -AddressPrefix '__.__.__.__/29'
```

> [!IMPORTANT]
> *-AddressPrefix* måste vara ett/29 IPv4-undernät, t.ex. ”10.0.0.0/29”. Vi ska använda IP-adresser i det här undernätet för att upprätta en anslutning mellan de två ExpressRoute-kretsarna. Du måste inte använda adresser i det här undernätet i ditt virtuella Azure-nätverk eller i ditt lokala nätverk.
> 



Spara konfigurationen på kretsen 1
```powershell
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

När åtgärden ovan är klar bör du ha anslutning mellan ditt lokala nätverk på båda sidorna via två ExpressRoute-kretsarna.

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>ExpressRoute-kretsar i olika Azure-prenumerationer

Om två kretsar inte finns i samma Azure-prenumeration, måste auktorisering. I följande konfiguration auktorisering genereras i kretsen 2-prenumeration och auktoriseringsnyckeln skickas till krets 1.

Generera en auktoriseringsnyckel. 
```powershell
$ckt_2 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $ckt_2 -Name "Name_for_auth_key"
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_2
```
Anteckna krets 2 privat peering's Id samt auktoriseringsnyckeln.

Kör följande kommando mot krets 1. Skicka in krets 2 privat peering's Id och auktoriseringsnyckel 
```powershell
Add-AzureRmExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering "circuit_2_private_peering_id" -AddressPrefix '__.__.__.__/29' -AuthorizationKey '########-####-####-####-############'
```

Spara konfigurationen på kretsen 1
```powershell
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

När åtgärden ovan är klar bör du ha anslutning mellan ditt lokala nätverk på båda sidorna via två ExpressRoute-kretsarna.

## <a name="get-and-verify-the-configuration"></a>Hämta och verifiera konfigurationen

Använder du följande kommando för att kontrollera konfigurationen på kretsen där konfigurationen har gjorts, d.v.s. krets 1 i exemplet ovan.

```powershell
$ckt1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
```

Om du kör bara *$ckt1* i PowerShell som du ser *CircuitConnectionStatus* i utdata. Det informerar dig om anslutningen har upprättats ”ansluten”, eller inte, ”frånkopplad”. 

## <a name="disable-connectivity-between-your-on-premises-networks"></a>Inaktivera anslutning mellan ditt lokala nätverk

Om du vill inaktivera det, kör du kommandon mot kretsen där konfigurationen har gjorts, d.v.s. krets 1 i exemplet ovan.

```powershell
$ckt1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
Remove-AzureRmExpressRouteCircuitConnectionConfig -Name "Your_connection_name" -ExpressRouteCircuit $ckt_1
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

Du kan köra Get-åtgärden för att kontrollera statusen. 

När åtgärden ovan är klar kan har du inte längre anslutning mellan ditt lokala nätverk via ExpressRoute-kretsar. 


## <a name="next-steps"></a>Nästa steg
1. [Mer information om ExpressRoute Global räckvidd](expressroute-global-reach.md)
2. [Kontrollera ExpressRoute-anslutningen](expressroute-troubleshooting-expressroute-overview.md)
3. [Länka ExpressRoute-krets till Azure-nätverk](expressroute-howto-linkvnet-arm.md)


