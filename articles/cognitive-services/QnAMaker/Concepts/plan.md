---
title: Planera din app-QnA Maker
description: Planera QnA Maker-appen måste förstå hur QnA Maker fungerar och samverkar med andra Azure-tjänster samt vissa kunskaps bas begrepp.
ms.topic: conceptual
ms.date: 07/2/2020
ms.openlocfilehash: d19ec51aec7e71b6f040a03543f72af3aed09556
ms.sourcegitcommit: 9b5c20fb5e904684dc6dd9059d62429b52cb39bc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85875719"
---
# <a name="plan-your-qna-maker-app"></a>Planera QnA Maker-appen

Planera QnA Maker-appen måste förstå hur QnA Maker fungerar och samverkar med andra Azure-tjänster samt vissa kunskaps bas begrepp.

## <a name="azure-resources"></a>Azure-resurser

Varje [Azure-resurs](azure-resources.md#resource-purposes) som skapats med QNA Maker har ett specifikt syfte. Eftersom varje resurs har egna syften, gränser och [pris nivå](azure-resources.md#pricing-tier-considerations)är det viktigt att förstå hur dessa resurser fungerar som en del av planerings processen.

|Resurs|Syfte|
|--|--|
| [QNA Maker](azure-resources.md#qna-maker-resource) resurs|Redigering och fråga förutsägelse|
| [Kognitiv sökning](azure-resources.md#cognitive-search-resource) resurs|Data lagring och sökning|
| Resurs för [resurs-och app-plan för App Service](azure-resources.md#app-service-and-app-service-plan)|Slutpunkt för frågeförutsägelse|
| [Application Insights](azure-resources.md#application-insights) resurs|Telemetri för fråga förutsägelse|

### <a name="resource-planning"></a>Resurs planering

När du lär dig att redigera och fråga förutsägelse, använder du den kostnads fria nivån, `F0` för varje resurs och ger både redigering och frågans förutsägelse. När du flyttar till en produktion, ett Live-scenario, utvärderar du ditt resurs val igen.

#### <a name="qna-maker-resource"></a>QnA Maker resurs

En enskild QnA Maker-resurs kan vara värd för fler än en kunskaps bas. Antalet kunskaps baser bestäms av den Kognitiv sökning pris nivåns antal index som stöds. Lär dig mer om [förhållandet mellan index och kunskaps baser](azure-resources.md#index-usage).

#### <a name="knowledge-base-size-and-throughput"></a>Kunskaps bas storlek och data flöde

När du planerar att bygga en riktig app bör du planera dina resurser för den stora kunskaps basen och de begär Anden för frågor som du förväntar dig.

En kunskaps bas storlek styrs av:
* Gränser för [kognitiv sökning resurs](../../../search/search-limits-quotas-capacity.md) pris nivå
* [QnA Maker gränser](../limits.md)

Frågan förutsägelse förfrågan för kunskaps basen styrs av webbappens plan och webbapp. Se de [rekommenderade inställningarna](azure-resources.md#recommended-settings) för att planera pris nivån.

### <a name="resource-sharing"></a>Resurs delning

Om du redan har några av de här resurserna som används kan du överväga att dela resurser. Vissa resurser [kan delas](azure-resources.md#share-services-with-qna-maker), men det här är ett avancerat scenario.

Alla kunskaps baser som skapats i samma QnA Maker resurs delar samma slut punkt för **test** frågans förutsägelse.

### <a name="understanding-impact-of-resource-selection"></a>Förstå effekten av resurs val

Med rätt resurs val menas din kunskaps bas svar på frågan förutsägelse.

Om din kunskaps bas inte fungerar som den ska, vanligt vis är problemet felaktig resurs hantering.

Felaktig resurs val kräver undersökning för att avgöra vilken [resurs som behöver ändras](azure-resources.md#when-to-change-a-pricing-tier).

## <a name="knowledge-bases"></a>Kunskaps banker

En kunskaps bas är direkt knuten till sin QnA Maker-resurs och innehåller de frågor och svar (QnA) som används för att besvara förfrågningar om fråga förutsägelse.

### <a name="language-considerations"></a>Språk överväganden

Den första kunskaps basen som skapas på QnA Maker-resursen anger språket för resursen. Du kan bara ha ett språk för en QnA Maker-resurs.

Strukturera QnA Maker resurser efter språk eller Använd [Translator](../../translator/translator-info-overview.md) för att ändra en fråga från ett annat språk till kunskaps bas språket innan du skickar frågan till slut punkten för frågans förutsägelse.

### <a name="ingesting-data-sources"></a>Mata in data källor

Inmatade [data källor](knowledge-base.md)som används för att skapa en kunskaps bas kan vara något av följande:

* Offentlig URL
* Privat SharePoint-URL
* Fil

Vid inmatnings processen konverteras [innehålls typer som stöds](content-types.md) till markdown. All ytterligare redigering av *svaret* görs med markdown. När du har skapat din kunskaps bas kan du redigera [QNA-par](question-answer-set.md) i QNA Maker Portal med [RTF-redigering](../how-to/edit-knowledge-base.md#rich-text-editing-for-answer).

### <a name="data-format-considerations"></a>Överväganden för data format

Eftersom det slutliga formatet för ett QnA-par är markdown, är det viktigt att förstå [markdown-supporten](../reference-markdown-format.md) .

Länkade bilder måste vara tillgängliga från en offentlig URL för att kunna visas i test fönstret på QnA Maker-portalen samt i alla klient program eftersom QnA Maker inte tillhandahåller autentisering för innehåll, inklusive bilder.

### <a name="bot-personality"></a>Bot-personlighet

Lägg till en bot-personlighet till din kunskaps bas med en [CHI2TEST-chatt](../how-to/chit-chat-knowledge-base.md). Den här informen går igenom med svar som finns i en viss konversations ton, till exempel *professionella* och *egna*. Den här CHI2TEST tillhandahålls som en konversations uppsättning, som du har total kontroll för att lägga till, redigera och ta bort.

En bot-personlighet rekommenderas om din robot ansluter till din kunskaps bas. Om du ansluter till flera tjänster, varav en är din kunskaps bas, kan du välja att fortsätta använda CHI2TEST i din kunskaps bas, men du bör gå igenom hur bot-tjänsten interagerar för att veta om det är rätt arkitektur design för din användning.

### <a name="conversation-flow-with-a-knowledge-base"></a>Konversations flöde med en kunskaps bas

Konversations flödet börjar vanligt vis med en hälsning från en användare, till exempel `Hi` eller `Hello` . Din kunskaps bas kan svara med ett allmänt svar, till exempel `Hi, how can I help you` , och det kan också innehålla ett urval av uppföljnings anvisningar för att fortsätta konversationen.

Du bör utforma ditt konversations flöde med en loop i åtanke så att en användare vet hur du använder din robot och inte överges av roboten i konversationen. [Uppföljnings instruktioner](../how-to/multiturn-conversation.md) ger länkar mellan QNA-par som tillåter konversations flödet.

### <a name="authoring-with-collaborators"></a>Redigera med medarbetare

Medarbetare kan vara andra utvecklare som delar den fullständiga utvecklings stacken i kunskaps bas programmet eller kan vara begränsade till att bara redigera kunskaps basen.

Redigering av kunskaps bas har stöd för flera [rollbaserade åtkomst behörigheter](../how-to/collaborate-knowledge-base.md) som du använder i Azure Portal för att begränsa omfattningen för en medarbetares förmågor.

## <a name="integration-with-client-applications"></a>Integrering med klient program

Integrering med [klient program](integration-with-other-applications.md) innebär att skicka en fråga till körnings slut punkten för förutsägelse. En fråga skickas till din speciella kunskaps bas med en SDK-eller REST-baserad begäran till din QnA Makers webb programs slut punkt.

För att klienten ska kunna autentiseras korrekt måste klient programmet skicka rätt autentiseringsuppgifter och ID för kunskaps bas. Om du använder en Azure Bot Service konfigurerar du inställningen som en del av robot konfigurationen i Azure Portal.

### <a name="conversation-flow-in-a-client-application"></a>Konversations flöde i ett klient program

Konversations flödet i ett [klient program](integration-with-other-applications.md), t. ex. en Azure-robot, kan kräva funktioner innan och efter att du har interagerat i kunskaps basen.

Om ditt klient program stöder konversations flöde, antingen med alternativa metoder för att hantera uppföljnings-eller CHI2TEST, utformar du dessa tidiga och kontrollerar att frågan som använder i klient programmet hanteras korrekt, antingen av en annan tjänst eller skickas till din kunskaps bas.

### <a name="dispatching-between-qna-maker-and-language-understanding-luis"></a>Sändning mellan QnA Maker och Language Understanding (LUIS)

Ett klient program kan tillhandahålla flera funktioner, bara en som besvaras av en kunskaps bas. Andra funktioner behöver fortfarande förstå konversations texten och extrahera innebörd från den.

En vanlig klient program arkitektur är att använda både QnA Maker och [language Understanding (Luis)](../../LUIS/what-is-luis.md) tillsammans. LUIS tillhandahåller text klassificering och extrahering för alla frågor, inklusive andra tjänster, medan QnA Maker ger svar från din kunskaps bas.

I en [delad arkitektur](../choose-natural-language-processing-service.md)görs [sändningen](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch) mellan de två tjänsterna med verktyget Dispatch från bot Framework.

### <a name="active-learning-from-a-client-application"></a>Aktiv inlärning från ett klient program

QnA Maker använder _Active Learning_ för att förbättra din kunskaps bas genom att föreslå alternativa frågor till ett svar. Klient programmet ansvarar för en del av den här [aktiva inlärningen](active-learning-suggestions.md). Klient programmet, via konversations frågor, kan avgöra att svaret som returnerades från kunskaps basen inte var det svar som användaren letade efter och fastställ det bättre svaret. Klient programmet måste [skicka tillbaka informationen till kunskaps basen](active-learning-suggestions.md#how-you-give-explicit-feedback-with-the-train-api) för att förbättra förutsägelse kvaliteten.

### <a name="providing-a-default-answer"></a>Ange ett standard svar

Om din kunskaps bas inte hittar något svar, returneras _standard svaret_. Det här svaret kan konfigureras från QnA Maker Portal, på sidan **Inställningar** eller [API: erna](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update#request-body).

Det här standardsvaret skiljer sig från standard svaret för Azure bot. Standard svaret för Azure-roboten konfigureras i Azure Portal för din robot som en del av konfigurations inställningarna och returneras när Poäng tröskelvärdet inte är uppfyllt.

## <a name="prediction"></a>Förutsägelse

Förutsägelsen är svaret från din kunskaps bas och innehåller mer information än bara svaret. Använd [GeneateAnswer-API: et](query-knowledge-base.md)för att få svar på en fråga som förutsäger.

### <a name="prediction-score-fluctuations"></a>Förutsägelse Poäng

Poängen kan ändras baserat på flera faktorer:

* Antal svar som du begärt som svar på [GenerateAnswer](query-knowledge-base.md) med `top` egenskapen
* Utbud av tillgängliga alternativa frågor
* Filtrering för metadata
* Fråga som skickats till `test` eller `production` kunskaps bas

Det finns en [svars rangordning i två steg](query-knowledge-base.md#how-qna-maker-processes-a-user-query-to-select-the-best-answer):
* Kognitiv sökning-första rangordning – för att ett svar ska returneras från Kognitiv sökning måste antalet _svar_ vara tillräckligt högt för att de bästa svaren returneras av kognitiv sökning så att de kan skickas till QNA Maker rangen
* QnA Maker – andra rang – Använd funktionalisering och Machine Learning för att fastställa bästa möjliga svar.

### <a name="service-updates"></a>Tjänstuppdateringar

Tjänst uppdateringar hanteras automatiskt genom att de [senaste körnings uppdateringarna](../how-to/set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates)tillämpas.

### <a name="scaling-throughput-and-resiliency"></a>Skalning, data flöde och återhämtning

Skalning, data flöde och återhämtning bestäms av [Azure-resurser](../how-to/set-up-qnamaker-service-azure.md), deras pris nivåer och alla omgivande arkitektur, till exempel [Traffic Manager](../how-to/set-up-qnamaker-service-azure.md#business-continuity-with-traffic-manager).

### <a name="analytics-with-application-insights"></a>Analys med Application Insights

Alla frågor till kunskaps basen lagras i Application Insights. Använd våra [vanligaste frågor](../how-to/get-analytics-knowledge-base.md) för att förstå dina mått.

## <a name="development-lifecycle"></a>Utvecklingscykel

[Utvecklings livs cykeln](development-lifecycle-knowledge-base.md) för en kunskaps bas pågår: redigera, testa och publicera din kunskaps bas.

### <a name="knowledge-base-development-of-qna-maker-pairs"></a>Kunskaps bas utveckling av QnA Maker par

Dina [QNA-par](question-answer-set.md) bör utformas och utvecklas baserat på användningen av klient programmet.

Varje par kan innehålla:
* Metadata – filtrerat vid frågor. På så sätt kan du tagga dina QnA-par med ytterligare information om källa, innehåll, format och syfte för dina data.
* Uppföljnings frågor – avgör en sökväg genom kunskaps basen så att användaren kommer till rätt svar.
* Alternativa frågor – alternativa frågor är viktiga för att låta sökningen matcha svar från en rad olika former av frågan. [Aktiva utbildnings förslag](active-learning-suggestions.md) gör alternativa frågor.

### <a name="devops-development"></a>DevOps-utveckling

Att utveckla en kunskaps bas som ska infogas i en DevOps-pipeline kräver att kunskaps basen isoleras under [batch-testning](../quickstarts/batch-testing.md).

En kunskaps bas delar Kognitiv sökning indexet med alla andra kunskaps baser på den QnA Maker resursen. När kunskaps basen isoleras av en partition kan delning av index orsaka en skillnad i poängen jämfört med den publicerade kunskaps basen.

För att få _samma resultat_ i `test` och `production` kunskaps baser, isolera en QNA Maker resurs till en enda kunskaps bas. I den här arkitekturen behöver resursen bara leva till längden på det isolerade batch-testet.

## <a name="next-step"></a>Nästa steg

* [Azure-resurser](../how-to/set-up-qnamaker-service-azure.md)
* [Frågor och svar-par](question-answer-set.md)