---
title: Projektet svaret Sök användning och visa krav - kognitiva Microsoft-tjänster | Microsoft Docs
description: Använd och visa krav för projektet svaret Sök-slutpunkten.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.technology: project-answer-search
ms.topic: article
ms.date: 04/13/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 6e8eaaaa2c83a1420f2de86b23e15f4f19f7a565
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35354135"
---
# <a name="project-answer-search-use-and-display-requirements"></a>Projektet svaret Sök använder och visa krav

Användning och visa krav gäller för någon implementering av innehåll och tillhörande information, till exempel relationer, metadata och andra signaler tillgängliga via anrop till Bing Kunskapssökning, Bing anpassad sökning, entiteten sökning, Image-sökning Nyheter-sökning, Video sökning, Visual Sök webbsökning, stavningskontroll och Autosuggest API: er. Implementeringsdetaljer relaterade till dessa krav finns i dokumentation för specifika funktioner och resultat.

## <a name="1-bing-spell-check-and-bing-autosuggest-api"></a>1. Bing stavningskontroll och Bing Autosuggest API.

Gör inte:

- Kopiera, spara eller cachelagra alla data som du får från Bing stavningskontroll eller Bing Autosuggest API: er
- Använd data du får från Bing stavningskontroll eller Bing Autosuggest API: er som en del av maskininlärning eller liknande algoritmbaserade aktivitet för att träna, utvärdera och förbättra nya eller befintliga tjänster som du eller tredje part kan erbjuda.

## <a name="2-definitions"></a>2. Definitioner

- ”svaret” refererar till en kategori av resultaten i ett svar. Ett svar från Bing Web Sök API kan till exempel innehålla svar i kategorier av webbsidan resultat, bild, video och nyheterna.
- ”svaret” innebär att alla svar och tillhörande data som tas emot som svar på ett enda anrop till en sökning API;
- ”resultat” refererar till ett objekt av information i ett svar. Till exempel beror en uppsättning data som är kopplad till en enda nyhetsartikel i svar en andra gång.
- ”API: er search” innebär gemensamt, Bing anpassad sökning, entiteten sökning, Image-sökning, News Search, Sök Video, Visual Sök och API: er för Web Search. 


## <a name="3-search-apis"></a>3. Sök API:er

Krav i det här avsnittet 3 gäller för API: er för sökning.

**EN. Sökinställningar för Internet.** Alla data som returneras i svar kan endast användas i internet-upplevelser för sökning. En internet-sökinställningar innebär att innehållet visas, i tillämpliga fall: 
- är relevant och svara på slutanvändarens direktfrågan eller andra tecken på användarens Sök intresse och avsikt (till exempel användare anges sökfråga); 
- hjälper användarna att hitta och gå till datakällor för data (till exempel de angivna URL: er implementeras som hyperlänkar så innehåll eller tillskrivningar jobbegäran synligt visas med data); eller om av Bing enheten Sök API synligt länkar till av bing.com Webbadressen som tillhandahålls i svaret som gör att användaren kan navigera i sökresultaten för den aktuella frågan på bing.com;
- innehåller flera resultat för användaren att välja från (till exempel flera resultat från nyheter svaret visas, eller returnera alla resultat om det är färre än flera); 
- är begränsad till ett belopp som är lämpligt att sökningen syfte (exempelvis bilden som miniatyrer är miniatyr storlek i förhållande till användarens bildskärm). 
- innehåller synlig indikation på att användaren att innehållet är sökresultat för Internet (t.ex, en instruktion att innehållet är ”från webben”); och
- innehåller en kombination av åtgärder som är lämpliga för din användning av data från API: er för Search inte bryta mot gällande lagar och tredje parts rättigheter (till exempel om förlita dig på en kreativa Commons-licens som överensstämmer med tillämpliga licensvillkoren villkor). Din juridiska rådgivare att avgöra vilka åtgärder som kan vara lämpliga finns.
Det enda undantaget till internet Sök upplevelse kravet är för URL: en identifiering som beskrivs i avsnittet 3E (icke-Visa URL identifiering) följande. 

**B. Begränsningar.** Gör inte:

- Kopiera, spara eller cachelagra alla data från svar (utom kvarhållning i den utsträckning som tillåts av avsnittet följande ”kontinuitet för tjänsten”); 
- använda data från Sök-API: er som en del av maskininlärning eller liknande algoritmbaserade aktivitet för att träna, utvärdera och förbättra nya eller befintliga tjänster som du eller tredje part kan erbjuda.
- ändra innehållet i resultatet (andra än att formatera dem på ett sätt som inte kränker några andra krav), såvida inte krävs enligt lag eller av Microsoft. 
- Utelämna information och URL: er som är associerade med resultatet innehåll.
- Ändra ordning, t.ex. genom att utelämnande, resultat visas i ett svar när en order eller rangordning tillhandahålls (för Bing anpassad sökning API, gäller inte den här regeln att ordna om implementeras via portalen customsearch.ai), såvida inte krävs enligt lag eller av Microsoft ;
- Visa andra innehållet i någon del av ett svar på ett sätt som skulle leda till en slutanvändare att tro att andra innehållet är en del av svaret; 
- Visa annonser som inte levereras av Microsoft på varje sida som innehåller någon del av ett svar; – Visa reklam med svar (i) från Bing bilden, nyheter eller Video Sök API: er; eller (ii) som är filtrerade eller begränsad främst (eller enbart) till bilden, nyheter och/eller video resultat.

**C. Meddelanden och anpassning.** 

