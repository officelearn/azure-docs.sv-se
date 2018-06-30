---
title: Enterprise-begrepp för en THOMAS app - Azure | Microsoft Docs
description: Förstå designbegrepp för stora THOMAS appar.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/05/2018
ms.author: v-geberr
ms.openlocfilehash: f5d1cf61ca7b8d8eeaed52fc3f45f8d4847ddda9
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37108696"
---
# <a name="enterprise-strategies-for-a-luis-app"></a>Enterprise strategier för en THOMAS app
Granska strategierna designen för din företagsapp.

## <a name="when-you-expect-luis-requests-beyond-the-quota"></a>När du förväntar dig THOMAS begäranden utöver kvoten
Om din THOMAS app förfrågningar överskrider den tillåtna [kvot hastighet](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/), sprida belastningen till flera THOMAS appar med den [samma app definition](#use-multiple-apps-with-same-app-definition) eller skapa och [tilldela flera nycklar](#assign-multiple-luis-keys-to-same-app) till den App. 

### <a name="use-multiple-apps-with-same-app-definition"></a>Använda flera appar med samma app definition
Exportera ursprungliga THOMAS appen och sedan importera appen tillbaka till separata appar. Varje app har sin egen app-ID. Skapa en separat nyckel för varje app när du publicerar, istället för att använda samma nyckel i alla appar. Utjämna belastningen över alla appar så att ingen enskild appen blir överbelastad till följd. Lägg till [Programinsikter](luis-tutorial-bot-csharp-appinsights.md) att övervaka förbrukningen. 

Kontrollera att avsiktshantering förutsägelser mellan den första och andra avsikten är litet att THOMAS inte är förväxlas, ger olika resultat mellan appar för mindre variationer i utterances för att få samma översta avsikten mellan alla appar. 

Ange en enda app som huvudserver. Alla utterances som föreslås för granskning ska läggas till appen master sedan flyttas tillbaka till andra appar. Detta är antingen en fullständig export av appen, eller läsa in märkt utterances från master till underordnade. Läser in kan göras från antingen den [THOMAS] [ LUIS] webbplats eller redigering API för en [enkel utterance](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08) eller för en [batch](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09). 

Schemalägga en regelbunden [granskning av slutpunkten utterances](label-suggested-utterances.md) för aktiva, till exempel varannan vecka, sedan träna om och publicera på nytt. 

### <a name="assign-multiple-luis-keys-to-same-app"></a>Tilldela flera THOMAS nycklar till samma app
Om appen THOMAS tar emot flera endpoint träffar än din enda nyckel kvoten tillåter skapar och tilldelar appen THOMAS flera nycklar. Skapa en traffic manager eller belastningsutjämnare för att hantera endpoint frågor över endpoint-nycklar. 

## <a name="when-your-monolithic-app-returns-wrong-intent"></a>När appen monolitisk returnerar fel avsikt
Om din app är avsedd att förutsäga ett stort antal användare utterances, Överväg att implementera den [dispatch modellen](#dispatch-tool-and-model). Dela upp en monolitisk app kan THOMAS fokus identifiering mellan avsikter har i stället för förvirrande mellan avsikter över appen överordnade och underordnade appar. 

Schemalägga en regelbunden [granskning av slutpunkten utterances](label-suggested-utterances.md) för aktiva, till exempel varannan vecka, sedan träna om och publicera på nytt. 

## <a name="when-you-need-to-have-more-than-500-intents"></a>När du behöver ha fler än 500 avsikter
Anta exempelvis att du utvecklar en office-assistent med än 500 avsikter. Om 200 avsikter avser schemalägga möten 200 finns om påminnelser, 200 är om att få information om kollegor och 200 är för att skicka e-post, gruppen avsikter så att varje grupp i en enda app sedan skapa en översta app som innehåller varje avsikt. Använd den [Sänd verktyget och arkitektur](#dispatch-tool-and-model) att bygga appen på den översta nivån. Ändra din bot för att använda sammanhängande anrop som visas i den [dispatch kursen][dispatcher-application-tutorial]. 

## <a name="when-you-need-to-combine-several-luis-and-qna-maker-apps"></a>När du behöver kombinera flera THOMAS och frågor och svar om maker appar
Om du har flera THOMAS och frågor och svar om maker-appar som måste svara på bot, Använd den [dispatch verktyget](#dispatch-tool-and-model) att bygga appen på den översta nivån. Ändra din bot för att använda sammanhängande anrop som visas i den [dispatch kursen][dispatcher-application-tutorial]. 

## <a name="dispatch-tool-and-model"></a>Sändning av verktyget och modell
Använd den [Dispatch] [ dispatch-tool] kommandoradsverktyg, finns i [BotBuilder verktyg](https://github.com/Microsoft/botbuilder-tools) kombinera flera THOMAS och/eller frågor och svar om Maker appar till en överordnad THOMAS app. Den här metoden kan du ha en överordnad domän inklusive alla ämnen och olika underordnade ämnesdomäner i separata appar. 

![Bild av dispatch-arkitektur](./media/luis-concept-enterprise/dispatch-architecture.png)

Den överordnade domänen som anges i THOMAS som en **V Dispatch** app. 

![Skärmbild av THOMAS applista med THOMAS app som skapats av dispatch-verktyget](./media/luis-concept-enterprise/dispatch.png)

Chatbot tar emot utterance, och skickar sedan till överordnat THOMAS app för förutsägelse. Högsta förväntade avsikten från överordnad app avgör vilka underordnade THOMAS app anropas nästa. Chatbot skickar utterance till appen underordnade för en mer specifik förutsägelse.

Förstå hur den här hierarkin för anrop görs från Bot Builder v4 [dispatcher--självstudien][dispatcher-application-tutorial].  

### <a name="intent-limits-in-dispatch-model"></a>Avsiktshantering gränser i dispatch-modellen
En dispatch-programmet har 500 dispatch-källor, motsvarar 500 avsikter som maximum. 

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [testa en batch](luis-how-to-batch-test.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions
[dispatcher-application-tutorial]: https://aka.ms/bot-dispatch
[dispatch-tool]: https://github.com/Microsoft/botbuilder-tools/tree/master/Dispatch