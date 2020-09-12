---
title: Azure frontend-cachelagring | Microsoft Docs
description: Den här artikeln hjälper dig att förstå hur Azures front dörr övervakar hälso tillståndet för dina arbets ändar
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: duau
ms.openlocfilehash: aada5b976721fdfed31131095f7f2b12aefefea9
ms.sourcegitcommit: 70ee014d1706e903b7d1e346ba866f5e08b22761
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2020
ms.locfileid: "90024289"
---
# <a name="caching-with-azure-front-door"></a>Cachelagring med Azures front dörr
Följande dokument anger beteendet för front dörren med routningsregler som har aktiverat cachelagring. Front dörren är en modern Content Delivery Network (CDN) och så vidare med dynamisk webbplats acceleration och belastnings utjämning, stöder den även cachelagring-beteenden precis som andra CDN.

## <a name="delivery-of-large-files"></a>Leverans av stora filer
Azures front dörr levererar stora filer utan fil storlek. Front dörren använder en teknik som kallas objekt segment. När en stor fil har begärts, hämtar Front Door mindre delar av filen från serverdelen. När en Front Door-miljö tagit emot en fullständig begäran eller en byteintervallbegäran för en fil begär den filen från serverdelen i segment om 8 MB.

</br>När segmentet har fått till gång till den främre dörren är det cachelagrat och betjänas direkt för användaren. Front dörren sedan för att hämta nästa segment parallellt. Den här för hämtningen garanterar att innehållet förblir ett segment före användaren, vilket minskar svars tiden. Den här processen fortsätter tills hela filen har hämtats (om det begärs), alla byte-intervall är tillgängliga (om de begärs) eller om klienten avslutar anslutningen.

