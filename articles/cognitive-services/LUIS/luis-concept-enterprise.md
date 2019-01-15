---
title: Enterprise-begrepp
titleSuffix: Language Understanding - Azure Cognitive Services
description: Information om design-principerna för stora LUIS-appar eller flera appar, inklusive LUIS och QnA Maker tillsammans.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: diberry
ms.openlocfilehash: 7c12118d0be939f63a2a11fa4dcdd45b87bcd3aa
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2019
ms.locfileid: "54263177"
---
# <a name="enterprise-strategies-for-a-luis-app"></a>Enterprise-strategier för en LUIS-app
Granska dessa design-strategier för din enterprise-app.

## <a name="when-you-expect-luis-requests-beyond-the-quota"></a>När du förväntar dig LUIS begäranden utöver kvoten
Om dina LUIS app begäran överskrider den tillåtna [kvot rate](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/), sprida belastningen till mer LUIS-appar med den [samma app-definition](#use-multiple-apps-with-same-app-definition) eller skapa och [tilldela flera nycklar](#assign-multiple-luis-keys-to-same-app) till den App. 

### <a name="use-multiple-apps-with-same-app-definition"></a>Använda flera appar med samma app-definition
Exportera den ursprungliga LUIS-appen och sedan importera appen tillbaka till separata appar. Varje app har sin egen app-ID. När du publicerar, istället för att använda samma nyckel över alla appar, skapa en separat nyckel för varje app. Balansera belastningen över alla appar så att ingen enskild app blir överbelastad till följd. Lägg till [Application Insights](luis-tutorial-bot-csharp-appinsights.md) att övervaka användningen. 

Kontrollera avsikt förutsägelsen mellan första och andra avsikten är liten att LUIS inte är blandas ihop, vilket ger olika resultat mellan appar för smärre variationer i uttryck för att få samma främsta syftet mellan alla appar. 

Ange en enda app som huvudserver. Yttranden som föreslås för granskning bör vara i appen master och därefter flyttas tillbaka till alla andra appar. Det här är antingen en fullständig export av appen eller läser in taggade yttranden från huvudmålservern till underordnade. Läser in kan göras från antingen den [LUIS](luis-reference-regions.md) webbplats eller redigering API: et för en [enkel uttryck](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08) eller för en [batch](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09). 

Schemalägga en regelbunden [granskning av slutpunkten yttranden](luis-how-to-review-endoint-utt.md) för aktiv inlärning, till exempel varannan vecka, träna och publicera. 

### <a name="assign-multiple-luis-keys-to-same-app"></a>Tilldela flera LUIS nycklar till samma app
Om LUIS-appen tar emot mer endpoint träffar än din enda nyckel kvoten kan skapa och tilldela fler nycklar till LUIS-app. Skapa en traffic manager eller belastningsutjämnare för att hantera endpoint-frågor över slutpunkt-nycklar. 

## <a name="when-your-monolithic-app-returns-wrong-intent"></a>När en monolitisk app returnerar fel avsikt
Om din app är avsedd att förutsäga ett stort antal användare yttranden, Överväg att implementera den [dispatch modellen](#dispatch-tool-and-model). Dela upp en monolitisk app kan LUIS fokus identifiering mellan avsikter har i stället för förvirrande mellan avsikter över appen överordnade och underordnade appar. 

Schemalägga en regelbunden [granskning av slutpunkten yttranden](luis-how-to-review-endoint-utt.md) för aktiv inlärning, till exempel varannan vecka, träna och publicera. 

## <a name="when-you-need-to-have-more-than-500-intents"></a>När du behöver ha fler än 500 intentioner
Anta exempelvis att du utvecklar en office-assistent som har över 500 intentioner. Om 200 avsikter relaterade till att schemalägga möten, 200 är i färd påminnelser, 200 är om att få information om kollegor, och 200 är för att skicka e-post, gruppen avsikter så att varje grupp är i samma app, sedan skapa en översta app som innehåller varje avsikt. Använd den [skicka verktyget och arkitektur](#dispatch-tool-and-model) att bygga appen på översta nivån. Ändra din robot för att använda sammanhängande anropet som visas i den [dispatch självstudien][dispatcher-application-tutorial]. 

## <a name="when-you-need-to-combine-several-luis-and-qna-maker-apps"></a>När du behöver att kombinera flera LUIS och QnA maker-appar
Om du har flera LUIS och QnA maker-appar som måste svara på en robot, Använd den [dispatch verktyget](#dispatch-tool-and-model) att bygga appen på översta nivån. Ändra din robot för att använda sammanhängande anropet som visas i den [dispatch självstudien][dispatcher-application-tutorial]. 

## <a name="dispatch-tool-and-model"></a>Dispatch-verktyget och modell
Använd den [Dispatch] [ dispatch-tool] kommandoradsverktyg, finns i [BotBuilder-tools](https://github.com/Microsoft/botbuilder-tools) att kombinera flera LUIS och/eller QnA Maker appar i en överordnad LUIS-app. Den här metoden låter dig ha en överordnad domän, inklusive alla ämnen och olika underordnade ämnesdomäner i separata appar. 

![Bild av dispatch-arkitektur](./media/luis-concept-enterprise/dispatch-architecture.png)

Den överordnade domänen som anges i LUIS med en version med namnet `Dispatch` i applistan. 

Chattrobot för den tar emot uttryck och sedan skickar till överordnat LUIS-app för förutsägelse. Främsta förväntade avsikten från överordnade appen anger vilka underordnade LUIS-app kallas därefter. Chattrobot skickar uttryck i appen underordnade för en mer specifik förutsägelse.

Förstå hur den här hierarkin för anrop görs från Bot Builder-v4 [dispatcher-program-tutorial][dispatcher-application-tutorial].  

### <a name="intent-limits-in-dispatch-model"></a>Avsiktshantering gränser i dispatch-modellen
En dispatch-programmet har 500 dispatch-källor, motsvarar 500 intentioner som maximum. 

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [testa en batch](luis-how-to-batch-test.md)

[dispatcher-application-tutorial]: https://aka.ms/bot-dispatch
[dispatch-tool]: https://aka.ms/dispatch-tool