---
title: Enterprise-koncept – LUIS
titleSuffix: Azure Cognitive Services
description: Information om design-principerna för stora LUIS-appar eller flera appar, inklusive LUIS och QnA Maker tillsammans.
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
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78361154"
---
# <a name="enterprise-strategies-for-a-luis-app"></a>Enterprise-strategier för en LUIS-app
Granska dessa design-strategier för din enterprise-app.

## <a name="when-you-expect-luis-requests-beyond-the-quota"></a>När du förväntar dig LUIS begäranden utöver kvoten

LUIS har en månatlig kvot och en kvot på per sekund, baserat på Azure-resursens pris nivå. 

Om din LUIS-app begär frekvens överskrider den tillåtna [kvoten](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/)kan du:

* Sprid belastningen till fler LUIS-appar med [samma app-definition](#use-multiple-apps-with-same-app-definition). Detta inkluderar, om du vill, att köra LUIS från en [behållare](luis-container-howto.md). 
* Skapa och [tilldela flera nycklar](#assign-multiple-luis-keys-to-same-app) till appen. 

### <a name="use-multiple-apps-with-same-app-definition"></a>Använda flera appar med samma app-definition
Exportera den ursprungliga LUIS-appen och sedan importera appen tillbaka till separata appar. Varje app har sin egen app-ID. När du publicerar, istället för att använda samma nyckel över alla appar, skapa en separat nyckel för varje app. Balansera belastningen över alla appar så att ingen enskild app blir överbelastad till följd. Lägg till [Application Insights](luis-tutorial-bot-csharp-appinsights.md) för att övervaka användningen. 

Kontrollera avsikt förutsägelsen mellan första och andra avsikten är liten att LUIS inte är blandas ihop, vilket ger olika resultat mellan appar för smärre variationer i uttryck för att få samma främsta syftet mellan alla appar. 

När du tränar dessa appar på samma nivå måste du [träna med alla data](luis-how-to-train.md#train-with-all-data).

Ange en enda app som huvudserver. Yttranden som föreslås för granskning bör vara i appen master och därefter flyttas tillbaka till alla andra appar. Det här är antingen en fullständig export av appen eller läser in taggade yttranden från huvudmålservern till underordnade. Inläsning kan göras från antingen [Luis](luis-reference-regions.md) -webbplatsen eller redigerings-API: et för en [enskild uttryck](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08) eller för en [batch](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09). 

Schemalägg en regelbunden granskning, till exempel varannan vecka, [slut punkts yttranden](luis-how-to-review-endpoint-utterances.md) för aktiv utbildning, och omträna och publicera igen. 

### <a name="assign-multiple-luis-keys-to-same-app"></a>Tilldela flera LUIS nycklar till samma app
Om LUIS-appen tar emot mer endpoint träffar än din enda nyckel kvoten kan skapa och tilldela fler nycklar till LUIS-app. Skapa en traffic manager eller belastningsutjämnare för att hantera endpoint-frågor över slutpunkt-nycklar. 

## <a name="when-your-monolithic-app-returns-wrong-intent"></a>När en monolitisk app returnerar fel avsikt
Om din app är avsedd att förutsäga en mängd olika användar yttranden bör du överväga att implementera [sändnings modellen](#dispatch-tool-and-model). Dela upp en monolitisk app kan LUIS fokus identifiering mellan avsikter har i stället för förvirrande mellan avsikter över appen överordnade och underordnade appar. 

Schemalägg en regelbunden [granskning av slut punkts yttranden](luis-how-to-review-endpoint-utterances.md) för aktiv inlärning, till exempel varannan vecka, och sedan omträna och publicera igen. 

## <a name="when-you-need-to-have-more-than-500-intents"></a>När du behöver ha fler än 500 intentioner
Anta att du utvecklar en Office-assistent som har över 500-avsikter. Om 200 avsikter relaterade till att schemalägga möten, 200 är i färd påminnelser, 200 är om att få information om kollegor, och 200 är för att skicka e-post, gruppen avsikter så att varje grupp är i samma app, sedan skapa en översta app som innehåller varje avsikt. Använd [sändnings modellen](#dispatch-tool-and-model) för att bygga appen på den översta nivån. Ändra sedan din robot till att använda det sammanhängande anropet, som du ser i [sändnings modellens självstudier](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&branch=master&tabs=cs). 

## <a name="when-you-need-to-combine-several-luis-and-qna-maker-apps"></a>När du behöver att kombinera flera LUIS och QnA maker-appar
Om du har flera LUIS-och QnA Maker-appar som behöver svara på en bot använder du [sändnings modellen](#dispatch-tool-and-model) för att skapa appen på den översta nivån.  Ändra sedan din robot till att använda det sammanhängande anropet, som du ser i [sändnings modellens självstudier](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&branch=master&tabs=cs). 

## <a name="dispatch-tool-and-model"></a>Dispatch-verktyget och modell
Använd kommando rads verktyget [Dispatch][dispatch-tool] som finns i [BotBuilder-tools](https://github.com/Microsoft/botbuilder-tools) för att kombinera flera LUIS och/eller QNA Maker appar till en överordnad Luis-app. Den här metoden låter dig ha en överordnad domän, inklusive alla ämnen och olika underordnade ämnesdomäner i separata appar. 

![Bild av dispatch-arkitektur](./media/luis-concept-enterprise/dispatch-architecture.png)

Den överordnade domänen anges i LUIS med en version med namnet `Dispatch` i listan över appar. 

Chatt-roboten tar emot uttryck och skickar sedan till den överordnade LUIS-appen för förutsägelse. Det mest förväntade syftet från den överordnade appen avgör vilken LUIS-underordnad app som anropas härnäst. Chatt-roboten skickar uttryck till den underordnade appen för en mer detaljerad förutsägelse.

Förstå hur den här hierarkin av anrop görs från Bot Builder v4 [dispatcher-Application-självstudie](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&branch=master&tabs=cs).  

### <a name="intent-limits-in-dispatch-model"></a>Avsiktshantering gränser i dispatch-modellen
En dispatch-programmet har 500 dispatch-källor, motsvarar 500 intentioner som maximum. 

## <a name="more-information"></a>Mer information

* [Robot Framework SDK](https://github.com/Microsoft/botframework)
* [Själv studie kurs om sändnings modell](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&branch=master&tabs=cs)
* [Skicka CLI](https://github.com/Microsoft/botbuilder-tools)
* Robot exempel för sändnings modell – [.net](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/14.nlp-with-dispatch), [Node. js](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs/14.nlp-with-dispatch)

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [testar en batch](luis-how-to-batch-test.md)

[dispatcher-application-tutorial]: https://aka.ms/bot-dispatch
[dispatch-tool]: https://aka.ms/dispatch-tool
