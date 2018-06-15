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
ms.openlocfilehash: 174af83686eba665a729246be7a477b9a5054f30
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/05/2018
ms.locfileid: "35356078"
---
# <a name="bing-search-api-use-and-display-requirements"></a>API för Bing Search använder och visa krav

Använd och visa krav gäller för någon implementering av innehåll och tillhörande information. Till exempel gäller för relationer, metadata och andra signaler. Det kan vara tillgängliga via anrop till följande API: er:

- Anpassad sökning i Bing
- Entitetssökning i Bing
- Bildsökning i Bing
- Nyhetssökning i Bing
- Videosökning i Bing
- Visuell sökning i Bing
- Webbsökning i Bing
- Stavningskontroll i Bing
- Automatiska förslag i Bing

Du kan hitta implementeringsdetaljer relaterade till dessa krav i dokumentation för specifika funktioner och resultat.     

## <a name="bing-spell-check-and-bing-autosuggest-apis"></a>API: er för Bing stavningskontroll och Bing Autosuggest

Gör inte:

- Kopiera, spara eller cachelagra alla data som du får från Bing stavningskontroll eller Bing Autosuggest API: er.
- Använd data du får från Bing stavningskontroll eller Bing Autosuggest API: er som en del av maskininlärning eller liknande algoritmbaserade aktivitet. Använd inte dessa data att träna, utvärdera och förbättra nya eller befintliga tjänster som du eller tredje part kan erbjuda.

## <a name="definitions"></a>Definitioner

- *Svaret* refererar till en kategori av resultaten i ett svar. Ett svar från Bing Web Sök API kan innehålla svar i kategorier av webbsidan resultat, bild, video, visual och nyheter.   
- *Svaret* innebär att alla svar och tillhörande data som tas emot som svar på ett enda anrop till en sökning-API.
- *Resultatet* refererar till ett objekt av information i ett svar. Till exempel beror en uppsättning data som är kopplad till en enda nyhetsartikel i svar en andra gång.
- *Sök API: er* gemensamt, innebär Bing anpassad sökning, entiteten sökning, Image-sökning, News Search, Sök Video, Visual Sök och Web API: er för sökning. 


## <a name="search-apis"></a>Sök API:er

Krav i det här avsnittet gäller för API: er för sökning. API: er för Search inkluderar inte Bing stavningskontroll eller för Bing Autosuggest. Kraven för dessa två API: erna beskrivs i föregående avsnitt.

### <a name="internet-search-experience"></a>Internet sökinställningar

Alla data som returneras i svar kan endast användas i internet-upplevelser för sökning. En internet-sökinställningar innebär att innehållet visas, i tillämpliga fall: 
- Är relevant och svara på slutanvändarens direktfrågan eller andra tecken på användarens Sök intresse och avsikt (till exempel en användare anges sökfråga). 
- Hjälper användarna att hitta och gå till datakällor för data (till exempel de angivna URL: er implementeras som hyperlänkar, så att innehåll eller tillskrivningar jobbegäran synligt visas med data). Eller, om du använder Bing enheten Sök-API, synligt länken till bing.com URL som anges i svaret som gör att användaren kan navigera i sökresultaten för den aktuella frågan på bing.com.
- Innehåller flera resultat för användaren att välja från (till exempel flera resultat från nyheter svaret visas eller returnera alla resultat om det är färre än flera). 
- Är begränsad till ett belopp som är lämpligt att sökningen syfte (till exempel bild miniatyrer är miniatyr storlek i förhållande till användarens bildskärm). 
- Innehåller en synlig indikation på att användaren att innehållet är sökresultat för internet (t.ex, en instruktion att innehållet är ”från webben”).
- Innehåller en kombination av åtgärder som är lämpliga för din användning av data från API: er för Search inte bryta mot gällande lagar och tredje parts rättigheter. Om du beroende av en kreativa Commons-licens, överensstämmer med tillämpliga licensvillkoren. Din juridiska rådgivare att avgöra vilka åtgärder som kan vara lämpliga finns.
Det enda undantaget till internet Sök upplevelse kravet är för URL: en identifiering som beskrivs senare i den här artikeln. 

