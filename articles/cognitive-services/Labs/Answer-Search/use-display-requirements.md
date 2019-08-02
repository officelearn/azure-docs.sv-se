---
title: Använda och Visa krav – sökning efter projekt svar
titlesuffix: Azure Cognitive Services
description: Använd och Visa krav för slut punkten för projekt svars sökning.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: answer-search
ms.topic: conceptual
ms.date: 04/13/2018
ms.author: rosh
ROBOTS: NOINDEX
ms.openlocfilehash: 2b42d61fd887f166a08b78510d5eaacb8a7cdcb8
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706712"
---
# <a name="project-answer-search-use-and-display-requirements"></a>Användning och visnings krav för projekt svars sökning

Användnings-och visnings krav gäller för all implementering av innehållet och tillhör ande information, till exempel relationer, metadata och andra signaler, tillgängliga via anrop till Bing Knowledge search, Anpassad sökning i Bing, Entitetssökning Bildsökning, Nyhetssökning, Videosökning, Visuell sökning, Webbsökning, Stavningskontroll och automatiska förslag för API: er. Implementerings information som rör dessa krav finns i dokumentationen för särskilda funktioner och resultat.

## <a name="1-bing-spell-check-and-bing-autosuggest-api"></a>1. Stavningskontroll i Bing och API för automatiska förslag i Bing.

Gör inte:

- Kopiera, lagra eller cachelagra data som du tar emot från Stavningskontroll i Bing eller Automatiska förslag i Bing API: er
- Använd data som du tar emot från Stavningskontroll i Bing-eller Automatiska förslag i Bing-API: er som en del av en maskin inlärning eller liknande algoritms-aktivitet för att träna, utvärdera eller förbättra nya eller befintliga tjänster som du eller tredje part kan erbjuda.

## <a name="2-definitions"></a>2. Definitioner

- "svar" syftar på en resultat kategori som returneras i ett svar. Till exempel kan ett svar från API för webbsökning i Bing innehålla svar i kategorier av webb sidas resultat, bild, video och nyheter.
- "Response" syftar på alla svar och tillhör ande data som tas emot som svar på ett enskilt anrop till ett API för sökning.
- "result" syftar på en informations post i ett svar. Data uppsättningen som är kopplad till en enda nyhets artikel är till exempel ett resultat av ett nyhets svar.
- "Sök-API: er" innebär kollektivt, Anpassad sökning i Bing, Entitetssökning, Bildsökning, Nyhetssökning, Videosökning, Visuell sökning och Webbsökning-API: er. 


## <a name="3-search-apis"></a>3. Sök API:er

Kraven i det här avsnittet gäller för Sök-API: er.

**EN. Internets öknings upplevelse.** Alla data som returneras i svar får bara användas i Internets öknings upplevelser. En Internets ökning innebär att det innehåll som visas, i tillämpliga fall: 
- är relevant och svarar på slutanvändarens direkta fråga eller annan indikation på användarens sökintresse och avsikt (t. ex. en användar angiven Sök fråga). 
- hjälper användare att hitta och navigera till data källor (till exempel att de angivna URL: erna implementeras som hyperlänkar, så att innehållet eller behörigheten är en klicknings bara länk som visas synligt med data). eller, om API för entitetsökning i Bing, länkas synligt till bing.com-URL: en som tillhandahölls i svaret som gör att användaren kan navigera till Sök resultaten för den relevanta frågan på bing.com;
- innehåller flera resultat för slutanvändaren att välja bland (till exempel, flera resultat från nyhets svaret visas, eller alla resultat om färre än flera returneras). 
- är begränsad till en lämplig mängd för att kunna hantera Sök syftet (till exempel miniatyr bildernas storlek i proportion till användarens visning). 
- inkluderar synlig information till slutanvändaren om att innehållet är Sök Resultat från Internet (till exempel en instruktion som innehållet är "från webben"). särskilt
- innehåller alla andra kombinationer av åtgärder som är lämpliga för att säkerställa att din användning av data som tas emot från Sök-API: erna inte strider mot gällande lagar eller tredje parts rättigheter (till exempel om du förlitar sig på en Creative Commons-licens och följer tillämplig licens villkor). Kontakta dina juridiska rådgivare för att fastställa vilka åtgärder som kan vara lämpliga.
Det enda undantaget till Internets öknings krav är för URL-identifiering enligt beskrivningen i avsnittet 3E (URL-identifiering för icke-Visa) nedan. 

