---
title: Livscykeln för kunskapsbas - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker lär sig bäst i en iterativ livscykel datamodellen ändras, uttryck exempel, publicering och samla in data från slutpunkt-frågor.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 97a4673be2a611149806855e792c5bf1f7a0942a
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/12/2019
ms.locfileid: "68955176"
---
# <a name="knowledge-base-lifecycle-in-qna-maker"></a>Kunskapsbas livscykel i QnA Maker
QnA Maker lär sig bäst i en iterativ livscykel datamodellen ändras, uttryck exempel, publicering och samla in data från slutpunkt-frågor. 

![Redigeringscykel](../media/qnamaker-concepts-lifecycle/kb-lifecycle.png)

## <a name="creating-a-qna-maker-knowledge-base"></a>Skapa en kunskapsbas med QnA Maker
Slutpunkt för QnA Maker knowledge base (KB) innehåller en bästa matchning svaret på en användarfråga baserat på innehållet i KB. Det är en engångsåtgärd för att skapa en innehållsdatabasen frågor, svar och associerade metadata för att skapa en kunskapsbas. En kunskapsbas kan skapas med vår högkvalitativa befintliga innehåll som sidor med vanliga frågor och svar, produkthandböcker eller strukturerade Q-A-par. Lär dig hur du [skapa en kunskapsbas](../How-To/create-knowledge-base.md).

## <a name="testing-and-updating-the-knowledge-base"></a>Testa och uppdatera kunskapsbasen

Kunskapsbasen är klart för testning när det fylls i med innehåll, redigeringsmässigt eller via automatisk extrahering. Du kan göra interaktiva tester i QnA Maker-portalen via **test** panelen genom att ange vanliga användar frågor och kontrol lera att svaren som returneras med rätt svar och tillräckligt förtroende poäng. 

* **Så här åtgärdar du låga förtroende poäng**: Lägg till alternativa frågor. 
* **När en fråga felaktigt returnerar [](confidence-score.md#change-default-answer)standardsvaret**: Lägg till nya svar på rätt fråga. 

Den här tät loop av test-uppdatering fortsätter tills du är nöjd med resultaten. Lär dig hur du [testa kunskapsbasen](../How-To/test-knowledge-base.md).

För stora KB använder du automatiserad testning med [generateAnswer-API: et](../how-to/metadata-generateanswer-usage.md#get-answer-predictions-with-the-generateanswer-api) och `isTest` egenskapen Body som `test` skickar frågor till kunskaps basen i stället för den publicerade kunskaps basen. 

```json
{
  "question": "example question",
  "top": 3,
  "userId": "Default",
  "isTest": true
}
```

## <a name="publish-the-knowledge-base"></a>Publicera kunskapsbasen
När du är klar testar kunskapsbasen kan du publicera den. Publicera push-meddelanden den senaste versionen av testade kunskapsbasen till en dedikerad Azure Search index som representerar den **publicerade** knowledge base. Den skapar även en slutpunkt som kan anropas i ditt program eller en chattrobot.

På så sätt kan alla ändringar som görs till testversion av kunskapsbasen påverkar inte den publicerade versionen som kanske bor i ett produktionsprogram.

Var och en av dessa kunskapsbaser kan vara mål för att testa separat. Med hjälp av API: erna kan du rikta test versionen av kunskaps basen `isTest` med egenskapen Body i generateAnswer-anropet.

Lär dig hur du [publicera din kunskapsbas](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).

## <a name="monitor-usage"></a>Övervaka användning
Om du vill kunna logga chattloggarna av din tjänst skulle du behöva aktivera Application Insights när du [skapa QnA Maker-tjänsten](../How-To/set-up-qnamaker-service-azure.md).

Du kan få olika analys av din tjänstanvändning. Läs mer om hur du använder application insights för att hämta [analys för QnA Maker-tjänsten](../How-To/get-analytics-knowledge-base.md).

Baserat på vad du lär dig av dina analyser, gör rätt [uppdateringar till din kunskapsbas](../How-To/edit-knowledge-base.md).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Förtroendepoäng](./confidence-score.md)

## <a name="see-also"></a>Se också 

[Kunskapsbas](./knowledge-base.md)
[QnA Maker-översikt](../Overview/overview.md)
