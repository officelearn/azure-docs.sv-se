---
title: "Azure ExpressRoute-kretsar och routningsdomäner | Microsoft Docs"
description: "Den här sidan innehåller en översikt över ExpressRoute-kretsar och routningsdomänerna."
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: 
ms.assetid: 6f0c5d8e-cc60-4a04-8641-2c211bda93d9
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/01/2018
ms.author: ganesr,cherylmc
ms.openlocfilehash: 943305c78a17031d647bf2fa0977d10c51213ef5
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="expressroute-circuits-and-routing-domains"></a>ExpressRoute-kretsar och routningsdomäner
 Måste du sortera en *ExpressRoute-krets* att ansluta din lokala infrastruktur till Microsoft via en anslutning provider. Följande bild visar en logisk representation av anslutningen mellan din WAN och Microsoft.

![](./media/expressroute-circuit-peerings/expressroute-basic.png)

## <a name="expressroute-circuits"></a>ExpressRoute-kretsar
En *ExpressRoute-krets* representerar en logisk anslutning mellan din lokala infrastruktur och dina Microsoft-molntjänster med en anslutning. Du kan ordna flera ExpressRoute-kretsar. Varje krets kan vara i samma eller olika regioner och kan vara ansluten till din lokala via olika anslutningen leverantörer. 

ExpressRoute-kretsar mappas inte till alla fysiska enheter. En anslutning identifieras unikt med en standard GUID kallas för en nyckel (s-nyckel). Nyckeln för tjänsten är enda information som utbyts mellan Microsoft provider för anslutningen och du. S-nyckeln är inte en hemlighet av säkerhetsskäl. Det finns en 1:1-mappning mellan en ExpressRoute-krets och s-nyckel.

En ExpressRoute-krets kan ha upp till tre oberoende: Azure offentlig, Azure privata och Microsoft. Varje peering är två oberoende BGP sessioner av dem redundant konfigureras för hög tillgänglighet. Det finns en 1: n (1 < = N < = 3) mappning mellan en ExpressRoute-krets och routning domäner. En ExpressRoute-krets kan ha en, två eller alla tre peerkopplingar aktiveras per ExpressRoute-kretsen.

Varje kretsen har en fast bandbredd (50 Mbit/s, 100 Mbit/s, 200 Mbit/s, 500 Mbit/s, 1 Gbit/s, 10 Gbit/s) och mappas till en provider för anslutningen och en peering plats. Den bandbredd som du väljer delas mellan alla peerkopplingar för kretsen. 

### <a name="quotas-limits-and-limitations"></a>Kvoter, gränser och begränsningar
Standardkvoter och gränser gäller för alla ExpressRoute-kretsen. Referera till den [Azure-prenumeration och tjänsten gränser, kvoter och begränsningar](../azure-subscription-service-limits.md) för aktuell information om kvoter.

## <a name="expressroute-routing-domains"></a>ExpressRoute-routningsdomäner
En ExpressRoute-krets har flera routningsdomäner associerad: Azure offentlig, Azure privata och Microsoft. Var och en av routningsdomänerna har konfigurerats identiskt på ett par av routrar (aktiv-aktiv eller läsa in delar configuration) för hög tillgänglighet. Azure-tjänster är kategoriserade som *Azure offentliga* och *Azure privata* som representerar den IP-adressering scheman.

![](./media/expressroute-circuit-peerings/expressroute-peerings.png)

### <a name="azure-private-peering"></a>Azure privat peering
Azure compute tjänster, nämligen virtuella datorer (IaaS) och molntjänster (PaaS), som har distribuerats i ett virtuellt nätverk kan vara ansluten via privat peering domänen. Privat peering domänen betraktas som en betrodd förlängning av Kärnnätverket i Microsoft Azure. Du kan ställa in dubbelriktad anslutning mellan ditt core nätverk och virtuella Azure-nätverk (Vnet). Den här peering kan du ansluta till virtuella datorer och molntjänster direkt på sina privata IP-adresser.  

Du kan ansluta flera virtuella nätverk till domänen för privat peering. Granska de [vanliga frågor om sidan](expressroute-faqs.md) information om gränser och begränsningar. Du kan besöka den [Azure-prenumeration och tjänsten gränser, kvoter och begränsningar](../azure-subscription-service-limits.md) sidan uppdaterad information om begränsar.  Referera till den [routning](expressroute-routing.md) för detaljerad information om routningskonfiguration.

### <a name="azure-public-peering"></a>Offentlig Azure-peering

> [!IMPORTANT]
> Alla Azure PaaS-tjänster är också tillgängliga via Microsoft-peering. Vi rekommenderar att du skapar Microsoft-peering och ansluter till Azure PaaS-tjänster över Microsoft-peering.  
>   


