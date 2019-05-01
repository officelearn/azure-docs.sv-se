---
title: Azure ExpressRoute-kretsar och peering | Microsoft Docs
description: Den här sidan innehåller en översikt över ExpressRoute-kretsar och routning domäner/peering.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: mialdrid
ms.custom: seodec18
ms.openlocfilehash: c4290473a7c1edce02d74a4a787c62ccf0d9c052
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64924314"
---
# <a name="expressroute-circuits-and-peering"></a>ExpressRoute-kretsar och peering

ExpressRoute-kretsar ansluter din lokala infrastruktur till Microsoft via en anslutningsleverantör. Den här artikeln hjälper dig att förstå ExpressRoute-kretsar och routning domäner/peering. Följande bild visar en logisk representation av anslutningen mellan ditt WAN-nätverk och Microsoft.

![](./media/expressroute-circuit-peerings/expressroute-basic.png)

> [!IMPORTANT]
> Azures offentliga peering har gjorts inaktuell och är inte tillgänglig för nya ExpressRoute-kretsar. Ny kretsar stöd för Microsoft-peering och privat peering.  
>

## <a name="circuits"></a>ExpressRoute-kretsar

En ExpressRoute-krets representerar en logisk anslutning mellan din lokala infrastruktur och Microsofts molntjänster via en anslutningsleverantör. Du kan beställa flera ExpressRoute-kretsar. Varje krets kan finnas i samma eller olika regioner och kan vara ansluten till ditt lokala nätverk via olika anslutningsleverantörer.

ExpressRoute-kretsar mappas inte till alla fysiska enheter. En krets identifieras unikt genom en standard som GUID kallas för en Tjänstnyckel (s-nyckel). Tjänstnyckeln är enda information som utbyts mellan Microsoft anslutningsleverantören och du. S-nyckeln är inte en hemlighet av säkerhetsskäl. Det finns en 1:1-mappning mellan en ExpressRoute-krets och s-nyckel.

Nya ExpressRoute-kretsar kan innehålla två oberoende peerings: Privat peering och Microsofts peering. Medan befintliga ExpressRoute-kretsar kan innehålla tre peerings: Azure offentlig, privat Azure och Microsoft. Varje peering är två oberoende BGP-sessioner, var och en av dem redundant konfigureras för hög tillgänglighet. Det finns en 1: n (1 < = N < = 3) mappning mellan en ExpressRoute-krets och routning domäner. En ExpressRoute-krets kan ha en, två eller alla tre peerings aktiveras per ExpressRoute-krets.

Varje krets har en fast bandbredd (50 Mbit/s, 100 Mbit/s, 200 Mbit/s, 500 Mbit/s, 1 Gbit/s, 10 Gbit/s) och mappas till en anslutningsprovider och en peering-plats. Den bandbredd som du väljer delas mellan alla krets peer-kopplingar

### <a name="quotas"></a>Kvoter, gränser och begränsningar

Standardkvoter och begränsningar gäller för varje ExpressRoute-krets. Referera till den [Azure-prenumeration och tjänstbegränsningar, kvoter och begränsningar](../azure-subscription-service-limits.md) för uppdaterad information om kvoter.

## <a name="routingdomains"></a>ExpressRoute-peering

En ExpressRoute-krets har flera routning domäner/peerings kopplade till den: Azure offentliga, Azures privata och Microsoft. Varje peering har konfigurerats identiskt på två routrar (i aktiv-aktiv eller dela belastningen konfiguration) för hög tillgänglighet. Azure-tjänster som kategoriseras som *Azures offentliga* och *privat Azure* som motsvarar den IP-adresser scheman.

![](./media/expressroute-circuit-peerings/expressroute-peerings.png)

### <a name="privatepeering"></a>Azures privata peering

Azure-Beräkningstjänster, nämligen virtuella datorer (IaaS) och molntjänster (PaaS), som distribueras inom ett virtuellt nätverk kan anslutas via privata peering domänen. Privat peering domänen anses vara en betrodd utökning av ditt kärnnätverk i Microsoft Azure. Du kan ställa in dubbelriktad anslutning mellan ditt core nätverk och Azure-nätverk (Vnet). Denna peering kan du ansluta till virtuella datorer och molntjänster direkt på sina privata IP-adresser.  

Du kan ansluta flera virtuella nätverk till privat peering domänen. Granska den [FAQ-sida](expressroute-faqs.md) information om gränser och begränsningar. Du kan gå till den [Azure-prenumeration och tjänstbegränsningar, kvoter och begränsningar](../azure-subscription-service-limits.md) för uppdaterad information om gränser.  Referera till den [routning](expressroute-routing.md) för detaljerad information om routningskonfiguration.

### <a name="microsoftpeering"></a>Microsoft-peering

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

Anslutning till Microsofts onlinetjänster (Office 365, Dynamics 365 och Azure PaaS-tjänster) sker via Microsoft-peering. Vi Aktivera dubbelriktad anslutning mellan ditt WAN-nätverk och Microsofts molntjänster via Microsoft peering routningsdomän. Du måste ansluta till Microsofts molntjänster endast med offentliga IP-adresser som ägs av dig eller din anslutningsleverantör och du måste följa alla definierade regler. Mer information finns i den [ExpressRoute-krav](expressroute-prerequisites.md) sidan.