### <a name="restrictions"></a>Begränsningar

Gör inte:

- Kopiera, spara eller cachelagra alla data från svar (utom kvarhållning i den utsträckning som tillåts i avsnittet ”kontinuitet för tjänsten” senare i den här artikeln). 
- Använd data togs emot från Sök-API: er som en del av maskininlärning eller liknande algoritmbaserade aktivitet. Använd inte dessa data att träna, utvärdera och förbättra nya eller befintliga tjänster som du eller tredje part kan erbjuda.
- Ändra innehållet i resultatet (andra än att formatera dem på ett sätt som inte kränker några andra krav), såvida inte krävs enligt lag eller godkänt av Microsoft. 
- Utelämna information och URL: er som är associerade med resultatet innehåll.
- Ordna om, t.ex. genom att utelämnande, resultat som visas i ett svar, när en order eller rangordning anges, såvida inte krävs enligt lag eller godkänt av Microsoft. (För Bing anpassad sökning API gäller inte den här regeln att ordna om implementeras via portalen customsearch.ai.)
- Visa andra innehållet i någon del av ett svar på ett sätt som skulle leda till en användare att tro att andra innehållet är en del av svaret. 
- Visa annonser som inte levereras av Microsoft på varje sida som innehåller någon del av ett svar. 
- Visa reklam med svar (i) från Bing bild, News Search, Sök Video eller Visual API: er Search; eller (ii) som är filtrerade eller begränsad främst (eller enbart) till bilden, nyheter och/eller video eller visual resultat.

### <a name="notices-and-branding"></a>Meddelanden och anpassning 

