---
title: Företagskoncept - LUIS
titleSuffix: Azure Cognitive Services
description: Förstå designkoncept för stora LUIS-appar eller flera appar, inklusive LUIS och QnA Maker tillsammans.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: efef3faf3cc4ff04235254f0ff6538d92a831196
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221066"
---
# <a name="enterprise-strategies-for-a-luis-app"></a>Företagsstrategier för en LUIS-app
Granska de här designstrategierna för företagsappen.

## <a name="when-you-expect-luis-requests-beyond-the-quota"></a>När du förväntar dig LUIS-begäranden utöver kvoten

LUIS har en månatlig kvot samt en kvot per sekund, baserat på prisnivån för Azure-resursen. 

Om din LUIS-appbegäranhet överskrider den tillåtna [kvotsatsen](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/)kan du:

* Sprid inläsningen till fler LUIS-appar med [samma appdefinition](#use-multiple-apps-with-same-app-definition). Detta inkluderar, eventuellt, kör LUIS från en [behållare](luis-container-howto.md). 
* Skapa och [tilldela flera nycklar](#assign-multiple-luis-keys-to-same-app) till appen. 

### <a name="use-multiple-apps-with-same-app-definition"></a>Använda flera appar med samma appdefinition
Exportera den ursprungliga LUIS-appen och importera sedan tillbaka appen till separata appar. Varje app har ett eget app-ID. När du publicerar, i stället för att använda samma nyckel i alla appar, skapar du en separat nyckel för varje app. Balansera belastningen för alla appar så att ingen enskild app är överväldigad. Lägg till [programstatistik](luis-tutorial-bot-csharp-appinsights.md) för att övervaka användningen. 

För att få samma högsta avsikt mellan alla appar, se till att avsiktsförutsägelsen mellan den första och andra avsikten är tillräckligt bred för att LUIS inte är förvirrad, vilket ger olika resultat mellan appar för mindre variationer i yttranden. 

När du tränar dessa syskonappar ska du se till att [träna med alla data](luis-how-to-train.md#train-with-all-data).

Ange en enda app som huvudsida. Alla yttranden som föreslås för granskning bör läggas till i huvudappen och sedan flyttas tillbaka till alla andra appar. Detta är antingen en fullständig export av appen eller läsa in de märkta yttrandena från bakgrunden till barnen. Inläsning kan göras från antingen [LUIS-webbplatsen](luis-reference-regions.md) eller redigerings-API:et för ett [enda uttryck](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08) eller för en [batch](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09). 

Schemalägg en periodisk granskning, till exempel varannan vecka, av [slutpunktsyttranden](luis-how-to-review-endpoint-utterances.md) för aktiv inlärning och omskola och publicera om. 

### <a name="assign-multiple-luis-keys-to-same-app"></a>Tilldela flera LUIS-nycklar till samma app
Om LUIS-appen får fler slutpunktsträffar än vad kvoten för den enda tangenten tillåter skapar och tilldelar du fler nycklar till LUIS-appen. Skapa en trafikhanterare eller belastningsutjämnare för att hantera slutpunktsfrågorna över slutpunktsnycklarna. 

## <a name="when-your-monolithic-app-returns-wrong-intent"></a>När din monolitiska app returnerar fel avsikt
Om din app är avsedd att förutsäga en mängd olika användaryttranden kan du överväga att implementera [leveransmodellen](#dispatch-tool-and-model). Genom att bryta upp en monolitisk app kan LUIS fokusera identifieringen mellan avsikter i stället för att bli förvirrad mellan avsikter i den överordnade appen och underordnade appar. 

Schemalägg en periodisk [granskning av slutpunktsyttranden](luis-how-to-review-endpoint-utterances.md) för aktiv inlärning, till exempel varannan vecka, och sedan träna om och publicera om. 

## <a name="when-you-need-to-have-more-than-500-intents"></a>När du behöver ha mer än 500 avsikter
Anta att du utvecklar en kontorsassistent som har över 500 avsikter. Om 200 avsikter avser schemaläggningsmöten handlar 200 om påminnelser, 200 handlar om att få information om kollegor och 200 är för att skicka e-post, gruppavsikter så att varje grupp finns i en enda app och sedan skapa en app på den högsta nivån som innehåller varje avsikt. Använd [leveransmodellen](#dispatch-tool-and-model) för att skapa appen på den översta nivån. Ändra sedan din bot för att använda kaskad samtal som visas i [leveransmodellen handledning](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&branch=master&tabs=cs). 

## <a name="when-you-need-to-combine-several-luis-and-qna-maker-apps"></a>När du behöver kombinera flera LUIS- och QnA maker-appar
Om du har flera LUIS- och QnA-maker-appar som behöver svara på en robot använder du [leveransmodellen](#dispatch-tool-and-model) för att skapa appen på den översta nivån.  Ändra sedan din bot för att använda kaskad samtal som visas i [leveransmodellen handledning](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&branch=master&tabs=cs). 

## <a name="dispatch-tool-and-model"></a>Leveransverktyg och modell
Använd kommandoradsverktyget [Dispatch][dispatch-tool] som finns i [BotBuilder-verktyg](https://github.com/Microsoft/botbuilder-tools) för att kombinera flera LUIS- och/eller QnA Maker-appar till en överordnad LUIS-app. Med den här metoden kan du ha en överordnad domän som omfattar alla ämnen och olika underordnade ämnesdomäner i separata appar. 

![Konceptuell bild av sändningsarkitektur](./media/luis-concept-enterprise/dispatch-architecture.png)

Den överordnade domänen noteras i `Dispatch` LUIS med en version som heter i applistan. 

Chattroboten tar emot uttrycket och skickar sedan till den överordnade LUIS-appen för förutsägelse. Den översta förväntade avsikten från den överordnade appen avgör vilken LUIS-underordnad app som kallas härnäst. Chattroboten skickar uttryck till den underordnade appen för en mer specifik förutsägelse.

Förstå hur den här hierarkin av anrop görs från Bot Builder v4 [dispatcher-application-tutorial](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&branch=master&tabs=cs).  

### <a name="intent-limits-in-dispatch-model"></a>Avsiktsgränser i leveransmodellen
Ett leveransprogram har 500 leveranskällor, motsvarande 500 avsikter, som det högsta. 

## <a name="more-information"></a>Mer information

* [Bot ram SDK](https://github.com/Microsoft/botframework)
* [Självstudiekurs för leveransmodell](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&branch=master&tabs=cs)
* [Skicka CLI](https://github.com/Microsoft/botbuilder-tools)
* Exempel på leveransmodellrobot - [.NET](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/14.nlp-with-dispatch), [Node.js](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs/14.nlp-with-dispatch)

## <a name="next-steps"></a>Nästa steg

* Läs om hur du [testar en batch](luis-how-to-batch-test.md)

[dispatcher-application-tutorial]: https://aka.ms/bot-dispatch
[dispatch-tool]: https://aka.ms/dispatch-tool