**B. Rättigheter.** Gör inte:

- Kopiera, lagra eller cachelagra data från svar (förutom kvarhållning i den utsträckning som tillåts av "kontinuitet för tjänsten" nedan); 
- Använd data som hämtats från Sök-API: er som en del av maskin inlärning eller liknande algoritmisk aktivitet för att träna, utvärdera eller förbättra nya eller befintliga tjänster som du eller tredje part kan erbjuda.
- ändra resultat innehåll (annat än att omformatera dem på ett sätt som inte strider mot något annat krav), såvida det inte krävs enligt lag eller överenskommits av Microsoft. 
- utelämna behörighet och URL: er kopplade till resultat innehållet.
- ändra ordning, inklusive utelämnande, resultat som visas i ett svar när en order eller rangordning tillhandahålls (för API för anpassad Bing-sökning gäller den här regeln inte för att sortera om implementerad via customsearch.ai-portalen), såvida det inte krävs enligt lag eller överenskommits av Microsoft ;
- Visa annat innehåll inom någon del av ett svar på ett sätt som skulle leda till att en användare kan tro att det andra innehållet är en del av svaret. 
- Visa annonsering som inte tillhandahålls av Microsoft på någon sida som visar någon del av ett svar. – Visa en annonsering med svar (i) från Bing-bilden, nyheter eller Videosökning-API: er; eller (II) som filtreras eller begränsas främst (eller endast) till bilder, nyheter och/eller video resultat.

**C. Meddelanden och varumärke.** 

- Ta en funktionell hyperlänk till Microsofts sekretess policy, som är tillgänglig i https://go.microsoft.com/fwlink/?LinkId=521839 nära varje punkt i användar upplevelsen (UX) som ger användaren möjlighet att ange en Sök fråga. Ge hyperlänken "Microsofts sekretess policy".
- Väl synlig Bing-anpassning, som är konsekvent med rikt linjerna https://go.microsoft.com/fwlink/?linkid=833278 i, nära varje punkt i UX-gränssnittet som ger användaren möjlighet att mata in en Sök fråga.  Detta varumärke måste tydligt ange användaren att Microsoft ska starta Sök upplevelsen för Internet.
- Du kan använda varje svar (eller en del av ett svar) som visas från webb-API: erna webb, bild, nyheter och video till Microsoft https://go.microsoft.com/fwlink/?linkid=833278 enligt beskrivningen i, om inte Microsoft anger något annat i form av användning. 
- Använd inte svar (eller delar av svar) som visas från API för anpassad Bing-sökning till Microsoft, om inte Microsoft anger något annat i skrivning till din specifika användning.


**STYR. Överför svar.** Om du gör det möjligt för en användare att överföra ett svar från ett Sök-API till en annan användare, t. ex. via ett meddelande program eller en bokning av sociala medier, gäller följande: 
- Överförda svar måste:
  - Bestå av innehåll som inte har ändrats från innehållet i svaren som visas för den överförda användaren (formateringsändringar är tillåtna).
  - Ta inte med några data i metadata form;
  - För svar från API: erna för webb-, bild-, diskussions-och video-API: erna visar det språk som visar att svaret erhölls via en Internets öknings upplevelse som drivs av Bing (till exempel "drivs av Bing", "Lär dig mer om den här bilden på Bing" eller med Bing-logotypen).
  - För svar från API för anpassad Bing-sökning visas språket som anger att svaret har erhållits via en Internets ökning (till exempel "Läs mer om det här Sök resultatet").
  - Visa hela frågan som används för att generera svaret. särskilt
  - Ta med en framträdande länk eller liknande behörighet till den underliggande källan för svaret, antingen direkt eller via sökmotorn (bing.com, m.bing.com eller din anpassade Sök tjänst, beroende på vad som är tillämpligt).
