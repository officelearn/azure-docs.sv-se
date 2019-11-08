---
title: Livs cykel för kunskaps bas – QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker lär sig bäst i en iterativ cykel med modell ändringar, uttryck exempel, publicering och insamling av data från slut punkts frågor.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 1cb5af13bdd309c762337e64ecde8538afc756b0
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2019
ms.locfileid: "73794845"
---
# <a name="knowledge-base-lifecycle-in-qna-maker"></a>Kunskaps bas livs cykel i QnA Maker
QnA Maker lär sig bäst i en iterativ cykel med modell ändringar, uttryck exempel, publicering och insamling av data från slut punkts frågor. 

![Redigeringscykel](../media/qnamaker-concepts-lifecycle/kb-lifecycle.png)

## <a name="creating-a-qna-maker-knowledge-base"></a>Skapa en QnA Maker kunskaps bas
QnA Maker Knowledge Base-slutpunkt (KB) tillhandahåller ett svar till en användar fråga baserat på innehållet i KB. Att skapa en kunskaps bas är en engångs åtgärd för att skapa en innehålls databas med frågor, svar och associerade metadata. Du kan skapa en kunskaps bas genom att crawla befintligt innehåll, till exempel sidor med vanliga frågor, produkt handböcker eller strukturerat Q – A par. Lär dig hur du [skapar en kunskaps bas](../quickstarts/create-publish-knowledge-base.md).

## <a name="testing-and-updating-the-knowledge-base"></a>Testa och uppdatera kunskaps basen

Kunskaps basen är redo för testning när den har fyllts med innehåll, antingen i redaktionellt syfte eller genom automatisk extrahering. Du kan göra interaktiva tester i QnA Maker-portalen via **test** panelen genom att ange vanliga användar frågor och kontrol lera att svaren som returneras med rätt svar och tillräckligt förtroende poäng. 

* **Så här åtgärdar du låga förtroende poäng**: Lägg till alternativa frågor. 
* **När en fråga felaktigt returnerar [standardsvaret](confidence-score.md#change-default-answer)** : Lägg till nya svar på rätt fråga. 

Den här tätt slingan av test-Update fortsätter tills du är nöjd med resultatet. Lär dig hur du [testar din kunskaps bas](../How-To/test-knowledge-base.md).

För stora KB använder du automatiserad testning med [generateAnswer-API: et](../how-to/metadata-generateanswer-usage.md#get-answer-predictions-with-the-generateanswer-api) och egenskapen `isTest` Body som skickar frågor till `test` kunskaps basen i stället för den publicerade kunskaps basen. 

```json
{
  "question": "example question",
  "top": 3,
  "userId": "Default",
  "isTest": true
}
```

## <a name="publish-the-knowledge-base"></a>Publicera kunskapsbasen
När du är färdig med att testa kunskaps basen kan du publicera den. Publicera push-överför den senaste versionen av den testade kunskaps basen till ett dedikerat Azure Kognitiv sökning-index som representerar den **publicerade** kunskaps basen. Den skapar även en slutpunkt som kan anropas i ditt program eller en chattrobot.

På så sätt påverkar alla ändringar som görs i test versionen av kunskaps basen inte den publicerade versionen som kan vara aktiv i ett produktions program.

Var och en av dessa kunskaps banker kan vara inriktade på att testa separat. Med hjälp av API: erna kan du rikta test versionen av kunskaps basen med `isTest` Body-egenskapen i generateAnswer-anropet.

Lär dig hur du [publicerar din kunskaps bas](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).

## <a name="monitor-usage"></a>Övervaka användning
Om du vill kunna logga chattarna för tjänsten måste du aktivera Application Insights när du [skapar din QNA Maker-tjänst](../How-To/set-up-qnamaker-service-azure.md).

Du kan få olika analyser av din tjänst användning. Lär dig mer om hur du använder Application Insights för att få [analyser för din QNA Maker-tjänst](../How-To/get-analytics-knowledge-base.md).

Med hjälp av vad du får från din analys kan du göra lämpliga [uppdateringar i kunskaps basen](../How-To/edit-knowledge-base.md).

## <a name="version-control-of-a-knowledge-base"></a>Versions kontroll för en kunskaps bas

Versions kontroll tillhandahålls inte av QnA Maker. Du måste exportera din kunskaps bas från sidan **Inställningar** och använda dina egna metoder och verktyg.

Exporten av kunskaps basen till TSV-eller XLS-formatet slutförs från **inställnings** sidan. 

När du behöver gå tillbaka till en speciell version måste du importera filen från det lokala systemet. På sidan **Inställningar** importerar du TSV-eller xls-filen. Då ersätts frågor och svar för närvarande i kunskaps basen med innehållet i den importerade filen.   

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Förtroende Poäng](./confidence-score.md)