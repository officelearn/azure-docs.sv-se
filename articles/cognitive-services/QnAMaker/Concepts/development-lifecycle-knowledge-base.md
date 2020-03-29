---
title: Kunskapsbasens livscykel - QnA Maker
description: QnA Maker lär sig bäst i en iterativ cykel av modelländringar, uttrycksexempel, publicera och samla in data från slutpunktsfrågor.
ms.topic: conceptual
ms.date: 02/27/2020
ms.openlocfilehash: 98fbd81baa717c981486f33cfb2b3a608cec27c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77914960"
---
# <a name="knowledge-base-lifecycle-in-qna-maker"></a>Kunskapsbaslivscykeln i QnA Maker
QnA Maker lär sig bäst i en iterativ cykel av modelländringar, uttrycksexempel, publicera och samla in data från slutpunktsfrågor.

![Redigeringscykel](../media/qnamaker-concepts-lifecycle/kb-lifecycle.png)

## <a name="creating-a-qna-maker-knowledge-base"></a>Skapa en QnA Maker-kunskapsbas
QnA Maker-kunskapsbasen (KB) ger ett svar på en användarfråga som bäst matchar en användarfråga baserat på kb-kB:s innehåll. Att skapa en kunskapsbas är en engångsåtgärd för att konfigurera en innehållsdatabas med frågor, svar och associerade metadata. En kunskapsbas kan skapas genom att genomsöka befintligt innehåll som FAQ-sidor, produkthandböcker eller strukturerade Q-A-par. Lär dig hur du [skapar en kunskapsbas](../quickstarts/create-publish-knowledge-base.md).

## <a name="testing-and-updating-the-knowledge-base"></a>Testa och uppdatera kunskapsbasen

Kunskapsbasen är redo för testning när den är fylld med innehåll, antingen redaktionellt eller genom automatisk extrahering. Interaktiv testning kan göras i QnA Maker-portalen via **testpanelen** genom att ange vanliga användarfrågor och kontrollera att svaren returnerades med rätt svar och tillräcklig konfidenspoäng.

* **Så här åtgärdar du låga konfidenspoäng**: lägg till alternativa frågor.
* **När en fråga returnerar [felaktigt standardsvaret:](../How-to/change-default-answer.md)** Lägg till nya svar på rätt fråga.

Denna snäva slinga av test-uppdatering fortsätter tills du är nöjd med resultatet. Lär dig hur du [testar din kunskapsbas](../How-To/test-knowledge-base.md).

För stora KBs använder du automatiserad testning med `isTest` [generateAnswer API](../how-to/metadata-generateanswer-usage.md#get-answer-predictions-with-the-generateanswer-api) och egenskapen body, som frågar kunskapsbasen `test` i stället för den publicerade kunskapsbasen.

```json
{
  "question": "example question",
  "top": 3,
  "userId": "Default",
  "isTest": true
}
```

## <a name="publish-the-knowledge-base"></a>Publicera kunskapsbasen
När du är klar med att testa kunskapsbasen kan du publicera den. Publicera skickar den senaste versionen av den testade kunskapsbasen till ett dedikerat Azure Cognitive Search-index som representerar den **publicerade kunskapsbasen.** Den skapar även en slutpunkt som kan anropas i ditt program eller en chattrobot.

På så sätt påverkar inte alla ändringar som görs i testversionen av kunskapsbasen den publicerade versionen som kan vara live i ett produktionsprogram.

Var och en av dessa kunskapsbaser kan riktas för testning separat. Med hjälp av API:erna kan du rikta `isTest` in dig på testversionen av kunskapsbasen med brödtextegenskapen i generateAnswer-anropet.

Läs om hur du [publicerar din kunskapsbas](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).

## <a name="monitor-usage"></a>Övervaka användning
För att kunna logga chattloggarna för din tjänst måste du aktivera Application Insights när du [skapar din QnA Maker-tjänst](../How-To/set-up-qnamaker-service-azure.md).

Du kan få olika analyser av din tjänstanvändning. Läs mer om hur du använder programinsikter för att få [analyser för din QnA Maker-tjänst](../How-To/get-analytics-knowledge-base.md).

Baserat på vad du lär dig av dina analyser kan du göra lämpliga [uppdateringar av din kunskapsbas](../How-To/edit-knowledge-base.md).

## <a name="version-control-for-data-in-your-knowledge-base"></a>Versionskontroll för data i din kunskapsbas

Versionskontroll för data tillhandahålls via import-/exportfunktionerna på sidan **Inställningar** i QnA Maker-portalen.

Du kan säkerhetskopiera en kunskapsbas genom att `.tsv` `.xls` exportera kunskapsbasen, antingen i eller format. När du har exporterat den här filen som en del av den vanliga källkontrollkontrollen.

När du behöver gå tillbaka till en viss version måste du importera filen från det lokala systemet. En exporterad kunskapsbas **får** endast användas via import på sidan **Inställningar.** Det kan inte användas som en datakälla för fil- eller URL-dokument. Detta ersätter frågor och svar som för närvarande finns i kunskapsbasen med innehållet i den importerade filen.

## <a name="test-and-production-knowledge-base"></a>Kunskapsbas för provning och produktion
En kunskapsbas är lagringsplatsen för frågor och svarsuppsättningar som skapats, underhållits och används via QnA Maker. Varje QnA Maker-resurs kan innehålla flera kunskapsbaser.

En kunskapsbas har två tillstånd: *testa* och *publiceras*.

### <a name="test-knowledge-base"></a>Testkunskapsbas

*Testkunskapsbasen* är den version som för närvarande redigeras, sparas och testas för att svaren är korrekt och fullständiga. Ändringar som görs i testkunskapsbasen påverkar inte slutanvändaren av ditt program eller chattrobot. Testkunskapsbasen kallas `test` i HTTP-begäran. Kunskapen `test` är tillgänglig med QnA Maker portal interaktiva **testfönstret.**

### <a name="production-knowledge-base"></a>Kunskapsbas för produktion

Den *publicerade kunskapsbasen* är den version som används i chattroboten eller programmet. Åtgärden att publicera en kunskapsbas placerar innehållet i testkunskapsbasen i den publicerade versionen av kunskapsbasen. Eftersom den publicerade kunskapsbasen är den version som programmet använder via slutpunkten kontrollerar du att innehållet är korrekt och väl testat. Den publicerade kunskapsbasen kallas `prod` i HTTP-begäran.


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Förslag på aktivt lärande](./active-learning-suggestions.md)