Se den [FAQ-sida](expressroute-faqs.md) för mer information om tjänster som stöds, kostnader och konfigurationsinformation. Se den [ExpressRoute-platser](expressroute-locations.md) för information om listan över anslutningsleverantörer erbjuder Microsoft peering support.

### <a name="publicpeering"></a>Azures offentliga peering (föråldrad för nya kretsar)

> [!Note]
> Azures offentliga peering har 1 NAT IP-adress som är kopplade till varje BGP-sessionen. Större än 2 NAT IP-adresser, flytta till Microsoft-peering. Microsoft-peering kan du konfigurera din egen NAT-allokeringar, samt använda flödesfilter för selektiv prefix annonser. Mer information finns i [flytta till Microsoft-peering](https://docs.microsoft.com/azure/expressroute/how-to-move-peering).
>

Tjänster som Azure Storage, SQL-databaser och webbplatser erbjuds på offentliga IP-adresser. Privat kan du ansluta till tjänster som finns i den offentliga IP-adresser, inklusive virtuella IP-adresser för cloud services, via offentlig peering routningsdomän. Du kan ansluta den offentliga peering domänen till din DMZ och ansluta till alla Azure-tjänster på deras offentliga IP-adresser från ditt WAN-nätverk utan att behöva ansluta via internet.

Anslutningen initieras alltid från ditt WAN-nätverk till Microsoft Azure-tjänster. Microsoft Azure-tjänster kommer inte att kunna initiera anslutningar till nätverket via den här routningsdomän. När offentlig peering är aktiverat kan ansluta du till alla Azure-tjänster. Vi kan inte du selektivt välja tjänster som vi annonsera vägar till.

Du kan definiera anpassade vägfilter i nätverket för att använda de vägar som du behöver. Referera till den [routning](expressroute-routing.md) för detaljerad information om routningskonfiguration.

Mer information om tjänster som stöds via offentlig peering routningsdomän finns i den [vanliga frågor och svar](expressroute-faqs.md).

## <a name="peeringcompare"></a>Peering jämförelse

I följande tabell jämförs de tre peerings:

|  | **Privat Peering** | **Microsoft-Peering** |  **Offentlig Peering** (inaktuellt för nya kretsar) |
| --- | --- | --- | --- |
| **Max. # prefix som stöds per peering** |4000 som standard 10 000 med ExpressRoute Premium |200 |200 |
| **IP-adressintervall som stöds** |Alla giltiga IP-adress inom ditt WAN. |Offentliga IP-adresser som ägs av dig eller din anslutningsleverantör. |Offentliga IP-adresser som ägs av dig eller din anslutningsleverantör. |
| **SOM antal krav** |Privata och offentliga AS-nummer. Du måste äga allmänheten som tal om du väljer att använda en. |Privata och offentliga AS-nummer. Du måste dock bevisa ägarskapet för offentliga IP-adresser. |Privata och offentliga AS-nummer. Du måste dock bevisa ägarskapet för offentliga IP-adresser. |
| **IP-protokoll som stöds**| IPv4 |  IPv4, IPv6 | IPv4 |
| **Routing gränssnitts-IP-adresser** |RFC1918 och offentliga IP-adresser |Offentliga IP-adresser registrerade åt dig i routningsregister. |Offentliga IP-adresser registrerade åt dig i routningsregister. |
| **MD5-Hash-stöd** |Ja |Ja |Ja |

Du kan aktivera en eller flera av routningsdomänerna som en del av ExpressRoute-kretsen. Du kan välja att ha alla routningsdomäner placera samma VPN-anslutning om du vill kombinera dem i en enda routningsdomän. Du kan också publicera dem på olika routningsdomäner liknar diagrammet. Den rekommenderade konfigurationen är att privat peering är ansluten direkt till Kärnnätverket och offentliga och Microsoft peering-länkar som är anslutna till din DMZ.

Varje peering kräver separat BGP-sessioner (ett par för varje typ av peering). BGP-sessionen par tillhandahåller en högtillgänglig länk. Om du ansluter via layer 2-anslutningsleverantörer, ansvarar du för att konfigurera och hantera routning. Du kan lära dig mer genom att granska den [arbetsflöden](expressroute-workflows.md) för att konfigurera ExpressRoute.

## <a name="health"></a>ExpressRoute-hälsa

ExpressRoute-kretsar kan övervakas för tillgänglighet, anslutning till virtuella nätverk och bandbredd användning med hjälp av [Övervakare av nätverksprestanda](https://docs.microsoft.com/azure/networking/network-monitoring-overview) (NPM).

NPM övervakar hälsotillståndet för Azures privata peering och Microsofts peering. Kolla in våra [publicera](https://azure.microsoft.com/blog/monitoring-of-azure-expressroute-in-preview/) för mer information.

## <a name="next-steps"></a>Nästa steg

* Hitta en tjänstleverantör. Se [ExpressRoute-tjänsten leverantörer och platser](expressroute-locations.md).
* Kontrollera att alla krav är uppfyllda. Se [ExpressRoute-krav](expressroute-prerequisites.md).
* Konfigurera ExpressRoute-anslutningen.
  * [Skapa och hantera ExpressRoute-kretsar](expressroute-howto-circuit-portal-resource-manager.md)
  * [Konfigurera routning (peering) för ExpressRoute-kretsar](expressroute-howto-routing-portal-resource-manager.md)