Tjänster som Azure Storage, SQL-databaser och webbplatser erbjuds på offentliga IP-adresser. Privat kan du ansluta till tjänster som ligger på den offentliga IP-adresser, inklusive VIP av dina molntjänster genom offentlig peering routningsdomän. Du kan ansluta offentlig peering domänen till din DMZ och ansluta till alla Azure-tjänster på sina offentliga IP-adresser från din WAN utan att behöva ansluta via internet. 

Anslutningen är alltid initieras från din WAN till Microsoft Azure-tjänster. Microsoft Azure-tjänster kommer inte att initiera anslutningar till nätverket via den här routningsdomän. När offentlig peering är aktiverat kan ansluta du till alla Azure-tjänster. Vi kan inte du selektivt Välj tjänster som vi annonserar vägar till.

Du kan definiera filter för anpassad routning i nätverket att använda de vägar som du behöver. Referera till den [routning](expressroute-routing.md) för detaljerad information om routningskonfiguration. 

Mer information om tjänster som stöds via offentlig peering routningsdomän finns i [vanliga frågor och svar](expressroute-faqs.md).

### <a name="microsoft-peering"></a>Microsoft-peering
[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

Anslutningen till Microsoft online services (Office 365, Dynamics 365 och Azure PaaS services) är via Microsoft-peering. Vi Aktivera dubbelriktad anslutning mellan dina WAN och Microsoft-molntjänster via Microsoft peering routningsdomän. Du måste ansluta till Microsoft-molntjänster endast med offentliga IP-adresser som ägs av dig eller anslutningsleverantören och du måste följa de definierade reglerna. Mer information finns i [ExpressRoute krav](expressroute-prerequisites.md) sidan.

Finns det [vanliga frågor om sidan](expressroute-faqs.md) för mer information om tjänster som stöds, kostnader och konfigurationsinformation. Finns det [ExpressRoute platser](expressroute-locations.md) sidan information om listan över anslutningen leverantörer som erbjuder peering Microsoft-supporten.

## <a name="routing-domain-comparison"></a>Routning domän jämförelse
I följande tabell jämförs de tre routningsdomänerna:

|  | **Privat Peering** | **Offentlig Peering** | **Microsoft-Peering*** |
| --- | --- | --- | --- |
| **Max. # prefix stöd per peering** |4000 som standard 10 000 med ExpressRoute Premium |200 |200 |
| **IP-adressintervall som stöds** |En giltig IP-adress inom din WAN. |Offentliga IP-adresser som ägs av dig eller anslutningsleverantören. |Offentliga IP-adresser som ägs av dig eller anslutningsleverantören. |
| **SOM antal krav** |Privata och offentliga som tal. Du måste äga allmänheten som tal om du väljer att använda en. |Privata och offentliga som tal. Du måste dock bevisa ägarskapet för offentliga IP-adresser. |Privata och offentliga som tal. Du måste dock bevisa ägarskapet för offentliga IP-adresser. |
| **IP-protokoll som stöds**| IPv4 | IPv4 | IPv4, IPv6 |
| **Routing gränssnitts-IP-adresser** |RFC1918 och offentliga IP-adresser |Offentliga IP-adresser som registrerad i Routning register. |Offentliga IP-adresser som registrerad i Routning register. |
| **MD5-Hash-support** |Ja |Ja |Ja |

(*) Kräver premiumnivån tillägg SKU

Du kan välja att aktivera en eller flera av routningsdomänerna som en del av ExpressRoute-kretsen. Du kan välja att ha alla routningsdomäner spärra samma VPN om du vill kombinera dem till en enda routningsdomän. Du kan också publicera dem på olika routningsdomäner liknar diagrammet. Den rekommenderade konfigurationen är att privat peering är direkt ansluten till Kärnnätverket och offentliga och Microsoft peering länkar är anslutna till ditt Perimeternätverk.

Om du väljer att alla tre peeringsessioner, måste du ha tre par BGP-sessioner (ett par för varje typ av peering). BGP-sessionen par ger en hög tillgänglighet länk. Om du ansluter via layer 2 anslutningen leverantörer ansvarar du för att konfigurera och hantera routning. Du kan lära dig mer genom att granska den [arbetsflöden](expressroute-workflows.md) för att ställa in ExpressRoute.

## <a name="next-steps"></a>Nästa steg
* Hitta en tjänstleverantör. Se [ExpressRoute service providers och platser](expressroute-locations.md).
* Kontrollera att alla krav är uppfyllda. Se [ExpressRoute-krav](expressroute-prerequisites.md).
* Konfigurera ExpressRoute-anslutningen.
  * [Skapa och hantera ExpressRoute-kretsar](expressroute-howto-circuit-portal-resource-manager.md)
  * [Konfigurera routning (peering) för ExpressRoute-kretsar](expressroute-howto-routing-portal-resource-manager.md)