- En framträdande plats innehåller en fungerande hyperlänk till sekretesspolicyn för Microsoft, finns på https://go.microsoft.com/fwlink/?LinkId=521839, nära varje punkt i användarupplevelsen (UX) som erbjuder en användare möjlighet att ange en sökfråga. Etikett hyperlink ”Microsoft Privacy Statement”.
- Visa Bing varumärke, överensstämmer med riktlinjerna som finns på en framträdande plats https://go.microsoft.com/fwlink/?linkid=833278, nära varje punkt i UX som erbjuder en användare möjlighet att ange en sökfråga.  Sådana företagsanpassning måste tydligt anger för användaren att Microsoft startar internet sökinställningar.
- Du kan attributet varje svar (eller delar av ett svar) från Bing Web, bild, nyheter och API: er för Video visas till Microsoft som beskrivs i https://go.microsoft.com/fwlink/?linkid=833278, såvida inte Microsoft anger något annat skriftligt för din användning. 
- Inte attributet svar (eller delar av svar) visas från Bing anpassad sökning API till Microsoft, om inte Microsoft anger något annat i skriva för din specifika användning.


**D. Överför svar.** Om du aktiverar en användare att överföra ett svar från en sökning-API till en annan användare som via en app för meddelanden eller sociala medier bokföring, gäller följande krav: 
- Överförda svar måste:
  - Består av innehåll som är oförändrade från innehållet i de svar som visas för användaren överförs (formateringsändringar är tillåtna);
  - Inte innehåller några data i form av metadata.
  - På svar från Bing Web, bild, nyheter och API: er för Video visningsspråket som anger svaret har hämtats via en internet-sökinställningar funktionalitet från Bing (till exempel ”drivs av Bing” ”mer information om den här avbildningen på Bing” eller använda Bing-logotypen).
  - På svar från Bing anpassad sökning API har visningsspråket som anger svaret hämtats via en internet-sökinställningar (till exempel ”mer information om det här sökresultatet”);
  - Tydligt visa fullständig frågan används för att generera svaret; och
  - Inkludera en framträdande länk eller liknande information till den underliggande datakällan i svaret, antingen direkt eller via sökmotor (bing.com, m.bing.com eller din anpassade söktjänst, i tillämpliga fall).
- Du kan inte automatisera överföringen av svar. En överföring måste initieras av en användaråtgärd tydligt och ett försök gjordes att överföra ett svar.
- Du kan bara aktivera en användare att överföra svar som visades som svar på frågan överförs användaren.

**E. Tjänstens stabilitet.** Kopiera inte, lagra, eller cachelagra alla data från sökning API-svar. Om du vill aktivera kontinuitet i tjänståtkomst och dataåtergivning, kan du dock behålla resultaten enbart följande villkor:

**Enheten.** Du kan aktivera en slutanvändare att behålla resultaten på en enhet för den mindre (i) 24 timmar från tidpunkten frågan eller (ii) tills en användare skickar en annan fråga för uppdaterade resultat, förutsatt att kvarhållna resultat endast får användas:

- att aktivera användaren åtkomst till resultat som tidigare returnerats till att slutanvändare på den enheten (till exempel vid avbrott i tjänsten); eller
- för att lagra resultaten som returnerades för proaktiv frågan personliga i väntan på användarens behov baserat på slutanvändarens signaler (till exempel vid förväntade avbrott).

**Server.** Du kan behålla resultat som är specifika för en enskild slutanvändare på ett säkert sätt på en server som du kan styra och visa kvarhållna resultatet:

- att aktivera användaren åtkomst till Historisk rapport över resultat returneras till användaren i din lösning förutsatt att resultaten inte bevaras kanske (i) mer än 21 dagar från det att slutanvändarens första fråga och (ii) visas som svar på en end u tidigare ser's nya eller upprepade frågan. eller
- anpassade i väntan på en slutanvändare behov baserat på slutanvändarens signaler för mindre (i) 24 timmar från frågan eller (ii) förrän en användare skickar en annan fråga för uppdaterade resultat att lagra resultaten som returnerades för proaktiv frågan.

När behålls resultat för en viss användare kan inte commingled med resultat för en annan användare, d.v.s., resultatet av varje användare måste vara kvar och levererat separat.

**Allmänt.** För alla sparade resultat:

- Inkludera tydliga, visas informationen om den tid som frågan har skickats
- presentera en knapp eller ett liknande sätt att fråga och visa uppdaterade resultat 
- Behåll Bing företagsanpassning vid utformning av resultaten och
- ta bort (och uppdatera med en ny fråga om det behövs) lagrade resultat inom tidsramarna anges.

**F. Icke-Visa URL: en identifiering.** Du kan bara använda sökningssvar i en icke-internet sökinställningar uteslutande av identifiering av URL: er för informationskällor för att svara på en fråga från användaren eller kund. Du kan kopiera dessa URL: er i en rapport eller liknande svar som du anger (i) bara för den användaren eller kund, som svar på frågan och (ii) som innehåller betydande ytterligare värdefullt innehåll relevanta för frågan. Kraven i avsnitt 3A till 3E av dessa krav för användning och visa gäller inte för den här icke-visa användning, utom: 

- Du kan inte cachelagra, kopiera eller lagra data eller innehåll från eller härledd från search-svar än begränsad URL kopiering som beskrivs ovan;
- Se till att din användning av data (inklusive URL: er) som tagits emot från API: er för Search inte bryta mot gällande lagar och tredje parts rättigheter. och
- Du får inte använda data (inklusive URL: er) från Sök-API: er som en del av sökindex eller maskininlärning eller liknande algoritmbaserade aktivitet för att skapa train, utvärdera eller förbättra tjänsterna som du eller tredje part kan erbjuda.

## <a name="next-steps"></a>Nästa steg
[Besvara Sök-översikt](overview.md)
