---
title: Azure virtuellt WAN partners platser | Microsoft Docs
description: Den här artikeln innehåller en lista över Azure virtuella WAN-partner och hub platser
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect find a Virtual WAN partner
ms.openlocfilehash: 76542392522ff14642ed7ccc6de8ed543d66513f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46985001"
---
# <a name="virtual-wan-partners-and-virtual-hub-locations"></a>Virtuella WAN-partner och virtuella hubben platser

Den här artikeln innehåller information om virtuellt WAN-nätverk stöds regioner och önskad partner för anslutning till virtuell hubb.

Azure Virtual WAN är en nätverkstjänst som tillhandahåller optimerade och automatiserade anslutningar mellan olika platser via Azure. Med Virtual WAN kan du ansluta och konfigurera platsspecifika enheter så att de kommunicerar med Azure. Detta kan göras antingen manuellt eller genom att använda önskade providern enheter via ett virtuellt WAN-nätverk samarbetspartner. Med samarbetspartner enheter kan du underlätta för förenkling anslutnings-och konfigurationshantering. Upprätta en anslutning från den lokala enheten är på ett automatiserat sätt till den virtuella hubben. En virtuell hubb är ett virtuellt nätverk med Microsoft-hanterad. Navet innehåller olika tjänstslutpunkter för anslutning från ditt lokala nätverk (vpnsite). Du kan bara ha en hubb per region.

## <a name="regions"></a>Regioner

Listan över regioner som stöds och är tillgängliga är följande:

|Geopolitisk region | Azure-regioner|
|---|---|
|Nordamerika | USA, östra; USA, västra; USA, östra 2; USA, västra 2; USA, centrala; USA, södra centrala; USA, norra centrala; USA västra centrala; Kanada, centrala; Kanada, östra |
|Sydamerika |Södra Brasilien |
| Europa | Frankrike, centrala; Frankrike, södra; Europa, norra; Europa, västra; Storbritannien, västra; Storbritannien, södra |
| Asien | Östra Asien, Sydostasien |
| Japan  | Västra Japan, östra Japan |
| Australien | Sydöstra Australien, östra Australien | 
| Myndigheter i Australien | Centrala Australien, Centrala Australien 2 |
| Indien | Västra Indien, centrala Indien, södra Indien |
| Sydkorea | Sydkorea, centrala; Sydkorea, södra | 

## <a name="automation-from-connectivity-partners"></a>Automation från anslutningspartners

Det här avsnittet beskriver vad på hög nivå av automatisering från anslutningsleverantörer.

Enheter som ansluter till Azure virtuellt WAN-nätverk har inbyggda automation för att ansluta. Detta anges vanligtvis upp i enhetshantering Gränssnittet (eller motsvarande), som konfigurerar anslutning och konfiguration hantering mellan VPN-enhet till en Azure virtuell hubb VPN-slutpunkt (VPN-gateway) för grenen.

Följande övergripande automation har ställts in i enheten/hantering av en konsol center:

* Behörighet för enheten att få åtkomst till Azure virtuella WAN-resursgrupp
* Ladda upp av gren enhet till Azure virtuellt WAN
* Automatisk nedladdning av Azure anslutningsinformation 
* Konfigurationen för den lokala grenen enhet 

Vissa anslutningar-partner kan utöka automatisering som omfattar att skapa virtuella Azure-Hubbnätverk och VPN-Gateway. Om du vill veta mer om automation [konfigurera Automation – WAN partner](virtual-wan-configure-automation-providers.md).

## <a name="connectivity-through-preferred-partners"></a>Anslutningar via önskad partner

Om partnern gren enheten inte visas i avsnittet nedan, kontakta din provider för grenen och de kontakta oss via e-post till azurevirtualwan@microsoft.com.

Du kan kontrollera följande länkar för att samla in mer information om tjänsterna som erbjuds av de önskade partner. 

|Önskad partner|
|---|
|[Barracuda Networks](https://www.barracuda.com/AzurevWAN)|
| [Check Point](https://www.checkpoint.com/solutions/microsoft-azure-virtual-wan/) |
| [Citrix](https://www.citrix.com/global-partners/microsoft/sd-wan-for-azure-virtual-wan.html)|
|[Netfoundry](https://netfoundry.io/solutions/netfoundry-for-microsoft-azure-virtual-wan/)|
|[Palo Alto Networks](https://researchcenter.paloaltonetworks.com/2018/09/azure-vwan-integration/) |
|[Riverbed teknik](https://www.riverbed.com/go/steelconnect-azurewan.html)|
|[128-teknik](https://www.128technology.com/partners/azure) |

## <a name="next-steps"></a>Nästa steg

Mer information om virtuellt WAN-nätverk finns i den [virtuellt WAN vanliga frågor och svar](virtual-wan-faq.md).

Mer information om hur du automatiserar anslutning till Azure virtuellt WAN-nätverk finns i [virtuella WAN-partner - automatisera](virtual-wan-configure-automation-providers.md).
