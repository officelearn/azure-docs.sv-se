---
title: Jämföra Flow, Logic Apps, Functions och WebJobs – Azure
description: 'Jämför Microsofts molntjänster som är optimerade för integreringsuppgifter: Flow, Logic Apps, Functions och WebJobs.'
services: functions,app-service\logic
documentationcenter: na
author: tdykstra
manager: cfowler
tags: ''
keywords: microsoft flow, flow, logic apps, azure functions, functions, azure webjobs, webjobs, event processing, dynamic compute, serverless architecture
ms.service: functions
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/09/2018
ms.author: tdykstra
ms.custom: mvc
ms.openlocfilehash: 6df97a40be7bf1c437c5228006d114ace768f8ca
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
ms.locfileid: "32157381"
---
# <a name="compare-flow-logic-apps-functions-and-webjobs"></a>Jämföra Flow, Logic Apps, Functions och WebJobs

Den här artikeln jämför följande Microsoft-molntjänster:

* [Microsoft Flow](https://flow.microsoft.com/)
* [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)
* [Azure Functions](https://azure.microsoft.com/services/functions/)
* [Azure App Service WebJobs](../app-service/web-sites-create-web-jobs.md)

Alla dessa tjänster kan lösa integrationsproblem och automatisera affärsprocesser. Samtliga tjänster definierar indata, åtgärder, villkor och utdata. Du kan välja att köra dem enligt ett schema eller med utlösare. Men varje tjänst har unika fördelar, och den här artikeln förklarar skillnaderna.

## <a name="compare-microsoft-flow-and-azure-logic-apps"></a>Jämföra Microsoft Flow och Azure Logic Apps

Flow och Logic Apps är båda integreringstjänster av typen *designer först* som kan skapa arbetsflöden. Båda tjänsterna integreras med olika SaaS- och företagsprogram. 

Flow är byggt på Logic Apps. De delar samma arbetsflödesdesigner och samma [anslutningsappar](../connectors/apis-list.md). 

Flow ger kontorspersonalen möjlighet att göra enkla integreringar (till exempel en godkännandeprocess i ett SharePoint-dokumentbibliotek) utan att behöva samråda med utvecklarna eller IT-avdelningen. I kontrast till detta finns Logic Apps, som används för att göra avancerade integreringar (till exempel B2B-processer) där DevOps och säkerhetsåtgärder på företagsnivå krävs. Det är vanligt att företagsarbetsflöden växer i komplexitet med tiden. Därför går det bra att börja med ett enkelt flöde och sedan konvertera det till en logikapp efter behov.

Tabellen nedan hjälper dig att avgöra om Flow eller Logic Apps passar bäst för en specifik integration.

|  | Flöde | Logic Apps |
| --- | --- | --- |
| Användare |Kontorsarbetarna, användare i verksamheten, SharePoint-administratörer |Professionella integratörer och utvecklare, IT-proffs |
| Scenarier |Självbetjäning |Avancerade integreringar |
| Designverktyg |I webbläsaren och mobilappen, endast användargränssnitt |I webbläsaren och [Visual Studio](../logic-apps/logic-apps-deploy-from-vs.md), [kodvyer](../logic-apps/logic-apps-author-definitions.md) är tillgängliga |
| Application Lifecycle Management (ALM) |Utforma och testa i icke-produktionsmiljöer, skicka till produktion när du är klar. |DevOps: källkontroll, testning, support, automatisering och hanterbarhet i [Azure Resource Management](../logic-apps/logic-apps-create-deploy-azure-resource-manager-templates.md) |
| Administratörsupplevelse |Principer för att hantera Flow-miljöer och dataförlustskydd, spåra licensiering [https://admin.flow.microsoft.com](https://admin.flow.microsoft.com) |Hantera resursgrupper, anslutningar, åtkomsthantering och loggning [https://portal.azure.com](https://portal.azure.com) |
| Säkerhet |Granskningsloggar för Office 365-säkerhet och efterlevnad, dataförlustskydd, [kryptering i vila](https://wikipedia.org/wiki/Data_at_rest#Encryption) för känsliga data, o.s.v. |Säkerhetskontroller i Azure: [Azure-säkerhet](https://www.microsoft.com/trustcenter/Security/AzureSecurity), [Security Center](https://azure.microsoft.com/services/security-center/), [spårningsloggar](https://azure.microsoft.com/blog/azure-audit-logs-ux-refresh/) med mera. |

## <a name="compare-azure-functions-and-azure-logic-apps"></a>Jämföra Azure Functions och Azure Logic Apps

Functions och Logic Apps är Azure-tjänster som möjliggör serverlösa arbetsbelastningar. Azure Functions är en serverlös beräkningstjänst, medan Azure Logic Apps tillhandahåller serverlösa arbetsflöden. Komplexa *orkestreringarna* kan skapas med båda. En orkestrering är en samling funktioner eller steg, som kallas *åtgärder* som utförs i Logic Apps, som körs för att utföra en komplicerad uppgift. För att till exempel bearbeta en batch med order kanske du kör flera instanser av en funktion parallellt, väntar tills alla instanser har slutförts och kör sedan en funktion som beräknar ett totalt resultat.

Med Azure Functions utvecklar du orkestreringarna genom att skriva kod och använda [tillägget Varaktiga funktioner](durable-functions-overview.md) (i förhandsversion). Med Logic Apps skapar du orkestreringarna genom att använda ett grafiskt användargränssnitt eller redigera konfigurationsfiler.

Du kan kombinera tjänsterna när du skapar en orkestrering och anropa funktioner från logikappar och logikappar från funktioner. Välj hur du skapar varje orkestrering utifrån tjänsternas funktioner eller vad du föredrar. I följande tabell visas några av de viktigaste skillnaderna mellan tjänsterna:
 
|  | Bestående funktioner | Logic Apps |
| --- | --- | --- |
| Utveckling | Kod först (imperativt) | Designer först (deklarativt) |
| Anslutning | [Ett dussintal inbyggda bindningstyper](functions-triggers-bindings.md#supported-bindings), skriv kod för anpassade bindningar | [Stor samling anslutningsprogram](../connectors/apis-list.md), [Enterprise-integrationspaket för B2B-scenarier](../logic-apps/logic-apps-enterprise-integration-overview.md), [skapa egna anslutningsprogram](../logic-apps/custom-connector-overview.md) |
| Åtgärder | Varje aktivitet är en Azure-funktion. Skriv kod för aktivitetsfunktioner |[Stor samling färdiga åtgärder](../logic-apps/logic-apps-workflow-actions-triggers.md)|
| Övervakning | [Azure Application Insights](../application-insights/app-insights-overview.md) | [Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md), [Operations Management Suite](../logic-apps/logic-apps-monitor-your-logic-apps-oms.md), [Log Analytics](../logic-apps/logic-apps-monitor-your-logic-apps.md)|
| Hantering | [REST API](durable-functions-http-api.md), [Visual Studio](https://docs.microsoft.com/azure/vs-azure-tools-resources-managing-with-cloud-explorer) | [Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md), [REST API](https://docs.microsoft.com/rest/api/logic/), [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.logicapp/?view=azurermps-5.6.0), [Visual Studio](https://docs.microsoft.com/azure/logic-apps/manage-logic-apps-with-visual-studio) |
| Körningskontext | Kan köras [lokalt](functions-runtime-overview.md) eller i molnet. | Körs bara i molnet.|

<a name="function"></a>

## <a name="compare-functions-and-webjobs"></a>Jämföra Functions och WebJobs

Liksom Azure Functions är Azure App Service WebJobs med WebJobs-SDK:n en *kodfokuserad* integreringstjänst som är avsedd för utvecklare. Båda bygger på [Azure App Service](../app-service/app-service-web-overview.md) och stöder funktioner som [källkontrollsintegration](../app-service/app-service-continuous-deployment.md), [autentisering](../app-service/app-service-authentication-overview.md) och [övervakning med Application Insights-integration](functions-monitoring.md).

### <a name="webjobs-and-the-webjobs-sdk"></a>WebJobs och WebJobs-SDK:n

Med *WebJobs*-funktionen i App Service kan du köra ett skript eller kod i kontexten för en App Service-webbapp. *WebJobs-SDK:n* är ett ramverk som utformats för WebJobs och som förenklar den kod du skriver för att besvara händelser i Azure-tjänsterna. Du kan t.ex. svara på skapandet av en avbildningsblob i Azure Storage genom att skapa en miniatyrbild. WebJobs-SDK:n körs som ett .NET-konsolprogram som du kan distribuera till ett WebJob. 

WebJobs och WebJobs-SDK:n fungerar bäst tillsammans, men du kan använda WebJobs utan WebJobs-SDK:n och vice versa. Ett WebJob kan köra vilket program eller skript som helst som körs i App Service-sandboxen. Ett WebJobs-SDK-konsolprogram kan köras var som helst där konsolprogram körs, t.ex. på lokala servrar.

### <a name="comparison-table"></a>Jämförelsetabell

Azure Functions bygger på WebJobs-SDK:n, och delar därför många händelseutlösare och anslutningsappar med andra Azure-tjänster. Här är några faktorer du bör tänka på när du väljer mellan Azure Functions och WebJobs med WebJobs-SDK:n:

|  | Funktioner | WebJobs med Webjobs-SDK:n |
| --- | --- | --- |
|[Serverlös appmodell](https://azure.microsoft.com/overview/serverless-computing/) med [automatisk skalning](functions-scale.md#how-the-consumption-plan-works)|✔||
|[Utveckla och testa i webbläsaren](functions-create-first-azure-function.md) |✔||
|[Betala per användning](functions-scale.md#consumption-plan)|✔||
|[Integrering med Logic Apps](functions-twitter-email.md)|✔||
| Utlösarhändelser |[Timer](functions-bindings-timer.md)<br>[Azure Storage-köer och blobar](functions-bindings-storage-blob.md)<br>[Azure Service Bus-köer och ämnen](functions-bindings-service-bus.md)<br>[Azure Cosmos DB](functions-bindings-cosmosdb.md)<br>[Azure Event Hubs](functions-bindings-event-hubs.md)<br>[HTTP/WebHook (GitHub, Slack)](functions-bindings-http-webhook.md)<br>[Azure Event Grid](functions-bindings-event-grid.md)|[Timer](functions-bindings-timer.md)<br>[Azure Storage-köer och blobar](functions-bindings-storage-blob.md)<br>[Azure Service Bus-köer och ämnen](functions-bindings-service-bus.md)<br>[Azure Cosmos DB](functions-bindings-cosmosdb.md)<br>[Azure Event Hubs](functions-bindings-event-hubs.md)<br>[Filsystem](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Files/FileTriggerAttribute.cs)|
| Språk som stöds  |C#<br>F#<br>JavaScript<br>Java (förhandsversion) |C#<sup>1</sup>|
|Pakethanterare|NPM och NuGet|NuGet<sup>2</sup>|

<sup>1</sup> WebJobs (utan WebJobs-SDK:n) stöder C#, JavaScript, Bash, .cmd, .bat, PowerShell, PHP, TypeScript, Python och med flera. Detta är inte en komplett lista. Ett WebJob kan köra vilket program eller skript som helst som kan köras i App Service-sandboxen.

<sup>2</sup> WebJobs (utan WebJobsSDK:n) stöder NPM och NuGet.

### <a name="summary"></a>Sammanfattning

Azure Functions erbjuder större utvecklarproduktivitet, fler alternativ för programmeringsspråk, fler utvecklingsmiljöalternativ, fler alternativ för Azure-tjänstintegration och fler prissättningsalternativ. I de flesta scenarierna är detta det bästa alternativet.

Här följer två scenarier för vilka WebJobs kan vara det bästa valet:

* Du behöver mer kontroll över den kod som lyssnar efter händelser, `JobHost`-objektet. Functions erbjuder ett begränsat antal olika sätt för att anpassa `JobHost`-beteendet i [host.json](functions-host-json.md)-filen. Ibland måste du behöva göra saker som inte kan anges av en sträng i en JSON-fil. Endast WebJobs-SDK:n låter dig konfigurera din egen återförsöksprincip för Azure Storage.
* Du har en App Service-app för vilken du vill köra kodavsnitt, och vill hantera dem tillsammans i samma DevOps-miljö.

När det gäller andra scenarier där du vill köra kodfragment för att integrera Azure eller tjänster från tredje part, kan du välja Azure Functions via WebJobs med WebJobs-SDK:n.

<a name="together"></a>

## <a name="flow-logic-apps-functions-and-webjobs-together"></a>Flow, Logic Apps, Functions och WebJobs tillsammans

Du behöver välja endast en av dessa tjänster De kan integreras med varandra, liksom med externa tjänster.

Ett flöde kan anropa en logikapp. En logikapp kan anropa en funktion, och en funktion kan anropa en logikapp. Läs t.ex. [Skapa en funktion som kan integreras med Azure Logic Apps](functions-twitter-email.md).

Integreringen mellan Flow, Logic Apps och Functions förbättras ständigt. Det går bra att skapa något i en tjänst och sedan använda detsamma i de andra tjänsterna.

## <a name="next-steps"></a>Nästa steg

Kom igång genom att skapa ditt första flöde, din första logikapp eller din första funktionsapp. Klicka på någon av följande länkar:

* [Kom igång med Microsoft Flow](https://flow.microsoft.com/en-us/documentation/getting-started/)
* [Skapa en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Skapa din första Azure-funktion](functions-create-first-azure-function.md)

Du hittar mer information om dessa integreringstjänster via följande länkar:

* [Artikel av Christopher Anderson om hur man utnyttjar Azure Functions och Azure App Service i integreringsscenarier](http://www.biztalk360.com/integrate-2016-resources/leveraging-azure-functions-azure-app-service-integration-scenarios/)
* [Artikel av Charles Lamanna om hur man förenklar integreringar](http://www.biztalk360.com/integrate-2016-resources/integrations-made-simple/)
* [Webbsändning om Logic Apps](http://aka.ms/logicappslive)
* [Vanliga frågor och svar om Microsoft Flow](https://flow.microsoft.com/documentation/frequently-asked-questions/)
