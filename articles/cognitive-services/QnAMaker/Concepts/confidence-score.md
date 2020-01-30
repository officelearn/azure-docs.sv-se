---
title: Förtroende poäng – QnA Maker
titleSuffix: Azure Cognitive Services
description: 'En kunskaps bas måste publiceras. När den har publicerats frågas kunskaps basen vid körnings förutsägelse slut punkten med generateAnswer-API: et.'
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
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2020
ms.locfileid: "76843460"
---
# <a name="the-confidence-score-of-an-answer"></a>Förtroende poängen för ett svar
När en användarfråga matchas mot en kunskapsbas, returnerar QnA Maker relevanta svar, tillsammans med ett förtroenderesultat. Det här resultatet indikerar var säker på att svaret är rätt matchning för den angivna användarfrågan.

Förtroendepoäng är ett tal mellan 0 och 100. Ett resultat på 100 är troligen en exakt matchning, samtidigt som ett resultat på 0 innebär att inget matchande svar hittades. Ju högre alternativet score - ju större tillförlitlighet i svaret. För en viss fråga kan det finnas flera svar returneras. I så fall returneras svar i fallande förtroendepoäng.

I exemplet nedan ser du frågor och svar om enheter, med 2 frågor.


![Exempel på frågor och svar om par](../media/qnamaker-concepts-confidencescore/ranker-example-qna.png)

Du kan förvänta dig poäng som exempel poäng intervallet nedan – för olika typer av användarfrågor för ovanstående exempel:


![Rankningen poäng intervall](../media/qnamaker-concepts-confidencescore/ranker-score-range.png)


Följande tabell visar vanliga förtroende som är associerade för ett visst poängintervall.

|Poäng värde|Poäng betydelse|Exempelfråga|
|--|--|--|
|90 - 100|En nästan exakt matchning av användarfråga och en KB-fråga|"Mina ändringar uppdateras inte i KB efter publicering"|
|> 70|Hög exakthet - vanligtvis ett bra svar som helt besvarar användarens fråga|”Jag har publicerat min Kunskapsbas men uppdateras inte”.|
|50 - 70|Medium förtroende - vanligtvis ett ganska bra svar som ska svara på det huvudsakliga syftet med användarfrågan|”Ska jag spara min uppdateringar innan jag publicera min Kunskapsbas”?|
|30 - 50|Låg förtroende - vanligtvis ett relaterade svar, som delvis besvarar användarens avsikt|”Vad gör spara och träna”?|
|< 30|Mycket låg förtroende - vanligtvis svarar inte användarens fråga, men har vissa ord eller fraser som matchande |”Var kan jag lägga till synonymer min Kunskapsbas”|
|0|Ingen matchning, så att svaret inte returneras.|”Hur mycket tjänsten kostar”|

## <a name="choose-a-score-threshold"></a>Välj ett poäng tröskelvärde
Tabellen ovan visar resultat som förväntas på de flesta KB-artiklar. Men eftersom varje KB skiljer sig åt, och har olika typer av ord, avsikter och mål, rekommenderar vi att du testar och väljer det tröskelvärde som passar bäst för dig. Som standard är tröskelvärdet inställt på 0, så att alla möjliga svar returneras. Den rekommenderade tröskeln som ska fungera för de flesta KB är **50**.

När du väljer tröskeln för ditt, Kom ihåg balans mellan precision och täckning och justera tröskeln för ditt baserat på dina krav.

- Om **Precision** (eller precision) är viktigare för ditt scenario och öka din tröskelvärdet. På så sätt kan varje gång du kommer tillbaka ett svar är en mycket mer CONFIDENT användningsfall och mycket mer troligt svar användare söker efter. I det här fallet kan du få lämnar fler frågor obehandlade. *Till exempel:* om du gör tröskelvärdet **70**, du kan gå miste om vissa tvetydig exempel gilla ”vad är spara och träna”?.

