---
title: Använd och visa kraven - projektet Svarssökning
titlesuffix: Azure Cognitive Services
description: Använd och visa kraven för projektet Svarssökning-slutpunkten.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: project-answer-search
ms.topic: conceptual
ms.date: 04/13/2018
ms.author: rosh
ms.openlocfilehash: 0869833ec83c40f061bebf1aeb1267f44dcbb048
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2018
ms.locfileid: "48868263"
---
# <a name="project-answer-search-use-and-display-requirements"></a>Projektet Svarssökning användning och visa krav

Användning och visa krav gäller för varje tillämpning av innehåll och tillhörande information, till exempel relationer, metadata och andra signaler, som är tillgängliga via anrop till Bing Kunskapssökning, Bing Custom Search, API för Entitetssökning, bildsökning, Nyhetssökning, Videosökning, Visual Search webbsökning, stavningskontroll och automatiska förslag i API: er. Implementeringsdetaljer relaterade till dessa krav finns i dokumentationen för specifika funktioner och resultat.

## <a name="1-bing-spell-check-and-bing-autosuggest-api"></a>1. Automatiska förslag i API för stavningskontroll i Bing och Bing.

Gör inte:

- Kopiera, lagra eller cachelagra alla data som du får från stavningskontroll i Bing eller Bing automatiska förslag i API: er
- använda data som du får från stavningskontroll i Bing eller Bing automatiska förslag i API: er som en del av alla machine learning eller liknande algoritmiska aktivitet för att träna, utvärdera eller förbättra nya eller befintliga tjänster som du eller tredje part kan erbjuda.

## <a name="2-definitions"></a>2. Definitioner

- ”svar” syftar på en viss kategori av resultatet som returneras i svaret. Ett svar från Bing Web Search API kan till exempel innehålla svar i kategorier av webbsidan resultat, bild, video och nyheter;
- ”svar” innebär att alla svar och tillhörande data som tas emot som svar på ett enda anrop till Search API: et
- ”resultat” refererar till objekt av informationen i ett svar. Till exempel är uppsättning data anslutna med en enda nyhetsartikel ett resultat i ett nytt svar.
- ”Search API: er” innebär kallas gemensamt Bing Custom Search, API för Entitetssökning, bildsökning, nyhetssökning, Videosökningsresultat, Visual Search och Web Search API: er. 


## <a name="3-search-apis"></a>3. Sök API:er

Kraven i detta avsnitt 3 gäller för API: er för sökresultat.

**EN. Internet sökupplevelse.** Alla data som returneras i svar kan bara användas i sökupplevelser för internet. En internet-sökupplevelse innebär att innehållet visas, beroende: 
- är relevanta och svarar till slutanvändarens direct query eller andra indikation på användarens search intresse och avsikt (till exempel användare som anges sökfråga); 
- hjälper användarna att hitta och navigera till datakällorna (till exempel de tillhandahållna URL: er implementeras som hyperlänkar så att innehållet eller attribution jobbegäran synligt visas med data); eller om för Entitetssökning i Bing, så att det syns länkar till bing.com URL: en i ett svar som gör att användaren kan navigera till sökresultatet för relevanta frågan på bing.com;
- innehåller flera resultat för användaren att välja från (till exempel om flera visas resultaten från svaret nyheter eller alla resultat om färre än flera returneras); 
- är begränsad till ett belopp som är lämpligt att har search syfte (till exempel bilder miniatyrer miniatyr-storlek i förhållande till användarens visning) 
- innehåller synlig indikation för slutanvändaren att innehållet är resultat från Internet (till exempel en instruktion att innehållet är ”från webben”), och
- en annan kombination av mått som är lämpliga för din användning av data som tas emot från Sök-API: er inte kränker någon mot gällande lagar och tredje parts rättigheter (till exempel om förlita sig på en Creative Commons-licens som uppfyller de tillämpliga licensen villkor). Konsultationen din juridiska rådgivare att avgöra vilka mått kan vara lämpliga.
Det enda undantaget internet search upplevelse kravet är för identifiering av URL: en enligt beskrivningen i avsnittet 3E (icke-Visningswebbadress discovery) efter. 

**B. Begränsningar.** Gör inte:

- Kopiera, lagra eller cachelagra data från svar (utom kvarhållning i den utsträckning som tillåts av ”affärskontinuitet för tjänsten” avsnittet efter;) 
- använda data som tas emot från Sök-API: er som en del av alla machine learning eller liknande algoritmiska aktivitet för att träna, utvärdera och förbättra nya eller befintliga tjänster som du eller tredje part kan erbjuda.
- ändra innehållet i resultatet (annat än att formatera om dem på ett sätt som inte kränker någon andra krav), såvida inte krävs enligt lag eller godkänts av Microsoft. 
- Utelämna attribution och URL: er som är associerade med resultatet innehåll.
- Ändra ordning, t.ex. genom att utelämnande resultat som visas i ett svar när en order eller rangordning tillhandahålls (för anpassad sökning i Bing, gäller inte den här regeln för sortering implementeras via portalen customsearch.ai), såvida inte krävs enligt lag eller godkänts av Microsoft ;
- Visa andra innehåll i någon del av ett svar på ett sätt som skulle leda till en användare att tro att det andra innehållet är en del av svaret; 
- Visa reklam som inte levereras av Microsoft på en sida som visar någon del av ett svar; – Visa reklam med svar (i) från Bing-bild, nyheter eller Video Search API: er; eller (ii) som filtreras eller begränsad främst (eller enbart) till avbildningen, nyheter och/eller video resultat.

**C. Information om och anpassning.** 

