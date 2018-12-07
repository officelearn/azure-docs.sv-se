---
title: Azure ytterdörren Service – cachelagring | Microsoft Docs
description: Den här artikeln hjälper dig att förstå hur Azure ytterdörren tjänsten övervakar hälsotillståndet för serverdelen
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
ms.openlocfilehash: 97c02726c7e359195c6bf4ea793404562f2acccf
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "53001941"
---
# <a name="caching-with-azure-front-door-service"></a>Cachelagring med Azure ytterdörren Service
Följande dokument anger beteende för ytterdörren med regler för routning som har aktiverat cachelagring.

## <a name="delivery-of-large-files"></a>Leveransen av stora filer
Azure ytterdörren-tjänst ger stora filer utan ett tak för filstorlek. Ytterdörren använder en teknik som kallas objekt storlekar. När en stor fil har begärts, hämtar ytterdörren mindre delar av filen från serverdelen. När du har fått en begäran om filen full- eller byte-intervall, begär en ytterdörren miljö filen från serverdelen i segment på 8 MB.

</br>När segmentet anländer till ytterdörren-miljö, är det cachelagras och hanteras direkt av användaren. Ytterdörren hämtar sedan före nästa segment parallellt. Den här före hämtning säkerställer att innehållet förblir en segment före användaren, vilket minskar svarstider. Den här processen fortsätter tills hela filen hämtas (om så krävs), alla byte-intervall finns (om så krävs), eller klienten avslutar anslutningen.

