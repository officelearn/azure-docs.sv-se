---
title: Förtroende poäng – QnA Maker
titleSuffix: Azure Cognitive Services
description: Förtroende poängen indikerar att svaret är den rätta matchningen för den aktuella användar frågan.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 08/30/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 4e6d86cb3fa304c8e85e7d0ff4a2810be1dc75af
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2019
ms.locfileid: "73794952"
---
# <a name="confidence-score-of-a-qna-maker-knowledge-base"></a>Tillförlitlighets poängen för en QnA Maker kunskaps bas
När en användar fråga matchas mot en kunskaps bas, returnerar QnA Maker relevanta svar, tillsammans med en förtroende poäng. Den här poängen indikerar att svaret är den rätta matchningen för den aktuella användar frågan. 

Förtroende poängen är ett tal mellan 0 och 100. Poängen på 100 är förmodligen en exakt matchning, medan poängen på 0 innebär att inget matchande svar hittades. Ju högre poäng, desto större besvars noggrannhet. Det kan finnas flera returnerade svar för en specifik fråga. I så fall returneras Svaren när du minskar förtroende poängen.

I exemplet nedan kan du se en QnA entitet med två frågor. 


![Exempel på QnA-par](../media/qnamaker-concepts-confidencescore/ranker-example-qna.png)

I exemplet ovan – du kan vänta med poängen som exempel Poäng intervallet nedan – för olika typer av användar frågor:


![Ranknings Poäng intervall](../media/qnamaker-concepts-confidencescore/ranker-score-range.png)


I följande tabell visas en typisk exakthet som är kopplad till en viss poäng.

|Poäng värde|Poäng betydelse|Exempel fråga|
|--|--|--|
|90 – 100|En nära exakt matchning av användar frågan och en KB-fråga|"Mina ändringar uppdateras inte i KB efter publicering"|
|> 70|Hög exakthet – vanligt vis ett korrekt svar som helt besvarar användarens fråga|"Jag har publicerat mitt KB men det har inte uppdaterats"|
|50 – 70|Medels Tor exakthet – vanligt vis ett ganska utmärkt svar som ska svara på det huvudsakliga syftet med användar frågan|"Ska jag spara mina uppdateringar innan jag publicerar mitt KB?"|
|30 - 50|Låg exakthet – vanligt vis ett relaterat svar som delvis besvarar användarens avsikt|"Vad händer med att spara och träna?"|
|< 30|Mycket låg exakthet – vanligt vis besvarar inte användarens fråga, men har några matchande ord eller fraser |"Var kan jag lägga till synonymer i min KB"|
|0|Ingen matchning, så svaret returneras inte.|"Hur mycket kostar tjänsten"|

## <a name="choose-a-score-threshold"></a>Välj ett Poäng tröskelvärde
Tabellen ovan visar de resultat som förväntas för de flesta KB. Men eftersom varje KB skiljer sig åt, och har olika typer av ord, avsikter och mål, rekommenderar vi att du testar och väljer det tröskelvärde som passar bäst för dig. Som standard är tröskelvärdet inställt på 0, så att alla möjliga svar returneras. Den rekommenderade tröskeln som ska fungera för de flesta KB är **50**.

När du väljer ditt tröskelvärde bör du tänka på balansen mellan precisionen och täckningen och justera tröskelvärdet utifrån dina behov.

- Om **precisionen** (eller precisionen) är viktigare för ditt scenario, ökar du tröskelvärdet. På så sätt kan du varje gång du returnerar ett svar vara ett mycket mer säkert ärende, och det är mycket mer sannolikt att svars användarna söker. I så fall kan du lämna fler frågor utan svar. *Exempel:* om du gör tröskelvärdet **70**kan du missa några tvetydiga exempel gillar "Vad är Spara och träna?".