- På en framträdande plats inkludera en funktionell hyperlänk till sekretesspolicyn för Microsoft, på https://go.microsoft.com/fwlink/?LinkId=521839, nära varje punkt i användarupplevelsen (UX) som erbjuder en användare möjlighet att ange en sökfråga. Etiketten hyperlink ”Microsoft Privacy Statement”.
- Visa på en framträdande plats Bing anpassning, följer riktlinjerna som är tillgängliga på https://go.microsoft.com/fwlink/?linkid=833278, nära varje punkt i UX-Gränssnittet som erbjuder en användare möjlighet att ange en sökfråga.  Anpassning av sådana måste tydligt ange för användaren att Microsoft är sökupplevelsen internet.
- Du kan attributet varje svar (eller del av ett svar) som visas från Bing webb, bild, nyheter och API: er för Video till Microsoft som beskrivs i https://go.microsoft.com/fwlink/?linkid=833278, såvida inte Microsoft anger något annat skriftligen för din användning. 
- Inte attributet svar (eller delar av svar) visas från Bing Custom Search API till Microsoft, såvida inte Microsoft anger något annat skriftligen för viss bruk.


**D. Överför svar.** Om du aktiverar en användare att överföra ett svar från ett Sök-API till en annan användare, till exempel via en app för meddelanden eller sociala medier bokföring, följande gäller: 
- Överförda svar måste:
  - Består av innehåll som är oförändrade från innehållet i svaren visas för användaren överföring (formateringsändringar är tillåtna);
  - Inte innehåller några data i form av metadata.
  - På svar från Bing webb, bild, nyheter och API: er för Video visningsspråket som anger svaret har hämtats via en internet-sökupplevelse som drivs av Bing (till exempel ”drivs av Bing” ”mer information om den här avbildningen på Bing” eller med hjälp av Bing-logotyp).
  - På svar från API: et för Bing Custom Search har visningsspråket som anger svaret hämtats via en internet-sökupplevelse (till exempel ”Läs mer om det här sökresultatet”);
  - På en framträdande plats visas den fullständiga fråga som används för att generera svar; och
  - Inkludera en framstående länk eller liknande information till den underliggande källan i svaret, antingen direkt eller via sökmotor (bing.com, m.bing.com eller din anpassade search-tjänst, så är tillämpligt).
- Du kan inte automatisera överföringen av svar. En överföring måste initieras av en användaråtgärd och ett försök gjordes att överföra ett svar på ett tydligt.
- Du kan bara aktivera en användare att överföra svar som visades som svar på överföring användarens fråga.

**E. Tjänstens stabilitet.** Kopiera, lagra, eller inte cachelagra data från Sök-API-svar. Om du vill aktivera affärskontinuitet service åtkomst och dataåtergivning, kan du dock behålla resultaten enbart under följande förhållanden:

**Enheten.** Du kan aktivera en användare att behålla resultaten på en enhet för den mindre av (i) 24 timmar från att frågan eller (ii) tills en användare skickar en annan fråga för uppdaterade resultat, förutsatt att sparade resultat får användas endast:

- att aktivera användaren åtkomst till resultat som tidigare returnerats till den användaren på enheten (till exempel när det gäller avbrott i tjänsten); eller
- för att lagra resultatet som returneras för proaktiv frågan anpassade i väntan på slutanvändarens behov baserat på slutanvändarens signaler (till exempel när det gäller förväntade tjänstavbrott).

**Server.** Du kan behålla resultaten som är specifika för en enda slutanvändare på ett säkert sätt på en server som du kontrollerar och visa de sparade resultat:

- att aktivera användaren åtkomst till en historisk rapport över resultat tidigare returneras till användaren i din lösning, förutsatt att resultatet inte ska (i) bevaras i mer än 21 dagar från tidpunkten för slutanvändarens första fråga och (ii) visas som svar på ett omfattande u ser's nya eller upprepade frågan. eller
- anpassade i väntan på användarens behov baserat på slutanvändarens signaler för det kortare av (i) 24 timmar från att frågan eller (ii) tills en användare skickar en annan fråga för uppdaterade resultat för att lagra resultatet som returneras för proaktiv frågan.

När det är kvar, resultat för en viss användare kan inte commingled med resultat för en annan användare, d.v.s., resultatet av varje användare måste vara kvar och levererat separat.

**Allmänt.** För alla sparade resultat:

- Inkludera ett meddelande om tydliga, visas den tid som frågan har skickats
- presentera en knapp eller liknande innebär att en ny fråga och få uppdaterade resultat 
- behålla Bing företagsanpassning vid utformning av resultaten och
- ta bort (och uppdatera med en ny fråga om det behövs) lagrade resultaten inom tidsramar som angetts.

**F. Icke-Visa URL: en identifiering.** Du bara använda Sök efter svar i en icke-internet sökfunktion för att upptäcka URL: er för informationskällor som svarar på en fråga från dina användare eller en kund. Du kan kopiera dessa URL: er i en rapport eller liknande svar som du anger (i) endast till användaren eller kunden, som svar på frågan och (ii) som innehåller betydande ytterligare värdefullt innehåll relevanta för frågan. Kraven i avsnitt 3A via 3E av dessa användning och visa krav gäller inte för den här icke-visa användning, utom: 

- Du bör inte cachelagra, kopiera eller lagra data eller innehåll från eller härledd från Sök-svaret än begränsad URL kopiering beskrivits;
- Se till att din användning av data (inklusive URL: er) som tas emot från API: er för sökresultat inte kränker någon mot gällande lagar och tredje parts rättigheter. och
- Du får inte använda uppgifterna (inklusive URL: er) från API: er för sökresultat som en del av alla sökindex eller machine learning eller liknande algoritmiska aktivitet för att skapa train, utvärdera eller förbättra tjänster som du eller tredje part kan erbjuda.

## <a name="next-steps"></a>Nästa steg
[Besvara över Search](overview.md)