- Om **täckning** (eller återkallande) är fler viktigt – och du vill att besvara så många frågor som möjligt, även om det finns bara en partiell relation till användarens fråga - sedan SÄNKA tröskelvärdet. Det innebär att det kan vara mer fall där svaret svarar inte användarens faktiska frågan, men ger andra relaterade något svar. *Exempel:* om du gör tröskelvärdet **30**kan du ge svar på frågor som "var kan jag redigera mitt KB?".

> [!NOTE]
> Nyare versioner av QnA Maker är förbättringar av bedömnings logik och kan påverka din tröskelvärdet. När du uppdaterar tjänsten, se till att testa och justera tröskelvärdet om det behövs. Du kan kontrollera QnA Service-version [här](https://www.qnamaker.ai/UserSettings), och se hur du kan få de senaste uppdateringarna [här](../How-To/set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates).

## <a name="set-threshold"></a>Ange tröskel

Ange tröskelvärdet som en egenskap för [GENERATEANSWER API-JSON-texten](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration). Det innebär att du ställer in den för varje anrop till GenerateAnswer.

I bot-ramverket ställer du in poängen som en del av alternativ- [C#](../how-to/metadata-generateanswer-usage.md?#use-qna-maker-with-a-bot-in-c) objektet med eller [Node. js](../how-to/metadata-generateanswer-usage.md?#use-qna-maker-with-a-bot-in-nodejs).

## <a name="improve-confidence-scores"></a>Förbättra förtroende-poäng
För att förbättra förtroendepoäng för ett visst svar till en användarfråga, du kan lägga till användarfrågan kunskapsbasen som en annan fråga på det svaret. Du kan också använda Skift läges känsliga [ord ändringar](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/alterations/replace) för att lägga till synonymer till nyckelord i din KB.


## <a name="similar-confidence-scores"></a>Liknande förtroende-poäng
När flera svar har en liknande förtroendepoäng, är det troligt att frågan var för allmän och därför matchade med samma sannolikhet med flera svar. Försök att strukturera dina kunskapsbaser bättre så att varje QnA-enhet har en distinkt avsikt.


## <a name="confidence-score-differences-between-test-and-production"></a>Förtroende poäng skillnader mellan test och produktion
Förtroendepoäng av ett svar kan ändras negligibly mellan test och publicerade versionen av kunskapsbasen även om innehållet är samma. Detta beror på att innehållet i testet och den publicerade kunskaps basen finns i olika Azure Kognitiv sökning-index.

Test indexet innehåller alla QnA-par i kunskaps baserna. När du frågar test indexet gäller frågan hela indexet och resultaten begränsas till partitionen för den aktuella kunskaps basen. Om resultatet av test frågan påverkar din möjlighet att verifiera kunskaps basen kan du:
* organisera kunskaps basen med något av följande:
    * 1 resurs begränsad till 1 KB: begränsa din enda QnA-resurs (och det resulterande Azure Kognitiv sökning test indexet) till en enda kunskaps bas.
    * 2 resurser – 1 för test, 1 för produktion: har två QnA Maker resurser, med hjälp av en för testning (med sina egna test-och produktions index) och en för produkt (som också har sina egna test-och produktions index)
* och Använd alltid samma parametrar, till exempel **[överst](../how-to/improve-knowledge-base.md#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers)** när du frågar efter både din test-och produktions kunskaps bas

När du publicerar en kunskaps bas, flyttas frågan och svars innehållet i kunskaps basen från test indexet till ett produktions index i Azure Search. Se hur [publicerings](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) åtgärden fungerar.

Om du har en kunskaps bas i olika regioner använder varje region sitt eget Azure Kognitiv sökning-index. Eftersom olika index används är poängen inte identiska.


## <a name="no-match-found"></a>Ingen matchning hittades
När ingen bra matchning hittas av rankningen, förtroendepoäng 0,0 eller ”None” returneras och Standardsvaret är ”bra att hitta någon matchning i KB”. Du kan åsidosätta [standardsvaret](../How-To/metadata-generateanswer-usage.md) i robot-eller program koden som anropar slut punkten. Alternativt kan du kan också ange åsidosättning svaret i Azure och detta ändrar standardvärdet för alla kunskapsbaser som distribueras i en viss QnA Maker-tjänsten.

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Bästa praxis](./best-practices.md)

