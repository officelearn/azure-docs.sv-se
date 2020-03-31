---
title: Azurblå ytterdörr - cachelagring | Microsoft-dokument
description: Den här artikeln hjälper dig att förstå hur Azure Front Door övervakar hälsan hos dina backends
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
ms.openlocfilehash: d4fed878e2c0b1430e963f43743fd772493d3270
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471752"
---
# <a name="caching-with-azure-front-door"></a>Cachelagring med Azure Ytterdörr
Följande dokument anger beteende för Ytterdörren med routningsregler som har aktiverat cachelagring. Ytterdörren är en modern Content Delivery Network (CDN) och så tillsammans med dynamisk plats acceleration och belastningsutjämning, stöder det också caching beteenden precis som alla andra CDN.

## <a name="delivery-of-large-files"></a>Leverans av stora filer
Azure Front Door levererar stora filer utan tak för filstorlek. Ytterdörren använder en teknik som kallas objekt chunking. När en stor fil har begärts, hämtar Front Door mindre delar av filen från serverdelen. När en Front Door-miljö tagit emot en fullständig begäran eller en byteintervallbegäran för en fil begär den filen från serverdelen i segment om 8 MB.

</br>När segmentet anländer till frontdörren miljön, är det cachelagrade och omedelbart serveras till användaren. Ytterdörren sedan pre-hämtar nästa bit parallellt. Den här förhandshämtningen säkerställer att innehållet ligger ett segment före användaren, vilket minskar svarstiden. Den här processen fortsätter tills hela filen hämtas (om så önskas), alla byteintervall är tillgängliga (om så önskas) eller så avslutar klienten anslutningen.

