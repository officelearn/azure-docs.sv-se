---
title: Global räckvidd för Azure ExpressRoute | Microsoft Docs
description: Den här artikeln hjälper dig att länka ExpressRoute-kretsar tillsammans för att göra ett privat nätverk mellan ditt lokala nätverk och aktivera Global räckvidd.
documentationcenter: na
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: cherylmc
ms.openlocfilehash: 90f34da10e0193e31a0f70187463799013f52be8
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46966823"
---
# <a name="link-expressroute-circuits-to-enable-expressroute-global-reach-preview"></a>Länka ExpressRoute-kretsar för att aktivera ExpressRoute Global räckvidd (förhandsversion)

ExpressRoute är en privat och flexibel sätt att ansluta ditt lokala nätverk till Microsoft Cloud. Du kan komma åt många Microsoft-molntjänster som Azure, Office 365 och Dynamics 365 från ditt privata Datacenter eller företagets nätverk. Du kan till exempel ha ett avdelningskontor i San Francisco med en ExpressRoute-krets i Silicon Valley och ett annat filialkontor i Baltimore med en ExpressRoute-krets i Washington DC. 

Båda avdelningskontor kan ha höghastighetsanslutning till Azure-resurser i både östra USA och västra USA. Men det går inte att filialkontor utbyta data direkt med varandra. Med andra ord utbyta 10.0.1.0/24 data med 10.0.3.0/24 och 10.0.4.0/24, men inte med 10.0.2.0/24.

![utan][1]

Med **ExpressRoute Global räckvidd**, du kan länka ExpressRoute-kretsar tillsammans för att göra ett nätverk för privata data mellan ditt lokala nätverk. I exemplet ovan med hjälp av ExpressRoute Global räckvidd, utbyta San Francisco kontoret (10.0.1.0/24) direkt data med dina Baltimore office (10.0.2.0/24) via de befintliga ExpressRoute-kretsarna och via Microsofts globala nätverk. 

![med][2]

ExpressRoute Global räckvidd stöds för närvarande i följande länder:

* USA
* Storbritannien 
* Japan

ExpressRoute-kretsarna måste skapas på den [ExpressRoute-peeringplatser](expressroute-locations.md) i dessa länder. Så här aktiverar du ExpressRoute Global räckvidd mellan [olika geopolitiska regioner](expressroute-locations.md), din kretsar måste vara Premium-SKU.


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
Du kan aktivera ExpressRoute Global räckvidd mellan två ExpressRoute-kretsar så länge som de finns i de länder/regioner som stöds och de skapas vid en annan peering-platser. Om din prenumeration äger både kretsar kan välja du antingen krets för att köra konfigurationen i avsnitten nedan. Om två kretsar finns i olika Azure-prenumerationer, kommer du behöver tillstånd från en Azure-prenumeration och skicka in auktoriseringsnyckeln när du kör kommandot konfiguration i andra Azure-prenumeration.

## <a name="to-enable-connectivity-between-your-on-premises-networks"></a>Genom att aktivera anslutning mellan ditt lokala nätverk

Använd följande kommandon för att hämta krets 1 och 2-kretsen. Två kretsar är i samma prenumeration.

```powershell
$ckt_1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
$ckt_2 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
```

Kör följande kommando mot krets 1 och skicka in krets 2 privat peering's-ID.

Observera att den privata peering's Id ska se ut som:

`/subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}/peerings/AzurePrivatePeering`


```powershell
Add-AzureRmExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering $ckt_2.Peerings[0].Id -AddressPrefix '__.__.__.__/29'
```

Den *- AddressPrefix* måste vara ett/29 IPv4-undernät, till exempel ”10.0.0.0/29”. Vi ska använda IP-adresser i det här undernätet för att upprätta en anslutning mellan de två ExpressRoute-kretsarna. Du bör inte använda adresser i det här undernätet i ditt virtuella Azure-nätverk eller i ditt lokala nätverk. 


Spara konfigurationen på kretsen 1.

```powershell
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

När den föregående åtgärden har slutförts bör du ha anslutning mellan ditt lokala nätverk på båda sidorna via två ExpressRoute-kretsarna.

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>ExpressRoute-kretsar i olika Azure-prenumerationer

Om två kretsar inte finns i samma Azure-prenumeration, måste auktorisering. I följande konfiguration auktorisering genereras i kretsen 2-prenumeration och auktoriseringsnyckeln skickas till krets 1.

Generera en auktoriseringsnyckel. 
```powershell
$ckt_2 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $ckt_2 -Name "Name_for_auth_key"
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_2
```
Anteckna krets 2 privat peering's ID, samt auktoriseringsnyckeln.

Kör följande kommando mot krets 1. Skicka in krets 2 privat peering's ID och auktoriseringsnyckel

```powershell
Add-AzureRmExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering "circuit_2_private_peering_id" -AddressPrefix '__.__.__.__/29' -AuthorizationKey '########-####-####-####-############'
```

Spara konfigurationen på kretsen 1.

```powershell
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

När de tidigare åtgärderna har slutförts bör du ha anslutning mellan ditt lokala nätverk på båda sidorna via två ExpressRoute-kretsarna.

## <a name="to-get-and-verify-the-configuration"></a>Skaffa och verifiera konfigurationen

Använder du följande kommando för att kontrollera konfigurationen på kretsen där konfigurationen gjordes. Det här exemplet använder kretsen 1 från föregående exempel.

```powershell
$ckt1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
```

Om du kör bara *$ckt1* i PowerShell kan du se *CircuitConnectionStatus* i utdata. Det informerar dig om anslutningen har upprättats ”ansluten”, eller inte, ”frånkopplad”. 

## <a name="to-disable-connectivity-between-your-on-premises-networks"></a>Att inaktivera anslutningen mellan ditt lokala nätverk

Om du vill inaktivera, kör du kommandon mot kretsen där konfigurationen gjordes. Det här exemplet använder kretsen 1 från föregående exempel.

```powershell
$ckt1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
Remove-AzureRmExpressRouteCircuitConnectionConfig -Name "Your_connection_name" -ExpressRouteCircuit $ckt_1
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

Du kan köra Get-åtgärden för att kontrollera statusen. 

När åtgärden ovan är klar kan har du inte längre anslutning mellan ditt lokala nätverk via ExpressRoute-kretsar. 

## <a name="next-steps"></a>Nästa steg
1. [Mer information om ExpressRoute Global räckvidd](expressroute-faqs.md#globalreach)
2. [Kontrollera ExpressRoute-anslutningen](expressroute-troubleshooting-expressroute-overview.md)
3. [Länka ExpressRoute-krets till Azure-nätverk](expressroute-howto-linkvnet-arm.md)


<!--Image References-->
[1]: ./media/expressroute-global-reach/1.png "diagram utan global räckvidd"
[2]: ./media/expressroute-global-reach/2.png "diagram med global räckvidd"