- En framträdande plats innehåller en fungerande hyperlänk till den [sekretesspolicy för Microsoft](https://go.microsoft.com/fwlink/?LinkId=521839), nära varje punkt i användarupplevelsen (UX) som erbjuder en användare möjlighet att ange en sökfråga. Etiketten hyperlänken **sekretesspolicy för Microsoft**.
- Tydligt visa Bing varumärke, konsekvent med den [Bing riktlinjer](https://go.microsoft.com/fwlink/?linkid=833278), nära varje punkt i UX som erbjuder en användare möjlighet att ange en sökfråga. Sådana företagsanpassning måste tydligt anger för användaren att Microsoft startar internet sökinställningar.
- Du kan attributet varje svar (eller delar av ett svar) från Bing webbsökning, Image-sökning, News Search, Sök Video och API: er med Visual Search visas till Microsoft, om inte Microsoft anger något annat skriftligt för din användning. Detta beskrivs i [Bing riktlinjer](https://go.microsoft.com/fwlink/?linkid=833278). 
- Inte attributet svar (eller delar av svar) visas från Bing anpassad sökning API till Microsoft, om inte Microsoft anger något annat i skriva för din specifika användning.

### <a name="transferring-responses"></a>Överför svar

Om du aktiverar en användare att överföra ett svar från en sökning-API till en annan användare som via en app för meddelanden eller sociala medier bokföring, gäller följande krav: 
- Överförda svar måste:
  - Bestå av innehåll som är oförändrade från innehållet i de svar som visas för användaren som överförs. Formateringsändringar är tillåtna.
  - Inte innehåller några data i form av metadata.
  - Visningsspråket som indikerar har hämtats via en internet-sökinställningar funktionalitet från Bing för svar från Bing Web, bild, nyheter, Video och API: er för Visual. Exempelvis kan du visa som ”drivs av Bing” eller ”Lär dig mer” mer information om den här avbildningen på Bing eller du kan använda Bing-logotypen.
  - Visa språk som du anger svaret har hämtats via en internet-sökinställningar på svar från Bing anpassad sökning API. Du kan till exempel visa språk, till exempel ”mer information om det här sökresultatet”.
  - Tydligt visa fullständig frågan används för att generera svaret.
  - Inkludera en framträdande länk eller liknande information till den underliggande datakällan i svaret, antingen direkt eller via sökmotor (bing.com, m.bing.com eller din anpassade söktjänst, i tillämpliga fall).
- Du kan inte automatisera överföringen av svar. En överföring måste initieras av en användaråtgärd tydligt och ett försök gjordes att överföra ett svar.
- Du kan bara aktivera en användare att överföra svar som visades som svar på frågan överförs användaren.

### <a name="continuity-of-service"></a>Tjänstens stabilitet 

Kopiera inte, lagra, eller cachelagra alla data från sökning API-svar. Om du vill aktivera kontinuitet i tjänståtkomst och dataåtergivning, kan du dock behålla resultaten enbart följande villkor:

**enheten.** Du kan aktivera en användare att behålla resultaten på en enhet för den mindre (i) 24 timmar från tidpunkten av frågan eller (ii) förrän en användare skickar en annan fråga för uppdaterade resultat, förutsatt att kvarhållna resultat endast får användas:

- Så här aktiverar du att användaren kommer åt resultat som tidigare returnerats till denna användare på enheten (till exempel vid avbrott i tjänsten).
- För att lagra resultaten som returnerades för proaktiv frågan personliga i väntan på användarens behov, baserat på användarens signaler (till exempel vid förväntade avbrott).

**Server.** Du kan behålla resultaten som är specifika för en enskild användare på ett säkert sätt på en server som du kan styra och visa kvarhållna resultatet:

- Så här aktiverar du att användaren kommer åt Historikrapport över resultat som tidigare returnerats till användaren i din lösning. Resultatet kan inte (i) kvar längre än 21 dagar från det att slutanvändarens första fråga och (ii) visas som svar på en användares nya eller upprepade frågan.
- Anpassade i väntan på användarens behov, baserat på användarens signalerar att lagra resultaten som returnerades för proaktiv frågan. Du kan lagra dessa resultat för mindre (i) 24 timmar från tidpunkten av frågan eller (ii) förrän en användare skickar en annan fråga för uppdaterade resultat.

När behålls kan inte resultat för en viss användare vara commingled med resultat för en annan användare. Som är resultatet av varje användare behålls och levererat separat.

### <a name="general"></a>Allmänt 

För alla sparade resultat:

- Inkludera tydliga, visas informationen om den tid som frågan har skickats.
- Aktuella användare med en knapp eller liknande innebär att att fråga och få uppdatera resultat. 
- Behålla Bing företagsanpassning vid utformning av resultaten.
- Ta bort (och uppdatera med en ny fråga om det behövs) lagrade resultat inom tidsramarna anges.

### <a name="non-display-url-discovery"></a>Icke-Visa URL: en identifiering 

Du kan bara använda sökningssvar i en icke-internet sökinställningar uteslutande av identifiering av URL: er för informationskällor för att svara på en fråga från användaren eller kund. Du kan kopiera dessa URL: er i en rapport eller liknande svar som du anger:

- Endast för den användare eller en kund som svar på frågan.
- Endast om den innehåller betydande ytterligare värdefullt innehåll, relevanta för frågan.

Använd API: er Search föregående avsnitt och visa krav gäller inte för den här icke-visa användningen, förutom följande: 

- Inte cachelagra, kopiera eller spara data eller innehåll från, eller härledd från search-svar än begränsad URL kopiering som beskrivs ovan.
- Kontrollera din användning av data (inklusive URL: er) som tagits emot från API: er för Search inte bryta mot gällande lagar och tredje parts rättigheter.
- Använd inte data (inklusive URL: er) från Sök-API: er som en del av search index eller machine learning eller liknande algoritmbaserade aktivitet. Använd inte dessa data att skapa train, utvärdera eller förbättra tjänsterna som du eller tredje part kan erbjuda.

## <a name="gdpr-compliance"></a>BNPR kompatibilitet  

Du förstår att dig och Microsoft är med avseende på alla personliga data omfattas EU allmänna Data Protection (BNPR) och som har bearbetats i samband med anrop till API: er Search, Bing stavningskontroll kontrollera API eller API för Bing Autosuggest oberoende data domänkontrollanter under BNPR. Du ansvarar oberoende din kompatibiliteten med BNPR.  