- Om **täckning** (eller återkallande) är mer viktigt – och du vill besvara så många frågor som möjligt, även om det bara finns en partiell relation till användarens fråga – och sedan sänka tröskelvärdet. Det innebär att det kan finnas flera fall där svaret inte svarar på användarens faktiska fråga, men ger något annat särskilt besvarat svar. *Exempel:* om du gör tröskelvärdet **30**kan du ge svar på frågor som "var kan jag redigera mitt KB?".

> [!NOTE]
> Nyare versioner av QnA Maker innehåller förbättringar av bedömnings logik och kan påverka din tröskel. När som helst kan du uppdatera tjänsten, se till att testa och justera tröskelvärdet om det behövs. Du kan kontrol lera din QnA-version [här](https://www.qnamaker.ai/UserSettings)och se hur du hämtar de senaste uppdateringarna [här](../How-To/set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates).

## <a name="set-threshold"></a>Ange tröskel 

Ange tröskelvärdet som en egenskap för [GENERATEANSWER API-JSON-texten](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration). Det innebär att du ställer in den för varje anrop till GenerateAnswer. 

I bot-ramverket ställer du in poängen som en del av alternativ- [C#](../how-to/metadata-generateanswer-usage.md?#use-qna-maker-with-a-bot-in-c) objektet med eller [Node. js](../how-to/metadata-generateanswer-usage.md?#use-qna-maker-with-a-bot-in-nodejs).

## <a name="improve-confidence-scores"></a>Förbättra förtroende poängen
Om du vill förbättra förtroende poängen för ett visst svar på en användar fråga kan du lägga till användar frågan i kunskaps basen som en annan fråga på det svaret. Du kan också använda Skift läges känsliga [ord ändringar](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/alterations/replace) för att lägga till synonymer till nyckelord i din KB.


## <a name="similar-confidence-scores"></a>Liknande förtroende Poäng
När flera svar har liknande förtroende poäng är det troligt att frågan var för allmän och därför matchad med lika stor sannolikhet med flera svar. Försök att strukturera kring bättre så att varje QnA entitet har en distinkt avsikt.


## <a name="confidence-score-differences"></a>Skillnader i förtroende Poäng
Förtroende poängen för ett svar kan ändra försumbarheten mellan testet och den publicerade versionen av kunskaps basen även om innehållet är detsamma. Detta beror på att innehållet i testet och den publicerade kunskaps basen finns i olika Azure Kognitiv sökning-index. När du publicerar en kunskaps bas, flyttas frågan och svars innehållet i kunskaps basen från test indexet till ett produktions index i Azure Search. Se hur [publicerings](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) åtgärden fungerar.

Om du har en kunskaps bas i olika regioner använder varje region sitt eget Azure Kognitiv sökning-index. Eftersom olika index används är poängen inte identiska. 


## <a name="no-match-found"></a>Ingen matchning hittades
När ingen lämplig matchning påträffas av Ranging returneras förtroende poängen på 0,0 eller "ingen", och standard svaret är "ingen lämplig matchning hittades i KB". Du kan åsidosätta [standardsvaret](#change-default-answer) i robot-eller program koden som anropar slut punkten. Alternativt kan du också ställa in åsidosättande svar i Azure och detta ändrar standardvärdet för alla kunskaps banker som distribueras i en viss QnA Maker tjänst.

## <a name="change-default-answer"></a>Ändra standard svar

1. Gå till [Azure Portal](https://portal.azure.com) och navigera till resurs gruppen som representerar den QNA Maker tjänst som du har skapat.

2. Klicka för att öppna **App Service**.

    ![I Azure Portal, Access app service för QnA Maker](../media/qnamaker-concepts-confidencescore/set-default-response.png)

3. Klicka på **program inställningar** och redigera fältet **DefaultAnswer** till önskat standard svar. Klicka på **Spara**.

    ![Välj program inställningar och redigera sedan DefaultAnswer för QnA Maker](../media/qnamaker-concepts-confidencescore/change-response.png)

4. Starta om App Service

    ![När du har ändrat DefaultAnswer startar du om QnA Maker AppService](../media/qnamaker-faq/qnamaker-appservice-restart.png)


## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Data källor som stöds](./data-sources-supported.md)

