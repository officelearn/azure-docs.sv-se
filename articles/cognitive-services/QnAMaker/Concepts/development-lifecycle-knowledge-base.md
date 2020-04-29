---
title: Livs cykel för kunskaps bas – QnA Maker
description: QnA Maker lär sig bäst i en iterativ cykel med modell ändringar, uttryck exempel, publicering och insamling av data från slut punkts frågor.
ms.topic: conceptual
ms.date: 02/27/2020
ms.openlocfilehash: 98fbd81baa717c981486f33cfb2b3a608cec27c7
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "77914960"
---
# <a name="knowledge-base-lifecycle-in-qna-maker"></a>Kunskaps bas livs cykel i QnA Maker
QnA Maker lär sig bäst i en iterativ cykel med modell ändringar, uttryck exempel, publicering och insamling av data från slut punkts frågor.

![Redigeringscykel](../media/qnamaker-concepts-lifecycle/kb-lifecycle.png)

## <a name="creating-a-qna-maker-knowledge-base"></a>Skapa en QnA Maker kunskaps bas
QnA Maker Knowledge Base-slutpunkt (KB) tillhandahåller ett svar till en användar fråga baserat på innehållet i KB. Att skapa en kunskaps bas är en engångs åtgärd för att skapa en innehålls databas med frågor, svar och associerade metadata. Du kan skapa en kunskaps bas genom att crawla befintligt innehåll, till exempel sidor med vanliga frågor, produkt handböcker eller strukturerat Q – A par. Lär dig hur du [skapar en kunskaps bas](../quickstarts/create-publish-knowledge-base.md).

## <a name="testing-and-updating-the-knowledge-base"></a>Testa och uppdatera kunskaps basen

Kunskaps basen är redo för testning när den har fyllts med innehåll, antingen i redaktionellt syfte eller genom automatisk extrahering. Du kan göra interaktiva tester i QnA Maker-portalen via **test** panelen genom att ange vanliga användar frågor och kontrol lera att svaren som returneras med rätt svar och tillräckligt förtroende poäng.

* **Så här åtgärdar du låga förtroende poäng**: Lägg till alternativa frågor.
* **När en fråga felaktigt returnerar [standardsvaret](../How-to/change-default-answer.md)**: Lägg till nya svar på rätt fråga.

Den här tätt slingan av test-Update fortsätter tills du är nöjd med resultatet. Lär dig hur du [testar din kunskaps bas](../How-To/test-knowledge-base.md).

För stora KB använder du automatiserad testning med [generateAnswer-API: et](../how-to/metadata-generateanswer-usage.md#get-answer-predictions-with-the-generateanswer-api) och egenskapen `isTest` Body, som `test` frågar kunskaps basen i stället för den publicerade kunskaps basen.

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

Var och en av dessa kunskaps banker kan vara inriktade på att testa separat. Med hjälp av API: erna kan du rikta test versionen av kunskaps basen `isTest` med egenskapen Body i generateAnswer-anropet.

Lär dig hur du [publicerar din kunskaps bas](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).

## <a name="monitor-usage"></a>Övervaka användning
Om du vill kunna logga chattarna för tjänsten måste du aktivera Application Insights när du [skapar din QNA Maker-tjänst](../How-To/set-up-qnamaker-service-azure.md).

Du kan få olika analyser av din tjänst användning. Lär dig mer om hur du använder Application Insights för att få [analyser för din QNA Maker-tjänst](../How-To/get-analytics-knowledge-base.md).

Med hjälp av vad du får från din analys kan du göra lämpliga [uppdateringar i kunskaps basen](../How-To/edit-knowledge-base.md).

## <a name="version-control-for-data-in-your-knowledge-base"></a>Versions kontroll för data i din kunskaps bas

Versions kontroll för data tillhandahålls via import/export-funktionerna på sidan **Inställningar** i QNA Maker-portalen.

Du kan säkerhetskopiera en kunskaps bas genom att exportera kunskaps basen i antingen `.tsv` eller `.xls` . När du har exporterat tar du med den här filen som en del av din vanliga käll kontrolls kontroll.

När du behöver gå tillbaka till en speciell version måste du importera filen från det lokala systemet. En exporterad kunskaps bas **får** bara användas via import på sidan **Inställningar** . Det går inte att använda den som en fil eller ett URL-dokuments data källa. Då ersätts frågor och svar för närvarande i kunskaps basen med innehållet i den importerade filen.

## <a name="test-and-production-knowledge-base"></a>Test-och produktions kunskaps bas
En kunskaps bas är en lagrings plats för frågor och svars uppsättningar som skapats, bibehålls och används via QnA Maker. Varje QnA Maker resurs kan innehålla flera kunskaps baser.

En kunskaps bas har två tillstånd: *testa* och *publicerade*.

### <a name="test-knowledge-base"></a>Testa kunskaps bas

*Test kunskaps basen* är den version som för närvarande redige ras, sparas och testas för precision och fullständig svars information. Ändringar som görs i test kunskaps basen påverkar inte slutanvändaren för ditt program eller chatt-robot. Test kunskaps basen kallas `test` i http-begäran. `test` Kunskapen är tillgänglig med QNA Makers interaktiva **test** fönster.

### <a name="production-knowledge-base"></a>Produktions kunskaps bas

Den *publicerade kunskaps basen* är den version som används i din chatt eller ditt program. Åtgärden att publicera en kunskaps bas ger innehållet i test kunskaps basen i den publicerade versionen av kunskaps basen. Eftersom den publicerade kunskaps basen är den version som programmet använder genom slut punkten kontrollerar du att innehållet är korrekt och testat. Den publicerade kunskaps basen kallas `prod` i http-begäran.


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Aktiva utbildnings förslag](./active-learning-suggestions.md)