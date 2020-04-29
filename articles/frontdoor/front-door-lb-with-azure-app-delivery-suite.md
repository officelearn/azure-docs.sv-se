---
title: Azure frontend-belastnings utjämning med Azures program leverans Suite | Microsoft Docs
description: Den här artikeln hjälper dig att lära dig hur Azure rekommenderar belastnings utjämning med sin Application Delivery Suite
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 44af14a01e7b045b7abb6a84db89a67f3dd22445
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80875290"
---
# <a name="load-balancing-with-azures-application-delivery-suite"></a>Belastningsutjämning med Azures paket för programleverans

## <a name="introduction"></a>Introduktion
Microsoft Azure tillhandahåller flera globala och regionala tjänster för att hantera hur nätverks trafiken distribueras och belastnings utjämning: Traffic Manager, front dörr, Application Gateway och Load Balancer.  Tillsammans med Azures många regioner och zonindelade-arkitektur kan du med hjälp av dessa tjänster skapa robusta och skalbara program med höga prestanda.

![Application Delivery Suite ][1]
 
Dessa tjänster är uppdelade i två kategorier:
1. **Globala belastnings Utjämnings tjänster** som Traffic Manager och front dörr distribuerar trafik från dina slutanvändare över dina regionala Server delar, över moln eller till och med dina hybrid lokala tjänster. Global belastnings utjämning dirigerar trafiken till närmaste tjänst Server del och reagerar på ändringar i tjänstens tillförlitlighet eller prestanda för att upprätthålla Always-on-maximala prestanda för dina användare. 
2. **Regionala belastnings Utjämnings tjänster** som Standard Load Balancer eller Application Gateway ger möjlighet att distribuera trafik inom virtuella nätverk (virtuella nätverk) över dina virtuella datorer (VM) eller zonindelade-tjänstens slut punkter inom en region.

Genom att kombinera globala och regionala tjänster i programmet får du ett tillförlitligt, tillförlitligt och säkert sätt att dirigera trafik till och från dina användare till dina IaaS, PaaS eller lokala tjänster. I nästa avsnitt beskriver vi var och en av dessa tjänster.

## <a name="global-load-balancing"></a>Global belastnings utjämning
**Traffic Manager** tillhandahåller global belastnings utjämning för DNS. Den söker efter inkommande DNS-begäranden och svarar med en felfri Server del, i enlighet med Routningsprincipen som kunden har valt. Alternativen för routningsmetoder är:
- Routning av prestanda för att skicka begär anden till den närmaste server delen vad gäller svars tid.
- Prioritera routning för att dirigera all trafik till en server del med andra server delar som säkerhets kopiering.
- Viktad resursallokering, som distribuerar trafik baserat på den viktning som tilldelas varje server del.
- Geografisk routning för att säkerställa att beställare som finns i specifika geografiska regioner dirigeras till de Server delar som mappas till dessa regioner (till exempel måste alla förfrågningar från Spanien dirigeras till den franska centrala Azure-regionen)
- Under näts dirigering som gör att du kan mappa IP-adressintervall till Server delar så att begär Anden som kommer från dem skickas till den angivna Server delen (till exempel att alla användare som ansluter från företagets HQ IP-adressintervall får annat webb innehåll än allmänna användare)

Klienten ansluter direkt till Server delen. Azure Traffic Manager identifierar när en server del inte är felfri och omdirigerar sedan klienterna till en annan felfria instans. Mer information om tjänsten hittar du i [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) -dokumentationen.

**Azures front dörr** tillhandahåller en dynamisk webbplats acceleration (DSA), inklusive global http-belastnings utjämning.  Det ser till att inkommande HTTP-begäranden dirigeras till närmaste tjänst Server del/region för angivet värdnamn, URL-sökväg och konfigurerade regler.  
Frontend-dörren avslutar HTTP-begäranden på kanten av Microsofts nätverk och aktivt avsökningar för att identifiera program-eller infrastrukturens hälso tillstånd eller fördröjnings ändringar.  Front dörren dirigerar sedan alltid trafiken till Server delen snabbast och tillgänglig (felfri). Läs mer om tjänsten [routnings arkitektur](front-door-routing-architecture.md) och [trafikroutnings metoder](front-door-routing-methods.md) om du vill veta mer om tjänsten.

## <a name="regional-load-balancing"></a>Regional belastnings utjämning
Application Gateway tillhandahåller program leverans kontroll (ADC) som en tjänst som erbjuder olika Layer 7 belastnings Utjämnings funktioner för ditt program. Det gör att kunderna kan optimera webb server gruppens produktivitet genom att avlasta CPU-intensiva TLS-avslutning till programgatewayen. Andra funktioner för Layer 7-routning omfattar resursallokerings distribution av inkommande trafik, cookie-baserad sessionsgräns, URL-sökväg baserad Routning och möjligheten att vara värd för flera webbplatser bakom en enda Programgateway. Application Gateway kan konfigureras som en Internetbaserad Gateway, en intern gateway eller en kombination av båda. Application Gateway är helt Azure-hanterad, skalbar och hög tillgänglig. För att få en bättre hantering ingår en omfattande uppsättning diagnostik- och loggningsfunktioner.
Load Balancer är en integrerad del av Azure SDN-stacken som tillhandahåller högpresterande Layer 4-belastnings Utjämnings tjänster med låg latens för alla UDP-och TCP-protokoll. Den hanterar inkommande och utgående anslutningar. Du kan konfigurera offentliga och interna belastningsutjämnade slutpunkter och definiera regler för att mappa inkommande anslutningar till serverdelspooldestinationer med TCP- och HTTP-avsökningsalternativ för att hantera tjänstens tillgänglighet.


