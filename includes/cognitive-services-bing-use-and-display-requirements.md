---
title: ta med fil
description: ta med fil
services: cognitive-services
author: MikeDodaro
ms.service: cognitive-services
ms.topic: include
ms.custom: include file
ms.date: 04/19/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: f166ceac1ae848565f861a94781ce0500c24747e
ms.sourcegitcommit: 02ce0fc22a71796f08a9aa20c76e2fa40eb2f10a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2018
ms.locfileid: "51289163"
---
Användning och visa krav gäller för varje tillämpning av innehåll och tillhörande information. Till exempel gäller för relationer, metadata och andra tecken. Det kan vara tillgänglig via anropa följande API: er:

- Anpassad sökning i Bing
- Entitetssökning i Bing
- Bildsökning i Bing
- Nyhetssökning i Bing
- Videosökning i Bing
- Visuell sökning i Bing
- Webbsökning i Bing
- Stavningskontroll i Bing
- Automatiska förslag i Bing

Du kan hitta implementeringsdetaljer relaterade till dessa krav i dokumentationen för specifika funktioner och resultat.     

## <a name="bing-spell-check-and-bing-autosuggest-apis"></a>Stavningskontroll i Bing och Bing automatiska förslag i API: er

Gör inte:

- Kopiera, lagra eller cachelagra alla data som du får från stavningskontroll i Bing eller Bing automatiska förslag i API: er.
- Använda data som du får från stavningskontroll i Bing eller Bing automatiska förslag i API: er som en del av alla machine learning eller liknande algoritmiska aktivitet. Använd inte dessa data att träna, utvärdera och förbättra nya eller befintliga tjänster som du eller tredje part kan erbjuda.

## <a name="definitions"></a>Definitioner

- *Svar* refererar till en viss kategori av resultatet som returneras i svaret. Ett svar från Bing Web Search API kan exempelvis innehålla svar i kategorier av webbsidan resultat, bild, video, visuella objekt och nyheter.   
- *Svaret* innebär att alla svar och tillhörande data som tas emot som svar på ett enda anrop till ett Search-API.
- *Resultatet* refererar till objekt av informationen i ett svar. Till exempel är uppsättning data anslutna med en enda nyhetsartikel ett resultat i ett nytt svar.
- *Sök API: er* kallas gemensamt innebär Bing Custom Search, API för Entitetssökning, bildsökning, nyhetssökning, Videosökningsresultat, Visual Search och Web Search API: er. 


## <a name="search-apis"></a>Sök API:er

Kraven i det här avsnittet gäller för API: er för sökresultat. API: er för sökresultat omfattar inte stavningskontroll i Bing eller automatiska förslag i Bing. Kraven för dessa två API: er beskrivs i föregående avsnitt.

### <a name="internet-search-experience"></a>Internet sökupplevelse

Alla data som returneras i svar kan bara användas i sökupplevelser för internet. En internet-sökupplevelse innebär att innehållet visas, beroende: 
- Är relevant och snabbt på slutanvändarens direct query eller andra indikation på användarens search intresse och avsikt (till exempel en användare som anges sökfråga). 
- Hjälper användarna att hitta och navigera till datakällorna (till exempel de tillhandahållna URL: er implementeras som hyperlänkar, så att innehållet eller attribution jobbegäran synligt visas med data). Eller, om du använder API för Entitetssökning i Bing, så att det syns länkar till bing.com URL: en i ett svar som gör att användaren kan navigera till sökresultatet för relevanta frågan på bing.com.
- Innehåller flera resultat för användaren kan välja mellan (till exempel flera visas resultaten från svaret nyheter eller alla resultat om färre än flera returneras). 
- Är begränsad till ett belopp som är lämpligt att har search syfte (exempelvis bild som miniatyrer miniatyr-storlek i förhållande till användarens visning). 
- Innehåller en synlig indikation för användaren att innehållet är resultat från internet (till exempel en instruktion att innehållet är ”från webben”).
- Innehåller en annan kombination av mått som är lämpliga för din användning av data som tas emot från Sök-API: er inte kränker någon mot gällande lagar och tredje parts rättigheter. Om du lita på en Creative Commons-licens, kompatibel med tillämpliga licensvillkoren. Konsultationen din juridiska rådgivare att avgöra vilka mått kan vara lämpliga.
Det enda undantaget internet search upplevelse kravet är för identifiering av URL: en, enligt beskrivningen nedan. 