</br>Mer information om byte Range-begäran finns i [RFC 7233](https://web.archive.org/web/20171009165003/http://www.rfc-base.org/rfc-7233.html).
Frontend-dörren cachelagrar alla segment när de tas emot och så att hela filen inte behöver cachelagras på frontend-cachen. Efterföljande begär Anden för fil-eller byte-intervallen hanteras från cachen. Om inte alla segment cachelagras används för hämtning för att begära segment från Server delen. Den här optimeringen är beroende av Server dels kapaciteten för att stödja byte range-begäranden. om Server delen inte stöder byte intervall begär Anden, är den här optimeringen inte effektiv.

## <a name="file-compression"></a>Filkomprimering
Front dörren kan dynamiskt komprimera innehåll på gränsen, vilket resulterar i ett mindre och snabbare svar på dina klienter. Alla filer är berättigade till komprimering. En fil måste dock vara av en MIME-typ som är berättigad till komprimerings lista. För närvarande tillåter inte front dörren att den här listan ändras. Den aktuella listan är:</br>
- "Application/EOT varningszon"
- "program/teckensnitt"
- "program/Font-sfnt"
- "program/java script"
- ”application/json”
- "program/OpenType"
- "Application/OTF"
- "Application/PKCS7-MIME"
- "program/TrueType"
- "Application/ttf",
- "Application/VND. MS-fontobject"
- "application/xhtml + xml"
- "Application/XML"
- "Application/XML + RSS"
- "program/x-Font-OpenType"
- "program/x-Font-TrueType"
- "program/x-Font-ttf"
- "Application/x-httpd-cgi"
- "Application/x-mpegurl"
- "Application/x-OpenType"
- "Application/x-OTF"
- "Application/x-perl"
- "Application/x-ttf"
- "Application/x-Java Script"
- "font/EOT varningszon"
- "font/ttf"
- "font/OTF"
- "font/OpenType"
- "bild/SVG + XML"
- "text/CSS"
- "text/CSV"
- "text/html"
- "text/Java Script"
- "text/js", "text/plain"
- "text/RichText"
- "text/tabbavgränsade-values"
- "text/xml"
- "text/x-skript"
- "text/x-komponent"
- "text/x-Java-källa"

Dessutom måste filen också vara mellan 1 KB och 8 MB i storlek.

Dessa profiler stöder följande komprimerings kodningar:
- [Gzip (GNU zip)](https://en.wikipedia.org/wiki/Gzip)
- [Brotli](https://en.wikipedia.org/wiki/Brotli)

Om en begäran stöder gzip och Brotli komprimering prioriteras Brotli-komprimeringen.</br>
När en begäran för en till gång anger komprimering och begäran resulterar i en cache missar, utför front dörren komprimering av till gången direkt på POP-servern. Efteråt hanteras den komprimerade filen från cachen. Det resulterande objektet returneras med en överförings kodning: segment.

## <a name="query-string-behavior"></a>Fråga om sträng beteende
Med front dörren kan du styra hur filer cachelagras för en webbegäran som innehåller en frågesträng. I en webbegäran med en frågesträng är frågesträngen den del av begäran som inträffar efter ett frågetecken (?). En frågesträng kan innehålla ett eller flera nyckel/värde-par, där fält namnet och dess värde skiljs åt av ett likhets tecken (=). Varje nyckel/värde-par avgränsas med ett et-tecken (&). Till exempel `http://www.contoso.com/content.mov?field1=value1&field2=value2`. Om det finns fler än ett nyckel/värde-par i en frågesträng i en begäran spelar det ingen roll.
- **Ignorera frågesträngar**: standard läge. I det här läget skickar front dörren de frågesträngar från begär Ande till Server delen på den första begäran och cachelagrar till gången. Alla efterföljande begär Anden för till gången som hanteras från Front dörrens miljö ignorerar frågesträngarna tills den cachelagrade till gången upphör att gälla.

- **Cachelagra varje unik URL**: i det här läget behandlas varje begäran med en unik URL, inklusive frågesträngen, som en unik till gång med sin egen cache. Till exempel, svaret från Server delen för en begäran om `www.example.ashx?q=test1` cachelagras i front dörrens miljö och returneras för efterföljande cacheminnen med samma frågesträng. En begäran om `www.example.ashx?q=test2` cachelagras som en separat till gång med en egen tids-till-Live-inställning.

## <a name="cache-purge"></a>Rensning av cache

Frontend-dörr cachelagrar till gångar tills till gångens TTL (Time-to-Live) går ut. När TTL-värdet för till gången har upphört att gälla när en klient begär till gång, hämtar den främre dörr miljön en ny uppdaterad kopia av till gången för att betjäna klientbegäran och lagra cachen.

Det bästa sättet att se till att användarna alltid får den senaste kopian av dina till gångar är att version till dina till gångar för varje uppdatering och publicera dem som nya URL: er. Front dörren kommer omedelbart att hämta de nya till gångarna för nästa klient begär Anden. Ibland kanske du vill rensa cachelagrat innehåll från alla Edge-noder och tvinga dem att hämta nya, uppdaterade till gångar. Detta kan bero på uppdateringar i ditt webb program eller för att snabbt uppdatera till gångar som innehåller felaktig information.

Välj de till gångar som du vill rensa från Edge-noderna. Om du vill rensa alla till gångar väljer du **Rensa alla**. Annars anger du sökvägen till varje till gång som du vill rensa i **sökväg**.

Dessa format stöds i listor med sökvägar som ska rensas:

- **Enkel sökväg rensa**: rensa enskilda till gångar genom att ange den fullständiga sökvägen till till gången (utan protokollet och domänen), med fil namns tillägget, till exempel/Pictures/strasbourg.png;
- **Jokertecken**: asterisk ( \* ) kan användas som jokertecken. Rensa alla mappar, undermappar och filer under en slut punkt med/ \* i sökvägen eller ta bort alla undermappar och filer under en särskild mapp genom att ange mappen följt av/ \* , till exempel/Pictures/ \* .
- **Rot domän rensning**: Rensa roten för slut punkten med "/" i sökvägen.

> [!NOTE]
> **Rensar jokertecken domäner**: om du anger cachelagrade sökvägar för rensning enligt beskrivningen i det här avsnittet gäller inte domäner som är associerade med den främre dörren. För närvarande stöder vi inte att domäner med jokertecken rensas direkt. Du kan rensa sökvägar från vissa under domäner genom att ange specifik-underdomänen och rensnings Sök vägen. Till exempel, om min frontend-dörr har `*.contoso.com` , kan jag rensa till gångar i min under domän `foo.contoso.com` genom att skriva `foo.contoso.com/path/*` . För närvarande är det imited att ange värd namn i rensnings innehålls Sök vägen, om det är tillämpligt.
>

Rensningar av cacheminnen på front dörren är inte Skift läges känsliga. Dessutom är de frågesträngen oberoende, vilket innebär att en URL rensas och alla frågesträngar tas bort. 

## <a name="cache-expiration"></a>Förfallo tid för cache
Följande ordning på rubriker används för att avgöra hur länge ett objekt lagras i cacheminnet:</br>
1. Cache-Control: s-MaxAge =\<seconds>
2. Cache-kontroll: max-ålder =\<seconds>
3. Upphör att gälla \<http-date>

Cache-Control Response-huvuden som indikerar att svaret inte cachelagras, t. ex. Cache-Control: Private, Cache-Control: no-cache och Cache-Control: No-Store har besvarats. Men om det finns flera begär anden i en POP för samma URL, kan de dela svaret. Om det inte finns någon cache-kontroll är standard beteendet att AFD cachelagrar resursen för X-tid där X plockas slumpmässigt mellan 1 och tre dagar.

## <a name="request-headers"></a>Begärandehuvuden

Följande begärandehuvuden kommer inte att vidarebefordras till en server del när cachelagring används.
- Innehålls längd
- Överförings kodning

## <a name="cache-duration"></a>Varaktighet för cache

Varaktigheten för cachen kan konfigureras i både front dörrs designer och i regel motorn. Varaktigheten för cachen som angetts i ytterdörr designer är den minsta cache-tiden. Den här åsidosättningen fungerar inte om Cache Control-huvudet från ursprunget har fler TTL än värdet för åsidosättning. 

Varaktigheten för cachen som ställts in via regel motorn är en True cache-åsidosättning, vilket innebär att det använder värdet override, oavsett vad ursprunget svars rubrik är.

## <a name="next-steps"></a>Nästa steg

- Läs hur du [skapar en Front Door](quickstart-create-front-door.md).
- Läs [hur Front Door fungerar](front-door-routing-architecture.md).