</br>Mer information om byte-intervall begäran [RFC 7233](https://web.archive.org/web/20171009165003/ http://www.rfc-base.org/rfc-7233.html).
Ytterdörren cachelagras alla segment när de tas emot och så hela filen inte behöver cachelagras på ytterdörren-cachen. Efterföljande begäranden om filen eller byte-intervall hämtas från cachen. Om inte alla segment cachelagras, förhämtning används för att begära segment från serverdelen. Denna optimering förlitar sig på serverdelen förmåga att byte-intervall supportförfrågningar; Om serverdelen inte stöder begäranden med byte-intervall kan inte denna optimering är effektivt.

## <a name="file-compression"></a>Filkomprimering
Åtkomsten kan dynamiskt innehåll komprimeras på kanten, vilket resulterar i ett mindre och snabbare svar på dina klienter. Alla filer är berättigade för komprimering. En fil måste dock vara av en MIME-typ som berättigade komprimering lista. Ytterdörren tillåter för närvarande inte den här listan som ska ändras. Den aktuella listan är:</br>
- ”application/eot”
- ”application/teckensnitt”
- ”application/font-sfnt”
- ”application/javascript”
- ”application/json”
- ”application/opentype”
- ”application/otf”
- ”application/pkcs7-mime”
- ”application/truetype”
- ”application/inget”
- ”application/vnd.ms-fontobject”
- ”application/xhtml + xml”
- ”application/xml”
- ”application/xml + rss”
- ”application/x-font-opentype”
- ”application/x-font-truetype”
- ”application/x-font-inget”
- ”application/x-httpd-cgi”
- ”application/x-mpegurl”
- ”application/x-opentype”
- ”application/x-otf”
- ”application/x-perl”
- ”application/x-inget”
- ”application/x-javascript”
- ”teckensnitt/eot”
- ”teckensnitt/inget”
- ”teckensnitt/otf”
- ”teckensnitt/opentype”
- ”bild/svg + xml”
- ”text/css”
- ”text/csv”
- ”text/html”
- ”text/javascript”
- ”text/js”, ”text/plain”
- ”text/richtext”
- ”text /-tabbavgränsade-värden”
- ”text/xml”
- ”text/x-skript”
- ”text/x-component”
- ”text/x-java-källa”

Filen måste dessutom innehålla mellan 1 KB och 8 MB i storlek, inklusive.

Dessa profiler stöder följande komprimering skriver:
- [gzip (GNU zip)](https://en.wikipedia.org/wiki/Gzip)
- [brotli](https://en.wikipedia.org/wiki/Brotli)

Om en begäran har stöd för gzip- och Brotli komprimering, företräde Brotli komprimering.</br>
När en begäran för en tillgång anger komprimering och begäran resultaten i en cachemiss, utför ytterdörren komprimering av tillgången direkt på POP-servern. Därefter hämtas den komprimerade filen från cachen. Det resulterande objektet returneras med ett transfer-encoding: chunked.

## <a name="query-string-behavior"></a>Uppträdande för frågesträngar
Med åtkomsten, kan du styra hur filerna cachelagras för en webbegäran som innehåller en frågesträng. I en webb-begäran med en frågesträng är frågesträngen som del av den begäran som inträffar efter ett frågetecken (?). En frågesträng kan innehålla en eller flera nyckel / värde-par, där fältnamnet och dess värde avgränsas med ett likhetstecken (=). Varje nyckel / värde-par är avgränsade med ett et-tecken (&). Till exempel http://www.contoso.com/content.mov?field1=value1&field2=value2. Om det finns fler än en nyckel / värde-par i en frågesträng för en begäran, spelar ingen deras inbördes ordning.
- **Ignorera frågesträngar**: standardläget. I det här läget ytterdörren skickar frågesträngarna från begäranden till serverdelen på den första begäran och cachelagrar tillgången. Alla efterföljande begäranden om tillgången som hämtas från miljön ytterdörren Ignorera frågesträngarna tills den cachelagra tillgången upphör att gälla.

- **Cachelagra varje unik URL**: I det här läget varje begäran med en unik URL, inklusive frågesträngen behandlas som en unik tillgång med sin egen cache. Till exempel svar från serverdelen för en begäran om `www.example.ashx?q=test1` är cachelagras på ytterdörren miljön och returneras för efterföljande cacheminnen med samma frågesträngen. En begäran om `www.example.ashx?q=test2` cachelagras som en separat tillgång med sin egen time-to-live-inställningen.

## <a name="cache-purge"></a>Rensa cache
Ytterdörren cachelagrar tillgångar tills tillgångens time to live (TTL) upphör att gälla. När tillgångens TTL upphör att gälla när en klient begär tillgången, ytterdörren miljö kommer att hämta en ny uppdaterad kopia av tillgången som fungerar klientbegäran och store uppdatera cachen.
</br>Rekommenderade metoder för att se till att användarna alltid hämta den senaste kopian av dina tillgångar är att version tillgångarna för varje uppdatering och publicera dem som nya URL: er. Ytterdörren kommer omedelbart att hämta nya tillgångar för nästa klientbegäranden. Ibland kanske du vill rensa cachelagrat innehåll från alla kantnoder och tvinga dem alla att hämta nya uppdaterade tillgångar. Detta kan bero på uppdateringar i ditt webbprogram eller för att snabbt uppdatera tillgångar som innehåller felaktig information.

</br>Välj vilka resurser som du vill ta bort från edge-noder. Om du vill ta bort alla tillgångar, klickar du på Rensa alla kryssrutan. I annat fall Skriv sökvägen för varje tillgång som du vill ta bort i textrutan sökväg. Nedan format som stöds i sökvägen.
1. **Den enda URL Rensa**: Rensa enskilda tillgångar genom att ange en fullständig URL med filnamnstillägget, till exempel /pictures/strasbourg.png;
2. **Jokerteckensrensning**: Asterisk (\*) kan användas som jokertecken. Rensa alla mappar, undermappar och filer under en slutpunkt med /\* i sökvägen eller rensa alla undermappar och filer under en viss mapp genom att ange mappen följt av /\*, till exempel /bilder/\*.
3. **Rot domän Rensa**: Rensa roten för slutpunkten med ”/” i sökvägen.

Cache återställningspunkter på åtkomsten är skiftlägeskänsliga. Dessutom är de oberoende av frågan sträng, vilket innebär att rensa en URL kommer Rensa alla frågesträng varianter av den. 

## <a name="cache-expiration"></a>Förfallotid för cache
I följande prioritetsordning rubriker används för att avgöra hur lång tid ett objekt som är lagrade i vår cache:</br>
1. Cache-Control: s-maxage =<seconds>
2. Cache-Control: maxage =<seconds>
3. Förfaller: < http-date >

Cache-Control-svarshuvuden som indikerar att svaret inte cachelagras, till exempel Cache-Control: privat Cache-Control: no-cache och Cache-Control: no-store respekteras. Om det finns flera begäranden pågående på en POP för samma URL, kan de dela svaret.


## <a name="request-headers"></a>Begärandehuvud

Följande begärandehuvuden kommer inte att vidarebefordras till en serverdel när du använder cachelagring.
- Auktorisering
- Content-Length
- Transfer-Encoding

## <a name="next-steps"></a>Nästa steg

- Läs hur du [skapar en Front Door](quickstart-create-front-door.md).
- Läs [hur Front Door fungerar](front-door-routing-architecture.md).