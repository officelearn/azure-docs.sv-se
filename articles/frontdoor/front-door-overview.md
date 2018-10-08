---
title: Azure Front Door Service | Microsoft Docs
description: Den här artikeln innehåller en översikt för Azure Front Door. Se om det är rätt belastningsutjämningslösning för dig.
services: frontdoor
documentationcenter: ''
author: sharad4u
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/29/2018
ms.author: sharadag
ms.openlocfilehash: 346fa5721df6c9ce0cf355adea21f59c93a394a9
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47040490"
---
# <a name="what-is-azure-front-door-service"></a>Vad är Azure Front Door Service?
Med Azure Front Door Service kan du definiera, hantera och övervaka global routning av din webbtrafik, genom att optimera för bästa prestanda och omedelbar global redundans för hög tillgänglighet. Med Front Door kan du transformera dina globala (för flera regioner) konsument- och företagsprogram till robusta, högpresterande och anpassade moderna program, API:er och innehåll som når en global publik med Azure.

Front Door fungerar i Layer 7- eller HTTP/HTTPS-lagret och använder anycast-protokoll med delad TCP och Microsofts globala nätverk för att förbättra globala anslutningar. Så om du väljer routningsmetod i konfigurationen kan du vara säker på att Front Door dirigerar dina klientbegäranden till den snabbaste och mest tillgängliga programserverdelen. En programserverdel är en Internetansluten tjänst i eller utanför Azure. Front Door innehåller en uppsättning [trafikroutningsmetoder](front-door-routing-methods.md) och [alternativ för hälsoövervakning av serverdelen](front-door-health-probes.md) som passar olika programbehov och modeller för automatisk redundans. Precis som [Traffic Manager](../traffic-manager/traffic-manager-overview.md) är Front Door motståndskraftigt mot fel, inklusive fel som påverkar en hel Azure-region.

>[!NOTE]
> Med Azure har du tillgång till en uppsättning fullständigt hanterade belastningsutjämningslösningar för dina scenarier. Om du är intresserad av en DNS-baserad global routning och **inte** har några krav på avslutning av TLS-protokoll (Transport Layer Security) (”SSL-avlastning”) eller bearbetning på programnivå för enskilda HTTP/HTTPS-begäranden, kan [Traffic Manager](../traffic-manager/traffic-manager-overview.md) kanske passa dig. Om du vill ha belastningsutjämning mellan dina servrar i en region för programnivån kan du använda [Application Gateway,](../application-gateway/application-gateway-introduction.md) och vid belastningsutjämning av nätverkslagret kan [Azure Load Balancer](../load-balancer/load-balancer-overview.md) vara användbart. Du kan med fördel kombinera dessa lösningar efter behov för dina slutpunkts-till-slutpunkts-scenarier.

Följande funktioner ingår i Front Door:

## <a name="accelerate-application-performance"></a>Påskynda programprestandan
Om du använder ett delat TCP-baserat anycast-protokoll, säkerställer Front Door att dina slutanvändare snabbt kan ansluta till närmaste POP-plats (Point of Presence) för Front Door. Med hjälp av Microsofts globala nätverk för att ansluta till dina programserverdelar från Front Doors POP, får du högre tillgänglighet och tillförlitlighet samtidigt som prestandan bibehålls. Den här anslutningen till din serverdel baseras också på så låg nätverksfördröjning som möjligt. Läs mer om Front Doors routningstekniker, som t.ex. [Delad TCP](front-door-routing-architecture.md#splittcp) och [Anycast-protokoll](front-door-routing-architecture.md#anycast).

## <a name="increase-application-availability-with-smart-health-probes"></a>Öka programmets tillgänglighet med smarta hälsokontroller

Front Door ger hög tillgänglighet för dina viktiga program med hjälp av dess smarta hälsokontroller, där dina serverdelar övervakas för både svarstid och tillgänglighet och du får omedelbar automatisk redundans om en serverdel kraschar. Det innebär att du kan köra planerat underhåll för dina program utan någon stilleståndstid. Front Door dirigerar trafiken till alternativa serverdelar när underhållet pågår.

## <a name="url-based-routing"></a>URL-baserad routning
Med URL-sökvägsbaserad routning kan du dirigera trafik till serverdelspooler som baseras på URL-sökvägen till begärandet. Ett av scenarierna är att dirigera begäranden för olika innehållstyper till olika serverdelspooler.

Till exempel dirigeras begäranden för `http://www.contoso.com/users/*` till UserProfilePool och `http://www.contoso.com/products/*` dirigeras till ProductInventoryPool.  Med Front Door får du ännu mer komplexa vägmatchningsscenarier där den bästa matchningsalgoritmen används. Om inget sökvägsmönster matchar kommer standardhanteringsregeln för `http://www.contoso.com/*` att väljas och trafiken dirigeras till en standardhanteringsregel som fångar in alla. Läs mer i [Vägmatchning](front-door-route-matching.md).

## <a name="multiple-site-hosting"></a>Värd för flera platser
Om du har flera webbplatser kan du konfigurera fler än en webbplats inom samma Front Door-konfiguration. Med den här funktionen kan du konfigurera en mer effektiv topologi för dina distributioner genom att lägga till olika webbplatser i en enda Front Door-konfiguration. Baserat på ditt programs arkitektur kan du konfigurera Azure Front Door Service till att antingen dirigera varje webbplats till sin egen serverdelspool, eller ha olika webbplatser som dirigeras till samma serverdelspool. Front Door kan exempelvis hantera trafik för `images.contoso.com` och `videos.contoso.com` från två serverdelspooler som heter ImagePool och VideoPool. Du kan också konfigurera att båda klientdelsvärdarna dirigerar trafik till en enda serverdelspool med namnet MediaPool.

På liknande sätt kan du ha två olika domäner, `www.contoso.com` och `www.fabrikam.com`, som har konfigurerats för samma Front Door.

## <a name="session-affinity"></a>Sessionstillhörighet
Den cookie-baserade sessionstillhörighetsfunktionen är användbar när du vill behålla en användarsession i samma programserverdel. Med hjälp av Front Doors hanterade cookies, dirigeras efterföljande trafik från en användarsession till samma programserverdel för bearbetning. Den här funktionen är viktig i de fall där sessionstillstånd har sparats lokalt på serverdelen för en användarsession.

## <a name="secure-sockets-layer-ssl-termination"></a>Secure Sockets Layer-avslutning (SSL)
Front Door har stöd för SSL-avslutning vid gränsen vilket innebär att enskilda användare kan konfigurera en SSL-anslutning med Front Door-miljöer, i stället för att upprätta den med långvariga anslutningar till programserverdelen. Dessutom har Front Door stöd för både HTTP- och HTTPS-anslutning mellan Front Door-miljöer och serverdelar. Det innebär att du också kan ange en SSL-kryptering från slutpunkt till slutpunkt. Om Front Door för din programarbetsbelastning tar emot över 5 000 begäranden per minut, kommer den vid återanvändning av anslutningen till aktiva tjänster endast upprätta 500 anslutningar till din programserverdel, vilket avsevärt minskar belastningen från serverdelen.

## <a name="custom-domains-and-certificate-management"></a>Anpassade domäner och certifikatshantering
När du använder Front Door för att leverera innehåll behövs en anpassad domän om du vill att ditt eget domännamn ska synas i din Front Door-URL. Att ha ett synligt domännamn kan vara praktiskt för dina kunder och användbart i profileringssyfte.
Front Door har också stöd för HTTPS för anpassade domännamn. Använd denna funktion genom att antingen välja hanterade Front Door-certifikat för din trafik, eller ladda upp ditt egna anpassade SSL-certifikat.

## <a name="application-layer-security"></a>Säkerhet för programskikt
Med Azure Front Door kan du skapa anpassade regler för brandväggen för webbaserade program (WAF) vid åtkomstkontroll, för att skydda din HTTP/HTTPS-arbetsbelastning från utnyttjande som baseras på klienternas IP-adresser, landskod och HTTP-parametrar. Dessutom kan du med Front Door också skapa begränsningsregler för att bekämpa skadlig robottrafik. 

Själva Front Door-plattformen är skyddad av [Azure DDoS Protection](../virtual-network/ddos-protection-overview.md) Basic. Om du behöver mer skydd kan Azure DDoS Protection Standard aktiveras på dina virtuella nätverk och skydda resurser från nätverkslagerattacker (TCP/UDP) med hjälp av automatiska justeringar och åtgärder. Front Door är en omvänd proxy för Layer 7 där webbtrafik kan passera serverdelarna och andra typer av trafik blockeras som standard.

## <a name="url-rewrite"></a>URL-omskrivning
Front Door har stöd för [URL-omskrivning](front-door-url-rewrite.md), vilket innebär att du kan konfigurera en valfri anpassad sökväg för vidarebefordran. Den används när en begäran skapas som ska vidarebefordras till serverdelen. Med Front Door kan du dessutom konfigurera det värdhuvud som ska skickas när du vidarebefordrar begärandet till din serverdel.

## <a name="protocol-support---ipv6-and-http2-traffic"></a>Protokollstöd – IPv6- och HTTP/2-trafik
Azure Front Door har inbyggt stöd för IPv6-anslutningar för slutpunkt till slutpunkt och även för HTTP/2-protokoll. 

HTTP/2-protokollet ger full duplex-kommunikation mellan programserverdelar och en klient över en långvarig TCP-anslutning. HTTP/2 ger en mer interaktiv kommunikation mellan serverdelen och klienten, som kan vara dubbelriktad utan att behöva den avsökning som krävs i HTTP-baserade implementeringar. HTTP/2-protokoll har låga omkostnader, till skillnad från HTTP, och kan återanvända samma TCP-anslutning för flera begäranden eller svar, vilket resulterar i ett mer effektivt utnyttjande av resurser. Läs mer om [HTTP/2-stöd i Azure Front Door Service](front-door-http2.md).

## <a name="pricing"></a>Prissättning

Information om priser finns i [Prissättning för Front Door](https://azure.microsoft.com/pricing/details/frontdoor/).

## <a name="next-steps"></a>Nästa steg

- Lär dig att [skapa en Front Door](quickstart-create-front-door.md).
- Lär dig [hur Front Door fungerar](front-door-routing-architecture.md).