</br>Mer information om begäran om byteintervall finns i [RFC 7233](https://web.archive.org/web/20171009165003/http://www.rfc-base.org/rfc-7233.html).
Ytterdörren cachelagrar alla bitar när de tas emot och därför behöver inte hela filen cachelagras på klientluckans cacheminne. Efterföljande begäranden för fil- eller byteintervallen visas från cacheminnet. Om inte alla segment cachelagras används förhämtning för att begära segment från backend. Den här optimeringen är beroende av serverdans förmåga att stödja begäranden för byteintervall. Om serverdan inte stöder begäranden för byteintervall är den här optimeringen inte effektiv.

## <a name="file-compression"></a>Filkomprimering
Ytterdörren kan dynamiskt komprimera innehåll på kanten, vilket resulterar i ett mindre och snabbare svar till dina kunder. Alla filer kan kommaprimering. En fil måste dock vara av en MIME-typ som kan komma i komprimeringslistan. För närvarande tillåter ytterdörren inte att denna lista ändras. Den aktuella listan är:</br>
- "ansökan/eot"
- "program/teckensnitt"
- "ansökan/font-sfnt"
- "program/javascript"
- ”application/json”
- "ansökan/opentype"
- "ansökan/otf"
- "ansökan/pkcs7-mime"
- "ansökan/truetype"
- "ansökan/ttf".
- "application/vnd.ms-fontobject"
- "application/xhtml+xml"
- "program/xml"
- "application/xml+rss"
- "application/x-font-opentype"
- "application/x-font-truetype"
- "application/x-font-ttf"
- "ansökan/x-httpd-cgi"
- "ansökan/x-mpegurl"
- "application/x-opentype"
- "ansökan/x-otf"
- "ansökan/x-perl"
- "ansökan/x-ttf"
- "program/x-javascript"
- "font/eot"
- "font/ttf"
- "font/otf"
- "font/opentype"
- "bild/svg+xml"
- "text/css"
- "text/csv"
- "text/html"
- "text/javascript"
- "text/js", "text/oformaterad"
- "text/richtext"
- "text/tabbavgränsade värden"
- "text/xml"
- "text/x-script"
- "text/x-komponent"
- "text/x-java-källa"

Dessutom måste filen också vara mellan 1 KB och 8 MB i storlek, inklusive.

Dessa profiler stöder följande komprimeringskodningar:
- [Gzip (GNU zip)](https://en.wikipedia.org/wiki/Gzip)
- [Brotli (brotli)](https://en.wikipedia.org/wiki/Brotli)

Om en begäran stöder gzip- och Brotli-komprimering har Brotli-komprimering företräde.</br>
När en begäran om en tillgång anger komprimering och begäran resulterar i en cache miss, utför Front Door komprimering av tillgången direkt på POP-servern. Därefter visas den komprimerade filen från cacheminnet. Den resulterande artikeln returneras med en överföringskodning: segmenterad.

## <a name="query-string-behavior"></a>Beteende för frågesträng
Med Ytterdörren kan du styra hur filer cachelagras för en webbbegäran som innehåller en frågesträng. I en webbbegäran med en frågesträng är frågesträngen den del av begäran som inträffar efter ett frågetecken (?). En frågesträng kan innehålla ett eller flera nyckelvärdespar där fältnamnet och dess värde avgränsas med ett likhetstecken (=). Varje nyckelvärdespar avgränsas med ett et-tecken (&). Till exempel `http://www.contoso.com/content.mov?field1=value1&field2=value2`. Om det finns mer än ett nyckelvärdespar i en frågesträng för en begäran spelar deras ordning ingen roll.
- **Ignorera frågesträngar**: Standardläge. I det här läget skickar Ytterdörren frågesträngarna från frågesträngaren till backend på den första begäran och cachelagrar tillgången. Alla efterföljande begäranden för tillgången som visas från front door-miljön ignorerar frågesträngarna tills den cachelagrade tillgången upphör att gälla.

- **Cache varje unik URL:** I det här läget behandlas varje begäran med en unik URL, inklusive frågesträngen, som en unik tillgång med sin egen cache. Svaret från backend för en begäran `www.example.ashx?q=test1` om cachelagras till exempel i front door-miljön och returneras för efterföljande cacheminnen med samma frågesträng. En begäran `www.example.ashx?q=test2` om cachelagras som en separat tillgång med sin egen tid till live-inställning.

## <a name="cache-purge"></a>Rensa cache
Ytterdörren cachelagrar tillgångar tills tillgångens time-to-live (TTL) löper ut. När tillgångens TTL har upphört att gälla, när en klient begär tillgången, hämtar front door-miljön en ny uppdaterad kopia av tillgången för att betjäna klientbegäran och lagra uppdatering av cacheminnet.
</br>Den bästa metoden för att se till att användarna alltid får den senaste kopian av dina tillgångar är att version dina tillgångar för varje uppdatering och publicera dem som nya webbadresser. Ytterdörren hämtar omedelbart de nya tillgångarna för nästa klientbegäranden. Ibland kanske du vill rensa cachelagrat innehåll från alla kantnoder och tvinga dem alla att hämta nya uppdaterade tillgångar. Detta kan bero på uppdateringar av webbprogrammet eller på att resurser som innehåller felaktig information snabbt uppdateras.

</br>Välj vilka tillgångar du vill rensa från kantnoderna. Om du vill rensa alla tillgångar klickar du på kryssrutan Rensa alla. Annars skriver du sökvägen för varje tillgång som du vill rensa i textrutan Sökväg. Nedanstående format stöds i sökvägen.
1. **Enkel sökvägsrensning:** Rensa enskilda tillgångar genom att ange den fullständiga sökvägen för tillgången (utan protokoll och domän), med filtillägget, till exempel /pictures/strasbourg.png;
2. **Jokerteckenrensning:** Asterisk (\*) kan användas som jokertecken. Rensa alla mappar, undermappar och filer under\* en slutpunkt med / i sökvägen eller rensa alla undermappar\*och filer under\*en viss mapp genom att ange mappen följt av / , till exempel /pictures/ .
3. **Rotdomänrensning:** Rensa roten för slutpunkten med "/" i sökvägen.

Cacherensningar på ytterdörren är skiftlägesokänsliga. Dessutom är de frågesträngaognostiker, vilket innebär att rensa en URL rensar alla frågesträngvariationer av den. 

## <a name="cache-expiration"></a>Cache förfallodatum
Följande ordning på rubriker används för att avgöra hur länge ett objekt ska lagras i vår cache:</br>
1. Cache-Kontroll: s-maxage\<= sekunder>
2. Cache-Control: max-age=\<sekunder>
3. Upphör att \<gälla: http-date>

Cache-Control-svarshuvuden som anger att svaret inte cachelagras, till exempel Cache-Control: privat, Cache-Control: no-cache och Cache-Control: no-store är infriade. Om det finns flera begäranden under flygning på en POP för samma webbadress kan de dock dela svaret. Om ingen cachekontroll finns är standardbeteendet att AFD cachelagrar resursen för X-tid där X slumpmässigt plockas mellan 1 till 3 dagar.

## <a name="request-headers"></a>Begärandehuvuden

Följande begäranden vidarebefordras inte till en backend när cachelagring används.
- Innehållslängd
- Överföring-kodning

## <a name="next-steps"></a>Nästa steg

- Läs hur du [skapar en Front Door](quickstart-create-front-door.md).
- Läs [hur Front Door fungerar](front-door-routing-architecture.md).