- Du får inte automatisera överföringen av svar. En överföring måste initieras av en användar åtgärd klart evidencing en avsikt att överföra ett svar.
- Du kan bara aktivera en användare för att överföra svar som visas som svar på den överförda användarens fråga.

**Ö. Kontinuitet för tjänsten.** Kopiera, lagra eller cachelagra inga data från Sök-API-svar. Men om du vill aktivera kontinuitet för tjänst åtkomst och data åter givning kan du behålla resultaten enbart under följande omständigheter:

**Anordningar.** Du kan göra det möjligt för en slutanvändare att behålla resultat på en enhet under den lägsta av (i) 24 timmar från tidpunkten för frågan eller (II) tills en slutanvändare skickar en annan fråga om uppdaterade resultat, förutsatt att kvarhållna resultat endast får användas:

- för att ge slutanvändaren åtkomst till resultat som tidigare returnerats till slutanvändaren på enheten (till exempel om tjänsten avbryts); eller
- för att lagra resultat som returneras för den proaktiva frågan personligt anpassad i förväntad slutanvändarens behov baserat på användarens signaler (till exempel i händelse av förväntat tjänst avbrott).

**Servernamn.** Du kan behålla resultat som är speciella för en enskild slutanvändare på ett säkert sätt på en server som du styr och bara visa de kvarhållna resultaten:

- för att slutanvändaren ska kunna komma åt en historisk rapport med resultat som tidigare returnerats till användaren i din lösning, förutsatt att resultaten inte kan (i) bevaras i mer än 21 dagar från tidpunkten för slutanvändarens första fråga och (II) som visas som svar på en slut u den nya eller upprepade frågan. eller
- för att lagra resultat som returneras för den proaktiva frågan personligt anpassad i förväntad slutanvändarens behov baserat på användarens signaler under den lägsta av (i) 24 timmarna från tidpunkten för frågan eller (II) tills en slutanvändare skickar en annan fråga om uppdaterade resultat.

När det behålls kan resultat för en särskild användare inte commingled med resultat för en annan användare, d.v.s. resultatet av varje användare måste behållas och levereras separat.

**Redovisningsjournalmallar.** För all presentation av kvarhållna resultat:

- ta med ett tydligt, synligt meddelande om när frågan skickades,
- presentera användaren en knapp eller liknande metoder för att skicka frågan igen och få uppdaterade resultat. 
- Behåll Bing-anpassningen i presentationen av resultaten och
- ta bort (och uppdatera med en ny fråga om det behövs) de lagrade resultaten inom de angivna tids ramarna.

**B. URL-identifiering som inte visas.** Du kan bara använda Sök svar i en icke-Internet-sökmiljö för att identifiera URL: er för informations källor som svarar på en fråga från din användare eller kund. Du kan kopiera dessa URL: er i en rapport eller liknande svar du bara anger (i) till den användaren eller kunden, som svar på frågan och (II) som innehåller betydande ytterligare värdefullt innehåll som är relevant för frågan. Kraven i avsnitt 3A till 3E i dessa användnings-och visnings krav gäller inte för denna användning, förutom: 

- Du får inte cachelagra, kopiera eller lagra data eller innehåll från eller härledas från Sök svaret, förutom den begränsade URL: en som beskrivs ovan.
- Se till att din användning av data (inklusive URL: er) som tas emot från Sök-API: erna inte strider mot gällande lagar eller tredje parts rättigheter. särskilt
- Du får inte använda data (inklusive URL: er) som tagits emot från Sök-API: erna som en del av ett sökindex eller Machine Learning eller en liknande algoritms-aktivitet för att skapa tåg, utvärdera eller förbättra tjänster som du eller tredje part kan erbjuda.

## <a name="next-steps"></a>Nästa steg
[Översikt över svars sökning](overview.md)
