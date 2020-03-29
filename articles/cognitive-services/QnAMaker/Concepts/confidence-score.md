---
title: Konfidenspoäng - QnA Maker
titleSuffix: Azure Cognitive Services
description: En kunskapsbas måste publiceras. När kunskapsbasen har publicerats efterfrågas slutpunkten för körningsprognos med hjälp av generateAnswer API.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 01/27/2020
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: d901a803311805825c22503af6098e805a67e8f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "76843460"
---
# <a name="the-confidence-score-of-an-answer"></a>Förtroendepoängen för ett svar
När en användarfråga matchas mot en kunskapsbas returnerar QnA Maker relevanta svar tillsammans med en konfidenspoäng. Den här poängen anger förtroendet för att svaret är rätt matchning för den givna användarfrågan.

Konfidenspoängen är ett nummer mellan 0 och 100. En poäng på 100 är sannolikt en exakt matchning, medan en poäng på 0 betyder, att inget matchande svar hittades. Ju högre poäng, desto större förtroende för svaret. För en viss fråga kan det finnas flera svar returnerade. I så fall returneras svaren i ordning efter minskad konfidenspoäng.

I exemplet nedan kan du se en QnA-entitet, med 2 frågor.


![Exempel på QnA-par](../media/qnamaker-concepts-confidencescore/ranker-example-qna.png)

För ovanstående exempel- du kan förvänta dig poäng som exempelpoängintervallet nedan- för olika typer av användarfrågor:


![Ranker poängintervall](../media/qnamaker-concepts-confidencescore/ranker-score-range.png)


Följande tabell anger typisk konfiden som är associerad för en viss poäng.

|Poängvärde|Poäng Betydelse|Exempelfråga|
|--|--|--|
|90 - 100|En nästan exakt matchning av användarfrågan och en KB-fråga|"Mina ändringar uppdateras inte i KB efter publicering"|
|> 70|Högt förtroende - vanligtvis ett bra svar som helt svarar på användarens fråga|"Jag publicerade min KB men den är inte uppdaterad"|
|50 - 70|Medium förtroende - vanligtvis ett ganska bra svar som bör svara på huvudsyftet med användarfrågan|"Ska jag spara mina uppdateringar innan jag publicerar min KB?"|
|30 - 50|Lågt förtroende - vanligtvis ett relaterat svar, som delvis svarar på användarens avsikt|" Vad gör spara och träna göra?"|
|< 30|Mycket lågt förtroende - vanligtvis inte svara på användarens fråga, men har några matchande ord eller fraser |"Var kan jag lägga till synonymer till min KB"|
|0|Ingen matchning, så svaret returneras inte.|"Hur mycket kostar tjänsten"|

## <a name="choose-a-score-threshold"></a>Välj ett poängtröskel
Tabellen ovan visar de poäng som förväntas på de flesta KBs. Men eftersom varje KB är olika, och har olika typer av ord, avsikter och mål- vi rekommenderar att du testar och väljer den tröskel som bäst fungerar för dig. Som standard är tröskelvärdet 0, så att alla möjliga svar returneras. Den rekommenderade tröskeln som ska fungera för de flesta KBs, är **50**.

När du väljer din tröskel, tänk på balansen mellan noggrannhet och täckning, och justera din tröskel baserat på dina krav.

- Om **noggrannhet** (eller precision) är viktigare för ditt scenario, sedan öka din tröskel. På så sätt, varje gång du returnerar ett svar, kommer det att bli en mycket mer självsäker fall, och mycket mer sannolikt att vara svaret användarna letar efter. I det här fallet kan du hamna lämnar fler frågor obesvarade. *Till exempel:* om du gör tröskeln **70,** kan du missa några tvetydiga exempel som "vad är spara och träna?".

- Om **täckning** (eller återkallande) är viktigare- och du vill svara på så många frågor som möjligt, även om det bara finns en partiell relation till användarens fråga- sedan sänka tröskeln. Detta innebär att det kan finnas fler fall där svaret inte svarar på användarens faktiska fråga, men ger några andra något relaterade svar. *Till exempel:* om du gör tröskelvärdet **30**kan du ge svar på frågor som "Var kan jag redigera min KB?"

