---
title: Använda NLP Cognitive Services för att berika konversationer
titleSuffix: Azure Cognitive Services
description: Cognitive Services tillhandahåller två tjänster för bearbetning av naturligt språk, Språkförståelse och QnA Maker, var och en med ett annat syfte. Förstå när du ska använda varje tjänst och hur de komplimang varandra.
author: diberry
ms.author: diberry
manager: nitinme
ms.topic: conceptual
ms.service: cognitive-services
ms.date: 08/01/2019
ms.openlocfilehash: 32159b37d3d1a8609181d81dc1a73f27177adb85
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "73818204"
---
# <a name="use-cognitive-services-with-natural-language-processing-nlp-to-enrich-bot-conversations"></a>Använd Cognitive Services med bearbetning av naturligt språk (NLP) för att berika botkonversationer

Cognitive Services tillhandahåller två tjänster för bearbetning av naturligt språk, [Språkförståelse](what-is-luis.md) och [QnA Maker](../qnamaker/overview/overview.md), var och en med ett annat syfte. Förstå när du ska använda varje tjänst och hur de komplimang varandra. 

Med nlp (Natural language processing) kan klientprogrammet, till exempel en chattrobot, arbeta med användarna med hjälp av naturligt språk. En användare anger en mening eller fras. Användarens text kan ha dålig grammatik, stavning och interpunktion. Den kognitiva tjänsten kan arbeta igenom användaren meningen ändå, returnera information chattroboten behöver för att hjälpa användaren. 

## <a name="cognitive-services-with-nlp"></a>Kognitiva tjänster med NLP

Språkförståelse (LUIS) och QnA Maker tillhandahåller NLP. Klientansökan skickar text på naturligt språk. Tjänsten tar texten, bearbetar den och returnerar ett resultat. 

## <a name="when-to-use-each-service"></a>När ska varje tjänst användas

Språk understanding (LUIS) och QnA Maker löser olika problem. LUIS bestämmer avsikten med en användares text (kallas ett uttryck), medan QnA Maker bestämmer svaret på en användares text (kallas en fråga). 

För att kunna välja rätt tjänst måste du förstå användartexten som kommer från klientprogrammet och vilken information klientprogrammet behöver få från Cognitive Service.

Om chattroboten `How do I get to the Human Resources building on the Seattle North campus?`tar emot texten använder du diagrammet nedan för att förstå hur varje tjänst fungerar med texten.

|Tjänst|Klientprogrammet bestämmer|
|--|--|
|LUIS|**Bestämmer användarens avsikt med** text - tjänsten returnerar inte svaret på frågan. Den här texten klassificeras till `FindLocation` exempel som matchande avsikt.<br>|
|QnA Maker|**Returnerar svaret på frågan** från en anpassad kunskapsbas. Den här texten bestäms till exempel som en `Get on the #9 bus and get off at Franklin street`fråga med det statiska textsvaret i .|
|||

## <a name="when-do-you-use-luis"></a>När använder du LUIS? 

Använd LUIS när du behöver veta avsikten med yttrandet som en del av en process i chattroboten. `How do I get to the Human Resources building on the Seattle North campus?`När du väl vet att användaren har för avsikt att hitta en plats kan du skicka information om uttrycket (utdraget med entiteter) till en annan tjänst, till exempel en transportserver, för att få svaret. 

Du behöver inte kombinera LUIS och QnA Maker för att avgöra avsikt. 

Du kan kombinera de två tjänsterna för det här uttrycket, om chattroboten behöver bearbeta texten baserat på avsikter och entiteter (med LUIS) samt hitta det specifika statiska textsvaret (med QnA Maker).

## <a name="when-do-you-use-qna-maker"></a>När använder du QnA Maker? 

Använd QnA Maker när du har en statisk kunskapsbas med svar. Den här kunskapsbasen är anpassad till dina behov, som du har skapat med dokument som PDF-filer och webbadresser. 

Om du fortsätter med `How do I get to the Human Resources building on the Seattle North campus?`exempelutlysningen skickar du texten, som en fråga, till den publicerade QnA Maker-tjänsten och får det bästa svaret. 

Du behöver inte kombinera LUIS och QnA Maker för att avgöra svaret på frågan.

Du kan kombinera de två tjänsterna för det här uttrycket, om chattroboten behöver bearbeta texten baserat på avsikter och entiteter (med LUIS) samt hitta svaret (med QnA Maker).

## <a name="use-both-services-when-your-knowledge-base-is-incomplete"></a>Använd båda tjänsterna när din kunskapsbas är ofullständig

Om du bygger din QnA Maker-kunskapsbas men vet att ämnesdomänen håller på att förändras (t.ex. aktuell information) kan du kombinera LUIS- och QnA Maker-tjänster. På så sätt kan du använda informationen i kunskapsbasen men även använda LUIS för att avgöra en användares avsikt. När klientprogrammet har för avsikten kan det begära relevant information från en annan källa. 

Klientprogrammet måste övervaka både LUIS- och QnA Maker-svaren för poäng. Om poängen från QnA Maker ligger under ett godtyckligt tröskelvärde använder du avsikts- och entitetsinformationen som returneras från LUIS för att vidarebefordra informationen till en tjänst från tredje part.

Om du fortsätter `How do I get to the Human Resources building on the Seattle North campus?`med exempeltexten antar du att QnA Maker returnerar en låg konfidenspoäng. Använd avsikten som returneras från LUIS `FindLocation` och alla `Human Resources building` `Seattle North campus`extraherade entiteter, till exempel och , för att skicka den här informationen till en mappnings- eller söktjänst för ett annat svar. 

Du kan presentera svaret från tredje part för användaren för validering. När du har användarens godkännande kan du gå tillbaka till QnA Maker för att lägga till informationen för att öka din kunskap. 

## <a name="use-both-services-when-your-chat-bot-needs-more-information"></a>Använd båda tjänsterna när din chattrobot behöver mer information

Om din chattrobot behöver mer information än någon av tjänsterna tillhandahåller använder du både tjänster och bearbetar båda svaren i klientprogrammet om du vill fortsätta genom ett beslutsträd. 

Använd Bot framework **[Dispatch CLI-verktyget](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch)** för att skapa en process för att arbeta med båda tjänsterna. Det här verktyget bygger en topp LUIS-app med avsikter som skickar mellan LUIS och QnA Maker som underordnade appar. 

Använd exempel på Bot builder, **NLP med leverans**, i [C#](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/14.nlp-with-dispatch) eller [Node.js](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs/14.nlp-with-dispatch), för att implementera den här typen av chattrobot. 

## <a name="best-practices"></a>Bästa praxis

Implementera metodtips för varje tjänst:

* [BÄSTA](luis-concept-best-practices.md) METODER FÖR LUIS
* Bästa praxis för [QnA Maker](../qnamaker/concepts/best-practices.md)

## <a name="see-also"></a>Se även

* [Språk understanding (LUIS)](what-is-luis.md)
* [QnA Maker](../qnamaker/overview/overview.md)
* [Skicka CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch)
* [Exempel på robotarram](https://github.com/Microsoft/BotBuilder-Samples)
* [Azure bot-tjänst](https://docs.microsoft.com/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0)
* [Azure bot emulator](https://github.com/Microsoft/BotFramework-Emulator)
* [Bot ram webbchatt](https://github.com/microsoft/BotFramework-WebChat)