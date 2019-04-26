---
title: 'Använd och visa kraven för API: er för Bing-sökresultat'
titleSuffix: Azure Cognitive Services
description: 'Krav för att visa sökresultat från API: er för Bing-sökresultat i dina program.'
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: aahi
ms.openlocfilehash: 5575668f164b97142e7c4b2ddb2608c3173426a6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60499893"
---
# <a name="bing-search-api-use-and-display-requirements"></a>Användnings- och visningskrav för API:er för Bing-sökresultat

Dessa användning och visa krav gäller för varje tillämpning av innehåll och tillhörande information från följande Bing Search API: erna, inklusive relationer, metadata och andra tecken.

- Anpassad sökning i Bing
- Entitetssökning i Bing
- Bildsökning i Bing
- Nyhetssökning i Bing
- Videosökning i Bing
- Bings visuella sökning
- Webbsökning i Bing
- Stavningskontroll i Bing
- Automatiska förslag i Bing

## <a name="definitions"></a>Definitioner


|Period  |Beskrivning  |
|---------|---------|
|Svar     | En kategori med resultatet som returneras i svaret. Ett svar från Bing Web Search API kan exempelvis innehålla svar i kategorier av webbsidan resultat, bild, video, visuella objekt och nyheter. |
|Svar     | Eventuella och alla svar och associerade data som tas emot som svar på ett enda anrop till ett Search-API. |
|Resultat    | Objekt av informationen i ett svar. Till exempel är uppsättning data anslutna med en enda nyhetsartikel ett resultat i ett nytt svar. |
|Sök API:er    | sammantaget, Bing Custom Search, API för Entitetssökning, bildsökning, nyhetssökning, Videosökningsresultat, Visual Search, lokala företag Sök och Web Search API: er. |

## <a name="bing-spell-check-and-bing-autosuggest-api-restrictions"></a>Begränsningar för stavningskontroll i Bing och automatiska förslag i Bing

Gör inte:

- Kopiera, lagra eller cachelagra alla data som du får från stavningskontroll i Bing eller Bing automatiska förslag i API: er.
- Använda data som du får från stavningskontroll i Bing eller Bing automatiska förslag i API: er som en del av alla machine learning eller liknande algoritmiska aktivitet. Använd inte dessa data att träna, utvärdera och förbättra nya eller befintliga tjänster som du eller tredje part kan erbjuda.

## <a name="bing-search-apis"></a>API:er för Bing-sökresultat

> [!NOTE]
> Kraven i det här avsnittet gäller bara den Search API: er, vilket inte inkluderar stavningskontroll i Bing eller automatiska förslag i Bing. 

### <a name="internet-search-experience-requirements"></a>Krav för Internet-upplevelse

Alla data som returneras i svar kan bara användas i sökupplevelser för internet. En internet-sökupplevelse innebär det innehåll som visas: 

- Är relevant och snabbt på slutanvändarens direct query eller andra tecken på sina search intresse och avsikt (till exempel en användare som anges sökfråga). 

- Hjälper användarna att hitta och gå till det svaret datakällor. Till exempel med klickbara länkar från hyperlänkar i svaret.

- Innehåller flera resultat för användaren kan välja mellan. 

- Finns i en placering som gör att användarna kan söka efter.

- Innehåller en synliga tecken på att innehållet är en internet-sökresultat. Till exempel en instruktion som innehållet är ”från webben”.

- Innehåller andra lämpliga åtgärder för att säkerställa att dina API: et för Bing Search-data inte kränker någon mot gällande lagar och tredje parts rättigheter. Konsultationen din juridiska rådgivare att avgöra vilka mått kan vara lämpliga.

Det enda undantaget kraven internet search upplevelse är för identifiering av URL: en, enligt beskrivningen nedan. 

### <a name="restrictions"></a>Begränsningar

Gör inte:

- Kopiera, lagra eller cachelagra data från svar (förutom kvarhållning i den utsträckning som tillåts av [replikeringsmodellen](#continuity-of-service). 

- Använda data som tas emot från Sök-API: er som en del av alla machine learning eller liknande algoritmiska aktivitet. Använd inte dessa data att träna, utvärdera och förbättra nya eller befintliga tjänster som du eller tredje part kan erbjuda.

- Ändra innehållet i resultatet (annat än att formatera om dem på ett sätt som inte kränker någon andra krav), såvida inte krävs enligt lag eller godkänt av Microsoft. 

- Utelämna attribution information och URL: er som är associerade med resultatet innehåll.

- Ändra ordning, t.ex. genom att utelämnande resultaten som visas i ett svar när en order eller rangordning anges, såvida inte krävs enligt lag eller godkänt av Microsoft. 

    > [!NOTE]
    > Det här kravet gäller inte för sortering implementerats via portalen för API: et för Bing Custom Search.

- Visa andra innehåll i någon del av ett svar på ett sätt som skulle leda till en användare att tro att det andra innehållet är en del av svaret. 

- Visa reklam som inte levereras av Microsoft på en sida som visar någon del av ett svar. 

- Visa reklam på sidor med svar:
    - Från Bing-bild, nyhetssökning, Videosökning eller Visual Search API: er
    - Som filtreras eller begränsad främst (eller enbart) till avbildningen, nyheter och/eller video- eller visual search-resultaten.

### <a name="notices-and-branding"></a>Information om och anpassning 
Gör du:

- Inkludera en funktionell hyperlänk till på en framträdande plats i [Microsoft Privacy Statement](https://go.microsoft.com/fwlink/?LinkId=521839), nära varje punkt i användarupplevelsen (UX) som erbjuder en användare möjlighet att ange en sökfråga. Etiketten på hyperlänken **Microsoft Privacy Statement**.

- Visa på en framträdande plats Bing anpassning, konsekvent med den [Bing riktlinjer](https://go.microsoft.com/fwlink/?linkid=833278), nära varje punkt i UX-Gränssnittet som erbjuder en användare möjlighet att ange en sökfråga. Anpassning av sådana måste tydligt tillstånd till användaren som Microsoft är sökupplevelsen internet.

- Du kan attributet varje svar (eller del av ett svar) som visas från Bing-webbsökning, bildsökning, nyhetssökning, Videosökning och Visual Search API: er till Microsoft, såvida inte Microsoft anger något annat skriftligen för din användning. Detta beskrivs i [Bing riktlinjer](https://go.microsoft.com/fwlink/?linkid=833278). 

Gör inte:

- Attributet svar (eller delar av svar) visas från Bing Custom Search API till Microsoft, såvida inte Microsoft anger något annat skriftligen för viss bruk.

### <a name="transferring-responses"></a>Överföring svar

Om du aktiverar en användare att överföra ett svar från ett Sök-API till en annan användare, till exempel via en app för meddelanden eller sociala medier bokföring, följande gäller: 

- Överförda svar måste:
  - Bestå av innehåll som är oförändrade från innehållet i svaren visas för överföring användaren. Formateringsändringar är tillåtna.
  - Inte innehåller några data i form av metadata.
  - Visningsspråket som anger svaret har hämtats via en internet-sökupplevelse som drivs av Bing på svar från Bing webb, bild, nyheter, Video och Visual API: er. Exempel: du kan visa språk, till exempel ”drivs av Bing” eller ”Läs” mer om den här avbildningen i Bing eller du kan använda Bing-logotyp.
  - Visa språk som du anger svaret har hämtats via en internet-sökupplevelse på svar från Bing Custom Search API. Du kan till exempel visa språk, till exempel ”Läs mer om det här sökresultatet”.
  - Visa den fullständiga fråga som används för att generera svaret på en framträdande plats.
  - Inkludera en framstående länk eller liknande information till den underliggande källan i svaret, antingen direkt eller via sökmotor (bing.com, m.bing.com eller din anpassade search-tjänst, så är tillämpligt).
- Du kan inte automatisera överföringen av svar. En överföring måste initieras av en användaråtgärd och ett försök gjordes att överföra ett svar på ett tydligt.
- Du kan bara aktivera en användare att överföra svar som visades som svar på överföring användarens fråga.

### <a name="continuity-of-service"></a>Tjänstens stabilitet 

Kopiera, lagra, eller inte cachelagra data från Sök-API-svar. Om du vill aktivera affärskontinuitet service åtkomst och dataåtergivning, kan du dock behålla resultaten enbart under följande förhållanden:

#### <a name="device"></a>Enhet

Du kan aktivera en användare att behålla resultaten på en enhet för den mindre av (i) 24 timmar från att frågan, eller (ii) tills en användare skickar en annan fråga för uppdaterade resultat, förutsatt att sparade resultat får användas endast:

- Att aktivera att användaren kommer åt resultat som tidigare returnerats till den användaren på enheten (till exempel när det gäller avbrott i tjänsten).
- För att lagra resultatet som returneras för proaktiv frågan anpassade i väntan på användarens behov, baserat på användarens signaler (till exempel när det gäller förväntade tjänstavbrott).

#### <a name="server"></a>Server

Du kan behålla resultaten som är specifika för en enskild användare på ett säkert sätt på en server som du kan styra och visa sparade resultatet:

- Att aktivera att användaren kommer åt en historisk rapport över resultat som tidigare returnerats till användaren i din lösning. Resultaten kan inte (i) kvar i mer än 21 dagar från tidpunkten för slutanvändarens första fråga och (ii) visas som svar på en användares nya eller upprepade fråga.
- Anpassade i väntan på användarens behov, baserat på användarens signaler att lagra resultatet som returneras för proaktiv frågan. Du kan lagra dessa resultat för den mindre av (i) 24 timmar från att frågan, eller (ii) tills en användare skickar en annan fråga för uppdaterade resultat.

När det är kvar, kan inte resultat för en viss användare vara commingled med resultat för en annan användare. Det vill säga resultatet av varje användare behålls och levererat separat.

### <a name="general"></a>Allmänt 

För alla sparade resultat:

- Inkludera ett meddelande om tydliga, visas den tid som frågan har skickats.
- Finns användarna med en knapp eller liknande innebär att en ny fråga och få uppdaterade resultat. 
- Behålla Bing företagsanpassning vid utformning av resultaten.
- ta bort (och uppdatera med en ny fråga om det behövs) lagrade resultaten inom tidsramar som angetts.

### <a name="non-display-url-discovery"></a>Identifiering av icke-Visa URL 

Du bara använda Sök efter svar i en icke-internet sökfunktion för att upptäcka URL: er för informationskällor som svarar på en fråga från dina användare eller en kund. Du kan kopiera dessa URL: er i en rapport eller liknande svar som du anger:

- Endast till användaren eller kund som svar på frågan.
- Endast om den innehåller betydande ytterligare värdefullt innehåll, relevanta för frågan.

I föregående avsnitt av Sök-API: er använder och visa krav gäller inte för den här icke-visa användning, förutom följande: 

- Inte cachelagra, kopiera eller spara data eller innehåll från, eller som är härledda från Sök-svaret än begränsad URL kopiering som beskrivs ovan.
- Se till att din användning av data (inklusive URL: er) som tas emot från API: er för sökresultat inte kränker någon mot gällande lagar och tredje parts rättigheter.
- Använd inte data (inklusive URL: er) togs emot från API: er för sökresultat som en del av search index eller machine learning eller liknande algoritmiska aktivitet. Använd inte dessa data för att skapa train, utvärdera eller förbättra tjänster som du eller tredje part kan erbjuda.

## <a name="gdpr-compliance"></a>GDPR-efterlevnad  

Du förstår att du och Microsoft är med avseende på alla personliga data omfattas av EU allmänna Dataskyddsförordningen (GDPR) och som har bearbetats i samband med anrop till API: er sökresultat, stavningskontroll i Bing eller automatiska förslag i Bing oberoende data-styrenheter enligt GDPR. Du är oberoende av varandra ansvarig för din efterlevnad med GDPR.  

