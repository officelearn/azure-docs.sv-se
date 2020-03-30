---
title: Azure Front Door - Belastningsutjämning med Azures programsaldopaket | Microsoft-dokument
description: Den här artikeln hjälper dig att lära dig mer om hur Azure rekommenderar belastningsutjämning med sin programsaldopaket
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
ms.openlocfilehash: 9f8d1959549eaddfb4a2c9ea271094db0073c788
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471718"
---
# <a name="load-balancing-with-azures-application-delivery-suite"></a>Belastningsutjämning med Azures paket för programleverans

## <a name="introduction"></a>Introduktion
Microsoft Azure tillhandahåller flera globala och regionala tjänster för att hantera hur nätverkstrafiken distribueras och belastningsutjämnas: Traffic Manager, Front Door, Application Gateway och Load Balancer.  Tillsammans med Azures många regioner och zonarkitektur kan du med hjälp av dessa tjänster tillsammans skapa robusta, skalbara högpresterande program.

![Svit för leverans av program ][1]
 
Dessa tjänster är indelade i två kategorier:
1. **Globala belastningsutjämningstjänster** som Traffic Manager och Front Door distribuerar trafik från dina slutanvändare över dina regionala backends, över moln eller till och med din hybrid lokala tjänster. Global belastningsutjämning dirigerar trafiken till din närmaste servicebackend och reagerar på ändringar i tjänstens tillförlitlighet eller prestanda för att upprätthålla alltid på, maximal prestanda för dina användare. 
2. **Regionala belastningsutjämningstjänster** som Standardbelastningsutjämning eller Application Gateway ger möjlighet att distribuera trafik inom virtuella nätverk (VNETs) över virtuella datorer (VMs) eller slutpunkter för zontjänster inom en region.

Genom att kombinera globala och regionala tjänster i ditt program kan du skapa ett heltäckande tillförlitligt, högpresterande och säkert sätt att dirigera trafik till och från användarna till dina IaaS-, PaaS- eller lokala tjänster. I nästa avsnitt beskriver vi var och en av dessa tjänster.

## <a name="global-load-balancing"></a>Global belastningsutjämning
**Traffic Manager** ger global DNS-belastningsutjämning. Den tittar på inkommande DNS-begäranden och svarar med en felfri serverning, i enlighet med den routningsprincip som kunden har valt. Alternativ för routningsmetoder är:
- Prestandaroutning för att skicka beställaren till närmaste backend när det gäller svarstid.
- Prioriterad routning för att dirigera all trafik till en backend, med andra backends som backup.
- Viktad rundhakardirigering, som distribuerar trafik baserat på den viktning som tilldelas varje backend.
- Geografisk routning för att säkerställa att beställare som finns i specifika geografiska regioner dirigeras till de backends som mappas till dessa regioner (till exempel bör alla begäranden från Spanien dirigeras till regionen France Central Azure)
- Undernätsdirigering som gör att du kan mappa IP-adressintervall till serverdel så att begäranden som kommer från dessa skickas till den angivna serverdelen (till exempel bör alla användare som ansluter från företagets HUVUDKONTORs IP-adressintervall få annat webbinnehåll än den allmänna användare)

Klienten ansluter direkt till den medd-knappen. Azure Traffic Manager identifierar när en serverdel är felaktig och omdirigerar sedan klienterna till en annan felfri instans. Läs [Dokumentationen](../traffic-manager/traffic-manager-overview.md) till Azure Traffic Manager om du vill veta mer om tjänsten.

**Azure Front Door** ger dynamisk webbplatsacceleration (DSA) inklusive global HTTP-belastningsutjämning.  Den tittar på inkommande HTTP-begäranden vägar till närmaste tjänst backend / region för den angivna värdnamn, URL sökväg och konfigurerade regler.  
Ytterdörren avslutar HTTP-begäranden i utkanten av Microsofts nätverk och avsökningar aktivt för att identifiera program- eller infrastrukturhälso- eller svarstidsändringar.  Ytterdörren sedan alltid rutter trafik till den snabbaste och tillgängliga (friska) backend. Se Front Doors [routningsarkitekturinformation](front-door-routing-architecture.md) och [trafikroutningsmetoder](front-door-routing-methods.md) för att lära dig mer om tjänsten.

## <a name="regional-load-balancing"></a>Regional belastningsutjämning
Application Gateway tillhandahåller ADC (Application Delivery Controller) som en tjänst som erbjuder olika load-balancing-funktioner för Layer 7 för ditt program. Det gör det möjligt för kunder att optimera webbgruppens produktivitet genom att avlasta CPU-intensiv SSL-avslutning till programgatewayen. Andra routningsfunktioner för Layer 7 inkluderar distribution av round-robin av inkommande trafik, cookie-baserad sessionstillhörighet, URL-sökvägsbaserad routning och möjligheten att vara värd för flera webbplatser bakom en enda programgateway. Application Gateway kan konfigureras som en Internet-vänd gateway, en intern-bara gateway, eller en kombination av båda. Application Gateway är helt Azure-hanterad, skalbar och högtillgänglig. För att få en bättre hantering ingår en omfattande uppsättning diagnostik- och loggningsfunktioner.
Belastningsutjämnare är en integrerad del av Azure SDN-stacken, vilket ger högpresterande layer 4-belastningsutjämningstjänster med låg latens för alla UDP- och TCP-protokoll. Den hanterar inkommande och utgående anslutningar. Du kan konfigurera offentliga och interna belastningsutjämnade slutpunkter och definiera regler för att mappa inkommande anslutningar till serverdelspooldestinationer med TCP- och HTTP-avsökningsalternativ för att hantera tjänstens tillgänglighet.