## <a name="choosing-a-global-load-balancer"></a>Välja en global belastningsutjämnare
När du väljer en global belastningsutjämnare mellan Traffic Manager och Azure-frontend för global routning bör du fundera på vad som är detsamma och vad som är detsamma om de två tjänsterna.   Båda tjänsterna tillhandahåller
- **Multi-GEO-redundans:** Om en region kraschar dirigerar trafiken sömlöst till den närmaste regionen utan att något behöver åtgärdas från program ägaren.
- **Närmaste regions dirigering:** Trafiken dirigeras automatiskt till den närmaste regionen

</br>I följande tabell beskrivs skillnaderna mellan Traffic Manager och Azures frontend-dörr:</br>

| Traffic Manager | Azure Front Door |
| --------------- | ------------------------ |
|**Valfritt protokoll:** Eftersom Traffic Manager fungerar i DNS-skiktet kan du dirigera vilken typ av nätverks trafik som helst. HTTP, TCP, UDP osv. | **Http-acceleration:** Med front dörr trafik är proxyn i Microsofts nätverk.  På grund av detta kan HTTP (S)-begär Anden se svars tid och data flödes förbättringar som minskar svars tiden för TLS-förhandlingen och använder frekventa anslutningar från AFD|
|**Lokal routning:** Med routning på ett DNS-lager går trafiken alltid från punkt till plats.  Routning från ditt avdelnings kontor till ditt lokala data Center kan ta en direkt sökväg. även på ditt eget nätverk med Traffic Manager. | **Oberoende skalbarhet:** Eftersom front dörren fungerar med HTTP-begäran kan begär anden till olika URL-sökvägar dirigeras till olika Server delar/regionala service pooler (mikrotjänster) baserat på regler och hälsan för varje program mikrotjänst.|
|**Fakturerings format:** DNS-baserade fakturerings skalor med dina användare och för tjänster med fler användare, plateaus för att minska kostnaderna vid högre användning. |**Infogad säkerhet:** Front dörren gör det möjligt att använda regler som hastighets begränsning och IP ACL-ing för att skydda dina Server delar innan trafiken når ditt program. 

</br>På grund av prestanda, Operability och säkerhets förmåner till HTTP-arbetsbelastningar med start dörren rekommenderar vi att kunderna använder en front dörr för sina HTTP-arbetsbelastningar.    Traffic Manager och front dörren kan användas parallellt för att hantera all trafik för ditt program. 

## <a name="building-with-azures-application-delivery-suite"></a>Skapa med Azures Application Delivery Suite 
Vi rekommenderar att alla webbplatser, API: er, tjänster är geografiskt redundanta och levererar trafik till sina användare från den närmaste (lägsta latens) platsen till dem närhelst det är möjligt.  Genom att kombinera tjänster från Traffic Manager, front dörr, Application Gateway och Load Balancer kan du bygga geografiskt och zonally redundant för att maximera tillförlitlighet, skalning och prestanda.

I följande diagram beskriver vi en exempel tjänst som använder en kombination av alla dessa tjänster för att bygga en global webb tjänst.   I det här fallet har arkitekten använt Traffic Manager för att dirigera till globala Server delar för fil-och objekt leverans, samtidigt som du använder en front dörr för att globalt dirigera URL-sökvägar som matchar mönstret/Store/* till den tjänst som de har migrerat till App Service samtidigt som alla andra begär anden till regionala programgateways.

I den här regionen har programutvecklaren beslutat att alla URL: er som matchar mönstret/images/* hanteras från en dedikerad pool av virtuella datorer som skiljer sig från resten av webb server gruppen.

Dessutom måste standard-VM-poolen som hanterar det dynamiska innehållet kommunicera med en backend-databas som finns i ett kluster med hög tillgänglighet. Hela distributionen konfigureras via Azure Resource Manager.

Följande diagram visar arkitekturen för det här scenariot:

![Detaljerad arkitektur för Application Delivery Suite][2] 

> [!NOTE]
> Det här exemplet är bara en av många möjliga konfigurationer av de belastnings Utjämnings tjänster som Azure erbjuder. Traffic Manager, front dörren, Application Gateway och Load Balancer kan blandas och matchas mot bästa möjliga belastnings Utjämnings behov. Om t. ex. TLS/SSL-avlastning eller Layer 7-bearbetning inte behövs, kan Load Balancer användas i stället för Application Gateway.


## <a name="next-steps"></a>Nästa steg

- Läs hur du [skapar en Front Door](quickstart-create-front-door.md).
- Läs [hur Front Door fungerar](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure1.png
[2]: ./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure2.png
