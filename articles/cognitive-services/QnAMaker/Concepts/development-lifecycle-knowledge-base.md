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
ms.date: 01/27/2020
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: b1978e45a7554358ddd948879143411f89e4c1b2
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2020
ms.locfileid: "76843413"
---
# <a name="knowledge-base-lifecycle-in-qna-maker"></a>Kunskapsbas livscykel i QnA Maker
QnA Maker lär sig bäst i en iterativ livscykel datamodellen ändras, uttryck exempel, publicering och samla in data från slutpunkt-frågor.

![Redigeringscykel](../media/qnamaker-concepts-lifecycle/kb-lifecycle.png)

## <a name="creating-a-qna-maker-knowledge-base"></a>Skapa en kunskapsbas med QnA Maker
Slutpunkt för QnA Maker knowledge base (KB) innehåller en bästa matchning svaret på en användarfråga baserat på innehållet i KB. Det är en engångsåtgärd för att skapa en innehållsdatabasen frågor, svar och associerade metadata för att skapa en kunskapsbas. En kunskapsbas kan skapas med vår högkvalitativa befintliga innehåll som sidor med vanliga frågor och svar, produkthandböcker eller strukturerade Q-A-par. Lär dig hur du [skapa en kunskapsbas](../quickstarts/create-publish-knowledge-base.md).

## <a name="testing-and-updating-the-knowledge-base"></a>Testa och uppdatera kunskapsbasen

Kunskapsbasen är klart för testning när det fylls i med innehåll, redigeringsmässigt eller via automatisk extrahering. Du kan göra interaktiva tester i QnA Maker-portalen via **test** panelen genom att ange vanliga användar frågor och kontrol lera att svaren som returneras med rätt svar och tillräckligt förtroende poäng.

* **Så här åtgärdar du låga förtroende poäng**: Lägg till alternativa frågor.
* **När en fråga felaktigt returnerar [standardsvaret](../How-to/change-default-answer.md)** : Lägg till nya svar på rätt fråga.

Den här tät loop av test-uppdatering fortsätter tills du är nöjd med resultaten. Lär dig hur du [testa kunskapsbasen](../How-To/test-knowledge-base.md).

För stora KB använder du automatiserad testning med [generateAnswer-API: et](../how-to/metadata-generateanswer-usage.md#get-answer-predictions-with-the-generateanswer-api) och egenskapen `isTest` Body, som frågar `test` Knowledge Base i stället för den publicerade kunskaps basen.

```json
{
  "question": "example question",
  "top": 3,
  "userId": "Default",
  "isTest": true
}
```

## <a name="publish-the-knowledge-base"></a>Publicera kunskapsbasen
När du är klar testar kunskapsbasen kan du publicera den. Publicera push-överför den senaste versionen av den testade kunskaps basen till ett dedikerat Azure Kognitiv sökning-index som representerar den **publicerade** kunskaps basen. Den skapar även en slutpunkt som kan anropas i ditt program eller en chattrobot.

På så sätt kan alla ändringar som görs till testversion av kunskapsbasen påverkar inte den publicerade versionen som kanske bor i ett produktionsprogram.

Var och en av dessa kunskapsbaser kan vara mål för att testa separat. Med hjälp av API: erna kan du rikta test versionen av kunskaps basen med `isTest` Body-egenskapen i generateAnswer-anropet.

Lär dig hur du [publicera din kunskapsbas](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).

## <a name="monitor-usage"></a>Övervaka användning
Om du vill kunna logga chattloggarna av din tjänst skulle du behöva aktivera Application Insights när du [skapa QnA Maker-tjänsten](../How-To/set-up-qnamaker-service-azure.md).

Du kan få olika analys av din tjänstanvändning. Läs mer om hur du använder application insights för att hämta [analys för QnA Maker-tjänsten](../How-To/get-analytics-knowledge-base.md).

Baserat på vad du lär dig av dina analyser, gör rätt [uppdateringar till din kunskapsbas](../How-To/edit-knowledge-base.md).

## <a name="version-control-for-data-in-your-knowledge-base"></a>Versions kontroll för data i din kunskaps bas

Versions kontroll för data tillhandahålls via import/export-funktionerna på sidan **Inställningar** i QNA Maker-portalen.

Du kan säkerhetskopiera en kunskaps bas genom att exportera kunskaps basen i antingen `.tsv`-eller `.xls`s format. När du har exporterat tar du med den här filen som en del av din vanliga käll kontrolls kontroll.

När du behöver gå tillbaka till en speciell version måste du importera filen från det lokala systemet. En exporterad kunskaps bas **får** bara användas via import på sidan **Inställningar** . Det går inte att använda den som en fil eller ett URL-dokuments data källa. Då ersätts frågor och svar för närvarande i kunskaps basen med innehållet i den importerade filen.

## <a name="test-and-production-knowledge-base"></a>Test-och produktions kunskaps bas
En kunskaps bas är en lagrings plats för frågor och svars uppsättningar som skapats, bibehålls och används via QnA Maker. Varje QnA Maker resurs kan innehålla flera kunskaps baser.

En kunskaps bas har två tillstånd: *testa* och *publicerade*.

### <a name="test-knowledge-base"></a>Testa kunskaps bas

*Test kunskaps basen* är den version som för närvarande redige ras, sparas och testas för precision och fullständig svars information. Ändringar som görs i test kunskaps basen påverkar inte slutanvändaren för ditt program eller chatt-robot. Test kunskaps basen kallas `test` i HTTP-begäran. `test` kunskapen är tillgänglig med QnA Makers interaktiva **test** fönster.

### <a name="production-knowledge-base"></a>Produktions kunskaps bas

Den *publicerade kunskaps basen* är den version som används i din chatt eller ditt program. Åtgärden att publicera en kunskaps bas ger innehållet i test kunskaps basen i den publicerade versionen av kunskaps basen. Eftersom den publicerade kunskaps basen är den version som programmet använder genom slut punkten kontrollerar du att innehållet är korrekt och testat. Den publicerade kunskaps basen kallas `prod` i HTTP-begäran.


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Förtroendepoäng](./confidence-score.md)