## <a name="choosing-a-global-load-balancer"></a>Välja en global belastningsutjämnare
När du väljer en global belastningsutjämnare mellan Traffic Manager och Azure Front Door för global routning bör du överväga vad som är liknande och vad som är annorlunda med de två tjänsterna.   Båda tjänsterna tillhandahåller
- **Multi-geo redundans:** Om en region går ner dirigeras trafiken sömlöst till närmaste region utan att programägaren har någon åtgärd.
- **Närmaste regionroutning:** Trafiken dirigeras automatiskt till närmaste region

</br>I följande tabell beskrivs skillnaderna mellan Traffic Manager och Azure Front Door:</br>

| Traffic Manager | Azure Front Door |
| --------------- | ------------------------ |
|**Alla protokoll:** Eftersom Traffic Manager fungerar på DNS-lagret kan du dirigera alla typer av nätverkstrafik. HTTP, TCP, UDP, etc. | **HTTP-acceleration:** Med Ytterdörren trafiken är proxied i utkanten av Microsofts nätverk.  På grund av detta ser HTTP(S) begäranden svarstid och dataflödesförbättringar som minskar svarstiden för SSL-förhandling och använder heta anslutningar från AFD till ditt program.|
|**Lokal routning:** Med routning på ett DNS-lager går trafiken alltid från punkt till punkt.  Routning från filialkontoret till ditt lokala datacenter kan ta en direkt sökväg. även i ditt eget nätverk med Traffic Manager. | **Oberoende skalbarhet:** Eftersom ytterdörren fungerar med HTTP-begäran kan begäranden till olika URL-sökvägar dirigeras till olika backend / regional servicepooler (mikrotjänster) baserat på regler och hälsotillståndet för varje programmikrotjänst.|
|**Faktureringsformat:** DNS-baserad faktureringsskalor med användarna och för tjänster med fler användare, platåer för att minska kostnaderna vid högre användning. |**Infogad säkerhet:** Ytterdörren gör det möjligt för regler som hastighetsbegränsning och IP ACL-ning så att du kan skydda dina backends innan trafiken når ditt program. 

</br>På grund av prestanda, funktionsduglighet och säkerhetsfördelar för HTTP-arbetsbelastningar med Ytterdörren rekommenderar vi att kunderna använder Ytterdörren för sina HTTP-arbetsbelastningar.    Traffic Manager och Ytterdörren kan användas parallellt för att betjäna all trafik för din ansökan. 

## <a name="building-with-azures-application-delivery-suite"></a>Skapa med Azures programsleveranspaket 
Vi rekommenderar att alla webbplatser, API:er, tjänster är geografiskt redundanta och levererar trafik till sina användare från den närmaste (lägsta latensen) till dem när det är möjligt.  Genom att kombinera tjänster från Traffic Manager, Ytterdörren, Application Gateway och Load Balancer kan du skapa geografiskt och zonmässigt redundant för att maximera tillförlitlighet, skalning och prestanda.

I följande diagram beskriver vi en exempeltjänst som använder en kombination av alla dessa tjänster för att skapa en global webbtjänst.   I det här fallet har arkitekten använt Traffic Manager för att dirigera globala backends för fil- och objektleverans, samtidigt som frontdörren används för att globalt dirigera URL-sökvägar som matchar mönstret /store/* till den tjänst de har migrerat till App Service medan de dirigerar alla andra begäranden till regionala programgateways.

I regionen, för deras IaaS-tjänst, har programutvecklaren beslutat att alla webbadresser som matchar mönstret /images/* serveras från en särskild pool med virtuella datorer som skiljer sig från resten av webbgruppen.

Dessutom måste den virtuella standardpoolen för virtuella datorer som betjänar det dynamiska innehållet prata med en backend-databas som finns i ett kluster med hög tillgänglighet. Hela distributionen har konfigurerats via Azure Resource Manager.

Följande diagram visar arkitekturen i det här scenariot:

![Detaljerad arkitektur för Application Delivery Suite][2] 

> [!NOTE]
> Det här exemplet är bara en av många möjliga konfigurationer av belastningsutjämningstjänster som Azure erbjuder. Traffic Manager, Ytterdörren, Application Gateway och Load Balancer kan blandas och matchas för att bäst passa dina belastningsutjämningsbehov. Om SSL-avlastning eller layer 7-bearbetning till exempel inte är nödvändig kan belastningsutjämning användas i stället för Application Gateway.


## <a name="next-steps"></a>Efterföljande moment

- Läs hur du [skapar en Front Door](quickstart-create-front-door.md).
- Läs [hur Front Door fungerar](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure1.png
[2]: ./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure2.png
