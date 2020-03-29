---
title: Användnings- och visningskrav för API:erna för Bing-sökning
titleSuffix: Azure Cognitive Services
description: Kraven för att visa sökresultat från API:erna för Bing-sökning i dina program.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: aahi
ms.openlocfilehash: 5575668f164b97142e7c4b2ddb2608c3173426a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "60499893"
---
# <a name="bing-search-api-use-and-display-requirements"></a>Användnings- och visningskrav för API:er för Bing-sökresultat

Dessa användnings- och visningskrav gäller för all implementering av innehållet och tillhörande information från följande API:er för Bing-sökning, inklusive relationer, metadata och andra signaler.

- Anpassad Bing-sökning
- Entitetssökning i Bing
- Bildsökning i Bing
- Nyhetssökning i Bing
- Videosökning i Bing
- Visuell sökning i Bing
- Webbsökning i Bing
- Stavningskontroll i Bing
- Automatiska förslag i Bing

## <a name="definitions"></a>Definitioner


|Period  |Beskrivning  |
|---------|---------|
|Svar     | En kategori av resultat som returneras i ett svar. Ett svar från API:et för webbsökning på Bing kan till exempel innehålla svar i kategorierna webbsidans resultat, bild, video, visuella och nyheter. |
|Svar     | Alla svar och tillhörande data som tas emot som svar på ett enda anrop till ett sök-API. |
|Resultat    | Ett informationsobjekt i ett svar. Den uppsättning data som är kopplad till en enda nyhetsartikel är till exempel ett resultat av ett nyhetssvar. |
|Sök API:er    | tillsammans API:erna för anpassad sökning i Bing, entitetssökning, bildsökning, nyhetssökning, videosökning, visuell sökning, lokal företagssökning och webbsökning. |

## <a name="bing-spell-check-and-bing-autosuggest-api-restrictions"></a>Api-begränsningar för Bing-stavningskontroll och Bing-kortspel

Gör inte:

- Kopiera, lagra eller cachelagra data som du får från API:erna för stavningskontroll eller Bing-kort.
- Använd data som du får från Bing Spell Check eller Bing Autosuggest API:er som en del av maskininlärning eller liknande algoritmisk aktivitet. Använd inte dessa data för att träna, utvärdera eller förbättra nya eller befintliga tjänster som du eller tredje part kan erbjuda.

## <a name="bing-search-apis"></a>API:er för Bing-sökresultat

> [!NOTE]
> Kraven i det här avsnittet gäller endast sök-API:erna, som inte innehåller Bing Spell Check eller Bing Autosuggest. 

### <a name="internet-search-experience-requirements"></a>Krav på sökupplevelse på Internet

Alla data som returneras i svar får endast användas i sökupplevelser på Internet. En sökupplevelse på Internet innebär att innehållet visas: 

- Är relevant och lyhörd för slutanvändarens direkta fråga, eller annan indikation på deras sökintresse och avsikt (till exempel en användarindikerat sökfråga). 

- Hjälper användarna att hitta och navigera till svarets datakällor. Till exempel att tillhandahålla klickbara länkar från hyperlänkar i svaret.

- Innehåller flera resultat som användaren kan välja mellan. 

- Finns i en placering som gör det möjligt för användare att söka.

- Innehåller en synlig indikation på att innehållet är ett sökresultat på Internet. Till exempel ett uttalande om att innehållet är "från webben".

- Innehåller andra lämpliga åtgärder för att säkerställa att dina Bing Search API-data inte bryter mot gällande lagar eller tredje parts rättigheter. Kontakta dina juridiska rådgivare för att avgöra vilka åtgärder som kan vara lämpliga.

Det enda undantaget från dessa krav på sökupplevelse på Internet är för identifiering av webbadresser, som beskrivs senare i den här artikeln. 

### <a name="restrictions"></a>Begränsningar

Gör inte:

- Kopiera, lagra eller cachelagra data från svar (utom lagring i den utsträckning som tillåts [av kontinuitet i tjänsten](#continuity-of-service). 

- Använd data som tas emot från sök-API:erna som en del av maskininlärning eller liknande algoritmisk aktivitet. Använd inte dessa data för att träna, utvärdera eller förbättra nya eller befintliga tjänster som du eller tredje part kan erbjuda.

- Ändra innehållet i resultaten (annat än att formatera om dem på ett sätt som inte bryter mot något annat krav), om inte krävs enligt lag eller godkänts av Microsoft. 

- Utelämna attributionsinformation och webbadresser som är associerade med resultatinnehåll.

- Ändra ordning, inklusive genom utelämnande, resultaten visas i ett svar när en order eller rangordning tillhandahålls, om inte krävs enligt lag eller godkänts av Microsoft. 

    > [!NOTE]
    > Det här kravet gäller inte för ombeställning som implementeras via portalen för API:et för anpassad sökning i Bing.

- Visa annat innehåll inom någon del av ett svar på ett sätt som skulle få en användare att tro att det andra innehållet är en del av svaret. 

- Visa reklam som inte tillhandahålls av Microsoft på någon sida som visar någon del av ett svar. 

- Visa all reklam på sidor med svar:
    - Från API:erna Bing Image, News Search, Video Search eller Visual Search
    - Som filtreras eller begränsas främst (eller enbart) till bild, nyheter och/eller video eller visuella sökresultat.

### <a name="notices-and-branding"></a>Meddelanden och varumärke 
Göra:

- På ett framträdande sätt kan du inkludera en funktionell hyperlänk till [Microsofts sekretesspolicy](https://go.microsoft.com/fwlink/?LinkId=521839), nära varje punkt i användarupplevelsen (UX) som ger en användare möjlighet att mata in en sökfråga. Märk sekretesspolicyn för **Hyperlänken från Microsoft**.

- Visa Bing-varumärke, i enlighet med riktlinjerna för [användning av Bing-varumärke,](https://go.microsoft.com/fwlink/?linkid=833278)nära varje punkt i användarupplevelsen som ger en användare möjlighet att mata in en sökfråga. Ett sådant varumärke måste tydligt ange för användaren att Microsoft driver sökupplevelsen på Internet.

- Du kan tillskriva varje svar (eller del av ett svar) som visas från API:erna för webbsökning, bildsökning, nyhetssökning, videosökning och visuell sökning till Microsoft, såvida inte Microsoft anger något annat skriftligt för din användning. Detta beskrivs i [Bing Trademark Usage Guidelines](https://go.microsoft.com/fwlink/?linkid=833278). 

Gör inte:

- Attributsvar (eller delar av svar) som visas från API:et för anpassad sökning i Bing till Microsoft, såvida inte Microsoft anger något annat skriftligt för just din användning.

### <a name="transferring-responses"></a>Överföra svar

Om du gör det möjligt för en användare att överföra ett svar från ett sök-API till en annan användare, till exempel via en meddelandeapp eller inlägg i sociala medier, gäller följande: 

- Överförda svar måste:
  - Består av innehåll som inte ändras från innehållet i svaren som visas för den överförande användaren. Det är tillåtet att formatera ändringar.
  - Innehåller inga data i metadataformuläret.
  - För svar från API:erna för Bing-webben, bild, nyheter, video och visuella objekt, visas språk som anger att svaret erhölls via en sökupplevelse på Internet som drivs av Bing. Du kan till exempel visa språk som "Drivs av Bing" eller "Läs mer om den här bilden på Bing" eller använda Bing-logotypen.
  - För svar från API:et för anpassad sökning på Bing visas ett visningsspråk som anger att svaret erhölls via en sökupplevelse på Internet. Du kan till exempel visa språk som "Läs mer om det här sökresultatet".
  - Visa den fullständiga frågan som används för att generera svaret på ett framträdande sätt.
  - Inkludera en framträdande länk eller liknande attribution till den underliggande källan till svaret, antingen direkt eller via sökmotorn (bing.com, m.bing.com eller din anpassade söktjänst, beroende på vad som är tillämpligt).
- Du får inte automatisera överföringen av svar. En överföring måste initieras av en användaråtgärd som tydligt styrker en avsikt att överföra ett svar.
- Du kan bara aktivera en användare för att överföra svar som visades som svar på den överförande användarens fråga.

### <a name="continuity-of-service"></a>Kontinuitet i tjänsten 

Kopiera, lagra eller cachelagra inte data från sök-API-svar. För att möjliggöra kontinuitet i tjänstens åtkomst och dataåtergivning kan du behålla resultaten endast under följande villkor:

#### <a name="device"></a>Enhet

Du kan göra det möjligt för en användare att behålla resultaten på en enhet under mindre belopp av (i) 24 timmar från tidpunkten för frågan, eller (ii) tills en användare skickar en annan fråga för uppdaterade resultat, förutsatt att balanserade resultat endast får användas:

- Så här gör du det möjligt för användaren att komma åt resultat som tidigare returnerats till den användaren på den enheten (till exempel vid avbrott i tjänsten).
- Om du vill lagra resultat som returneras för din proaktiva fråga som är anpassad i väntan på användarens behov, baserat på användarens signaler (till exempel vid förväntade avbrott i tjänsten).

#### <a name="server"></a>Server

Du kan behålla resultat som är specifika för en enskild användare på ett säkert sätt på en server som du styr och endast visa de balanserade resultaten:

- Så här gör du det möjligt för användaren att komma åt en historisk rapport över resultat som tidigare returnerats till den användaren i din lösning. Resultaten får inte (i) behållas i mer än 21 dagar från tidpunkten för slutanvändarens första fråga och (ii) visas som svar på en användares nya eller upprepade fråga.
- Om du vill lagra resultat som returneras för din proaktiva fråga som är anpassad i väntan på användarens behov, baserat på användarens signaler. Du kan lagra dessa resultat för mindre av (i) 24 timmar från tidpunkten för frågan, eller (ii) tills en användare skickar en annan fråga för uppdaterade resultat.

När de behålls kan resultaten för en viss användare inte blandas med resultat för en annan användare. Det innebär att resultaten för varje användare måste behållas och levereras separat.

### <a name="general"></a>Allmänt 

För alla presentation av balanserade resultat:

- Inkludera ett tydligt, synligt meddelande om när frågan skickades.
- Presentera användaren med en knapp eller liknande sätt att fråga om och få uppdaterade resultat. 
- Behåll varumärket Bing i presentationen av resultaten.
- Ta bort (och uppdatera med en ny fråga om det behövs) de lagrade resultaten inom de angivna tidsramarna.

### <a name="non-display-url-discovery"></a>Identifiering av URL som inte visas 

Du får endast använda söksvar i en sökupplevelse som inte är på Internet i det enda syftet att identifiera webbadresser till informationskällor som svarar på en fråga från din användare eller kund. Du kan kopiera sådana webbadresser i en rapport eller liknande svar som du anger:

- Endast till den användaren eller kunden, som svar på den frågan.
- Endast om det innehåller betydande ytterligare värdefullt innehåll, relevant för frågan.

De föregående avsnitten i sök-API:er använder och visar kraven gäller inte för denna användning som inte visas, förutom följande: 

- Cacheminne, kopiera eller lagra inte data eller innehåll från eller härledas från söksvaret, förutom den begränsade URL-kopiering som beskrivits tidigare.
- Se till att din användning av data (inklusive webbadresserna) som tas emot från sök-API:erna inte bryter mot gällande lagar eller tredje parts rättigheter.
- Använd inte data (inklusive webbadresser) som tas emot från sök-API:erna som en del av något sökindex eller maskininlärning eller liknande algoritmisk aktivitet. Använd inte dessa data för att skapa tåg, utvärdera eller förbättra tjänster som du eller tredje part kan erbjuda.

## <a name="gdpr-compliance"></a>GDPR-efterlevnad  

När det gäller alla personuppgiftsanpassade som omfattas av EU:s allmänna dataskyddsförordning (GDPR) och som behandlas i samband med samtal till API:erna för sök-API: er, Bing Spell Check API eller Bing Autosuggest API, förstår du att du och Microsoft är oberoende registeransvariga enligt GDPR. Du är oberoende ansvarig för att du följer GDPR.  

