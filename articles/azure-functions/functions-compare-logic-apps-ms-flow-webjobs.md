---
title: Vad är Microsoft Flow, Logic Apps, Functions och WebJobs? – Azure
description: 'Jämför Microsofts molntjänster som är optimerade för integreringsuppgifter: Microsoft Flow, Logic Apps, Functions och WebJobs.'
services: functions, logic-apps
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: microsoft flow, flow, logic apps, azure functions, functions, azure webjobs, webjobs, event processing, dynamic compute, serverless architecture
ms.service: azure-functions
ms.devlang: multiple
ms.topic: overview
ms.date: 04/09/2018
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: ea99c7fe9bc7fd8d6e4e26baa0afe45505949098
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2019
ms.locfileid: "58895655"
---
# <a name="what-are-microsoft-flow-logic-apps-functions-and-webjobs"></a>Vad är Microsoft Flow, Logic Apps, Functions och WebJobs?

Den här artikeln jämför följande Microsoft-molntjänster:

* [Microsoft Flow](https://flow.microsoft.com/)
* [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)
* [Azure Functions](https://azure.microsoft.com/services/functions/)
* [Azure App Service WebJobs](../app-service/webjobs-create.md)

Alla dessa tjänster kan lösa integrationsproblem och automatisera affärsprocesser. Samtliga tjänster definierar indata, åtgärder, villkor och utdata. Du kan välja att köra dem enligt ett schema eller med utlösare. Varje tjänst har unika fördelar, och den här artikeln förklarar skillnaderna.

## <a name="compare-microsoft-flow-and-azure-logic-apps"></a>Jämföra Microsoft Flow och Azure Logic Apps

Microsoft Flow och Logic Apps är båda integreringstjänster av typen *designer först* som kan skapa arbetsflöden. Båda tjänsterna integreras med olika SaaS- och företagsprogram. 

Microsoft Flow är byggt på Logic Apps. De delar samma arbetsflödesdesigner och samma [anslutningsappar](../connectors/apis-list.md). 

Microsoft Flow ger kontorspersonalen möjlighet att göra enkla integreringar (till exempel en godkännandeprocess i ett SharePoint-dokumentbibliotek) utan att behöva samråda med utvecklarna eller IT-avdelningen. Logic Apps kan även användas för att göra avancerade integreringar (till exempel B2B-processer) där Azure DevOps och säkerhetsåtgärder på företagsnivå krävs. Det är vanligt att företagsarbetsflöden växer i komplexitet med tiden. Därför går det bra att börja med ett enkelt flöde och sedan konvertera det till en logikapp efter behov.

Tabellen nedan hjälper dig att avgöra huruvida Microsoft Flow eller Logic Apps passar bäst för en specifik integrering:

|  | Microsoft Flow | Logic Apps |
| --- | --- | --- |
| Användare |Kontorsarbetarna, användare i verksamheten, SharePoint-administratörer |Professionella integratörer och utvecklare, IT-proffs |
| Scenarier |Självbetjäning |Avancerade integreringar |
| Designverktyg |I webbläsaren och mobilappen, endast användargränssnitt |I webbläsaren och [Visual Studio](../logic-apps/logic-apps-deploy-from-vs.md), [kodvyer](../logic-apps/logic-apps-author-definitions.md) är tillgängliga |
| Application Lifecycle Management (ALM) |Utforma och testa i icke-produktionsmiljöer, skicka till produktion när det är klart |Azure DevOps: källkontroll, testning, support, automatisering och hanterbarhet i [Azure Resource Manager](../logic-apps/logic-apps-create-deploy-azure-resource-manager-templates.md) |
| Administratörsupplevelse |Hantera Microsoft Flow-miljöer och principer för dataförlustskydd (DLP), spåra licensiering: [Administrationscenter för Microsoft Flow](https://admin.flow.microsoft.com) |Hantera resursgrupper, anslutningar, åtkomsthantering och loggning: [Azure Portal](https://portal.azure.com) |
| Säkerhet |Granskningsloggar för Office 365-säkerhet och efterlevnad, principer för dataförlustskydd, [kryptering i vila](https://wikipedia.org/wiki/Data_at_rest#Encryption) för känsliga data |Säkerhetskontroller i Azure: [Azure security](https://www.microsoft.com/en-us/trustcenter/Security/AzureSecurity), [Azure Security Center](https://azure.microsoft.com/services/security-center/) och [spårningsloggar](https://azure.microsoft.com/blog/azure-audit-logs-ux-refresh/) |

## <a name="compare-azure-functions-and-azure-logic-apps"></a>Jämföra Azure Functions och Azure Logic Apps

Functions och Logic Apps är Azure-tjänster som möjliggör serverlösa arbetsbelastningar. Azure Functions är en serverlös beräkningstjänst, medan Azure Logic Apps tillhandahåller serverlösa arbetsflöden. Båda kan skapa komplexa *orkestreringar*. En orkestrering är en samling funktioner eller steg, som kallas *åtgärder* som utförs i Logic Apps, som körs för att utföra en komplicerad uppgift. För att till exempel bearbeta en batch med ordrar kör du kanske flera instanser av en funktion parallellt, väntar tills alla instanser har slutförts och kör sedan en funktion som beräknar ett resultat för aggregatet.

Med Azure Functions utvecklar du orkestreringarna genom att skriva kod och använda [tillägget Varaktiga funktioner](durable/durable-functions-concepts.md). Med Logic Apps skapar du orkestreringarna genom att använda ett grafiskt användargränssnitt eller redigera konfigurationsfiler.

Du kan kombinera tjänsterna när du skapar en orkestrering och anropa funktioner från logikappar och logikappar från funktioner. Välj hur du skapar varje orkestrering utifrån tjänsternas funktioner eller vad du föredrar. I följande tabell visas några av de viktigaste skillnaderna mellan tjänsterna:
 
|  | Bestående funktioner | Logic Apps |
| --- | --- | --- |
| Utveckling | Kod först (imperativt) | Designer först (deklarativt) |
| Anslutning | [Ett dussintal inbyggda bindningstyper](functions-triggers-bindings.md#supported-bindings), skriv kod för anpassade bindningar | [Stor samling anslutningsprogram](../connectors/apis-list.md), [Enterprise-integrationspaket för B2B-scenarier](../logic-apps/logic-apps-enterprise-integration-overview.md), [skapa egna anslutningsprogram](../logic-apps/custom-connector-overview.md) |
| Åtgärder | Varje aktivitet är en Azure-funktion. Skriv kod för aktivitetsfunktioner |[Stor samling färdiga åtgärder](../logic-apps/logic-apps-workflow-actions-triggers.md)|
| Övervakning | [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) | [Azure-portalen](../logic-apps/quickstart-create-first-logic-app-workflow.md), [Azure Monitor-loggar](../logic-apps/logic-apps-monitor-your-logic-apps.md)|
| Hantering | [REST API](durable/durable-functions-http-api.md), [Visual Studio](https://docs.microsoft.com/azure/vs-azure-tools-resources-managing-with-cloud-explorer) | [Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md), [REST API](https://docs.microsoft.com/rest/api/logic/), [PowerShell](https://docs.microsoft.com/powershell/module/az.logicapp), [Visual Studio](https://docs.microsoft.com/azure/logic-apps/manage-logic-apps-with-visual-studio) |
| Körningskontext | Kan köras [lokalt](functions-runtime-overview.md) eller i molnet | Körs bara i molnet|

<a name="function"></a>

## <a name="compare-functions-and-webjobs"></a>Jämföra Functions och WebJobs

Liksom Azure Functions är Azure App Service WebJobs med WebJobs-SDK:n en *kodfokuserad* integreringstjänst som är avsedd för utvecklare. Båda bygger på [Azure App Service](../app-service/overview.md) och stöder funktioner som [källkontrollsintegration](../app-service/deploy-continuous-deployment.md), [autentisering](../app-service/overview-authentication-authorization.md) och [övervakning med Application Insights-integration](functions-monitoring.md).

### <a name="webjobs-and-the-webjobs-sdk"></a>WebJobs och WebJobs-SDK:n

Du kan använda *WebJobs*-funktionen i App Service för att köra ett skript eller kod i kontexten för en App Service-webbapp. *WebJobs-SDK:n* är ett ramverk som utformats för WebJobs och som förenklar den kod du skriver för att besvara händelser i Azure-tjänsterna. Till exempel kan du svara på skapandet av en avbildningsblob i Azure Storage genom att skapa en miniatyrbild. WebJobs-SDK:n körs som ett .NET-konsolprogram som du kan distribuera till ett WebJob. 

WebJobs och WebJobs-SDK:n fungerar bäst tillsammans, men du kan använda WebJobs utan WebJobs-SDK:n och vice versa. Ett WebJob kan köra vilket program eller skript som helst som körs i App Service-sandboxen. Ett WebJobs-SDK-konsolprogram kan köras var som helst där konsolprogram körs, t.ex. på lokala servrar.

### <a name="comparison-table"></a>Jämförelsetabell

Azure Functions bygger på WebJobs-SDK:n, och delar därför många händelseutlösare och anslutningsappar med andra Azure-tjänster. Här är några faktorer som du bör tänka på när du väljer mellan Azure Functions och WebJobs med WebJobs-SDK:n:

|  | Functions | WebJobs med Webjobs-SDK:n |
| --- | --- | --- |
|[Serverlös appmodell](https://azure.microsoft.com/solutions/serverless/) med [automatisk skalning](functions-scale.md#how-the-consumption-and-premium-plans-work)|✔||
|[Utveckla och testa i webbläsaren](functions-create-first-azure-function.md) |✔||
|[Priser för betala per användning](functions-scale.md#consumption-plan)|✔||
|[Integrering med Logic Apps](functions-twitter-email.md)|✔||
| Utlösarhändelser |[Timer](functions-bindings-timer.md)<br>[Azure Storage-köer och BLOB-objekt](functions-bindings-storage-blob.md)<br>[Azure Service Bus-köer och ämnen](functions-bindings-service-bus.md)<br>[Azure Cosmos DB](functions-bindings-cosmosdb.md)<br>[Azure Event Hubs](functions-bindings-event-hubs.md)<br>[HTTP/WebHook (GitHub, Slack)](functions-bindings-http-webhook.md)<br>[Azure Event Grid](functions-bindings-event-grid.md)|[Timer](functions-bindings-timer.md)<br>[Azure Storage-köer och BLOB-objekt](functions-bindings-storage-blob.md)<br>[Azure Service Bus-köer och ämnen](functions-bindings-service-bus.md)<br>[Azure Cosmos DB](functions-bindings-cosmosdb.md)<br>[Azure Event Hubs](functions-bindings-event-hubs.md)<br>[Filsystem](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Files/FileTriggerAttribute.cs)|
| Språk som stöds  |C#<br>F#<br>JavaScript<br>Java (förhandsversion) |C#<sup>1</sup>|
|Pakethanterare|NPM och NuGet|NuGet<sup>2</sup>|

<sup>1</sup> WebJobs (utan WebJobs-SDK:n) stöder C#, JavaScript, Bash, .cmd, .bat, PowerShell, PHP, TypeScript, Python med flera. Den här listan är inte omfattande. Ett WebJob kan köra vilket program eller skript som helst som kan köras i App Service-sandboxen.

<sup>2</sup> WebJobs (utan WebJobsSDK:n) stöder NPM och NuGet.

### <a name="summary"></a>Sammanfattning

Azure Functions ger mer produktivitet för utvecklare än Azure App Service WebJobs. Det har även fler alternativ för programmeringsspråk, utvecklingsmiljöer och Azure-tjänstintegrering och priser. I de flesta scenarierna är detta det bästa alternativet.

Här följer två scenarier för vilka WebJobs kan vara det bästa valet:

* Du behöver mer kontroll över den kod som lyssnar efter händelser, `JobHost`-objektet. Functions erbjuder ett begränsat antal olika sätt för att anpassa `JobHost`-beteendet i [host.json](functions-host-json.md)-filen. Ibland måste du behöva göra saker som inte kan anges av en sträng i en JSON-fil. Endast WebJobs-SDK:n låter dig konfigurera din egen återförsöksprincip för Azure Storage.
* Du har en App Service-app för vilken du vill köra kodavsnitt, och du vill hantera dem tillsammans i samma Azure DevOps-miljö.

När det gäller andra scenarier där du vill köra kodfragment för att integrera Azure eller tjänster från tredje part, kan du välja Azure Functions via WebJobs med WebJobs-SDK:n.

<a name="together"></a>

## <a name="microsoft-flow-logic-apps-functions-and-webjobs-together"></a>Microsoft Flow, Logic Apps, Functions och WebJobs tillsammans

Du behöver inte välja endast en av dessa tjänster. De integreras med varandra lika bra som med externa tjänster.

Ett flöde kan anropa en logikapp. En logikapp kan anropa en funktion, och en funktion kan anropa en logikapp. Läs t.ex. [Skapa en funktion som kan integreras med Azure Logic Apps](functions-twitter-email.md).

Integreringen mellan Microsoft Flow, Logic Apps och Functions förbättras ständigt. Det går bra att skapa något i en tjänst och sedan använda detsamma i de andra tjänsterna.

Det finns mer information om integreringstjänster via följande länkar:

* [Utnyttjar Azure Functions och Azure App Service i integreringsscenarier av Christopher Anderson](http://www.biztalk360.com/integrate-2016-resources/leveraging-azure-functions-azure-app-service-integration-scenarios/)
* [Artikel av Charles Lamanna integreringar](http://www.biztalk360.com/integrate-2016-resources/integrations-made-simple/)
* [Logic Apps Live-webbsändning](https://aka.ms/logicappslive)
* [Vanliga och frågor svar om Microsoft Flow](https://flow.microsoft.com/documentation/frequently-asked-questions/)

## <a name="next-steps"></a>Nästa steg

Kom igång genom att skapa ditt första flöde, din första logikapp eller din första funktionsapp. Välj någon av följande länkar:

* [Kom igång med Microsoft Flow](https://flow.microsoft.com/en-us/documentation/getting-started/)
* [Skapa en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Skapa din första Azure-funktion](functions-create-first-azure-function.md)