> [!NOTE]
> Nyare versioner av QnA Maker innehåller förbättringar av bedömningslogiken och kan påverka tröskelvärdet. Varje gång du uppdaterar tjänsten, se till att testa och justera tröskeln om det behövs. Du kan kontrollera din QnA Service version [här](https://www.qnamaker.ai/UserSettings), och se hur du får de senaste uppdateringarna [här](../How-To/set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates).

## <a name="set-threshold"></a>Ange tröskelvärde

Ange tröskelpoängen som egenskap för [generateanswer API JSON.](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) Det innebär att du ställer in den för varje anrop till GenerateAnswer.

Från bot-ramverket anger du poängen som en del av alternativobjektet med [C#](../how-to/metadata-generateanswer-usage.md?#use-qna-maker-with-a-bot-in-c) eller [Node.js](../how-to/metadata-generateanswer-usage.md?#use-qna-maker-with-a-bot-in-nodejs).

## <a name="improve-confidence-scores"></a>Förbättra konfidenspoängen
Om du vill förbättra konfidenspoängen för ett visst svar på en användarfråga kan du lägga till användarfrågan i kunskapsbasen som en alternativ fråga om det svaret. Du kan också använda skiftlägesokänsliga [ordändringar](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/alterations/replace) för att lägga till synonymer i nyckelord i KB.


## <a name="similar-confidence-scores"></a>Liknande konfidenspoäng
När flera svar har en liknande förtroendepoäng är det troligt att frågan var för allmän och därför matchas med samma sannolikhet med flera svar. Försök att strukturera dina QnAs bättre så att varje QnA-entitet har en distinkt avsikt.


## <a name="confidence-score-differences-between-test-and-production"></a>Konfidenspoängskillnader mellan test och produktion
Ett svars förtroendepoäng kan ändras försumbart mellan testet och den publicerade versionen av kunskapsbasen även om innehållet är detsamma. Detta beror på att innehållet i testet och den publicerade kunskapsbasen finns i olika Azure Cognitive Search-index.

Testindexet innehåller alla QnA-par i dina kunskapsbaser. När du frågar efter testindexet gäller frågan för hela indexet och sedan begränsas resultaten till partitionen för den specifika kunskapsbasen. Om testfrågeresultaten påverkar din förmåga att validera kunskapsbasen negativt kan du:
* organisera din kunskapsbas med något av följande:
    * 1 resurs som är begränsad till 1 KB: begränsa din enda QnA-resurs (och det resulterande Azure Cognitive Search-testindexet) till en enda kunskapsbas.
    * 2 resurser - 1 för test, 1 för produktion: har två QnA Maker resurser, med hjälp av en för testning (med egna test-och produktionsindex) och en för produkt (även med sina egna test-och produktionsindex)
* och använd alltid samma parametrar, till exempel **[överst](../how-to/improve-knowledge-base.md#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers)** när du frågar både din test- och produktionskunskapsbas

När du publicerar en kunskapsbas flyttas frågan och svaret i kunskapsbasen från testindex till ett produktionsindex i Azure-sökning. Se hur [publish](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) publiceringsåtgärden fungerar.

Om du har en kunskapsbas i olika regioner använder varje region sitt eget Azure Cognitive Search-index. Eftersom olika index används kommer poängen inte att vara exakt desamma.


## <a name="no-match-found"></a>Ingen matchning hittades
När ingen bra matchning hittas av ranker, förtroende poäng på 0,0 eller "Ingen" returneras och standardsvaret är "Ingen bra matchning finns i KB". Du kan åsidosätta det här [standardsvaret](../How-To/metadata-generateanswer-usage.md) i bot- eller programkoden som anropar slutpunkten. Alternativt kan du också ange åsidosättningssvaret i Azure och detta ändrar standardvärdet för alla kunskapsbaser som distribueras i en viss QnA Maker-tjänst.

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Metodtips](./best-practices.md)