### <a name="restrictions"></a>Begränsningar

Gör inte:

- Kopiera, lagra eller cachelagra data från svar (utom kvarhållning i den utsträckning som tillåts av avsnittet ”affärskontinuitet för tjänsten” senare i den här artikeln). 
- Använda data som tas emot från Sök-API: er som en del av alla machine learning eller liknande algoritmiska aktivitet. Använd inte dessa data att träna, utvärdera och förbättra nya eller befintliga tjänster som du eller tredje part kan erbjuda.
- Ändra innehållet i resultatet (annat än att formatera om dem på ett sätt som inte kränker någon andra krav), såvida inte krävs enligt lag eller godkänt av Microsoft. 
- Utelämna attribution och URL: er som är associerade med resultatet innehåll.
- Ändra ordning, t.ex. genom att utelämnande, resulterar visas i ett svar när en order eller rangordning anges, såvida inte krävs enligt lag eller godkänt av Microsoft. (För anpassad sökning i Bing gäller inte den här regeln att sortering implementeras via portalen customsearch.ai.)
- Visa andra innehåll i någon del av ett svar på ett sätt som skulle leda till en användare att tro att det andra innehållet är en del av svaret. 
- Visa reklam som inte levereras av Microsoft på en sida som visar någon del av ett svar. 
- Visa reklam med svar (i) från Bing-bild, nyhetssökning, Videosökning eller Visual Search API: er; eller (ii) som filtreras eller begränsad främst (eller enbart) till avbildningen, nyheter och/eller video eller visuella resultat.

### <a name="notices-and-branding"></a>Information om och anpassning 

- Inkludera en funktionell hyperlänk till på en framträdande plats i [Microsoft Privacy Statement](https://go.microsoft.com/fwlink/?LinkId=521839), nära varje punkt i användarupplevelsen (UX) som erbjuder en användare möjlighet att ange en sökfråga. Etiketten på hyperlänken **Microsoft Privacy Statement**.
- Visa på en framträdande plats Bing anpassning, konsekvent med den [Bing riktlinjer](https://go.microsoft.com/fwlink/?linkid=833278), nära varje punkt i UX-Gränssnittet som erbjuder en användare möjlighet att ange en sökfråga. Anpassning av sådana måste tydligt ange för användaren att Microsoft är sökupplevelsen internet.
- Du kan attributet varje svar (eller del av ett svar) som visas från Bing-webbsökning, bildsökning, nyhetssökning, Videosökning och Visual Search API: er till Microsoft, såvida inte Microsoft anger något annat skriftligen för din användning. Detta beskrivs i [Bing riktlinjer](https://go.microsoft.com/fwlink/?linkid=833278). 
- Inte attributet svar (eller delar av svar) visas från Bing Custom Search API till Microsoft, såvida inte Microsoft anger något annat skriftligen för viss bruk.

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

**Enheten.** Du kan aktivera en användare att behålla resultaten på en enhet för den mindre av (i) 24 timmar från att frågan, eller (ii) tills en användare skickar en annan fråga för uppdaterade resultat, förutsatt att sparade resultat får användas endast:

- Att aktivera att användaren kommer åt resultat som tidigare returnerats till den användaren på enheten (till exempel när det gäller avbrott i tjänsten).
- För att lagra resultatet som returneras för proaktiv frågan anpassade i väntan på användarens behov, baserat på användarens signaler (till exempel när det gäller förväntade tjänstavbrott).

**Server.** Du kan behålla resultaten som är specifika för en enskild användare på ett säkert sätt på en server som du kan styra och visa sparade resultatet:

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

## <a name="gdpr-compliance"></a>GDPR-kompatibilitet  

Du förstår att du och Microsoft är med avseende på alla personliga data omfattas av EU allmänna Dataskyddsförordningen (GDPR) och som har bearbetats i samband med anrop till API: er sökresultat, stavningskontroll i Bing eller automatiska förslag i Bing oberoende data-styrenheter enligt GDPR. Du är oberoende av varandra ansvarig för din efterlevnad med GDPR.  

