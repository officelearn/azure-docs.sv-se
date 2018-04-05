---
title: Välj mellan Flow, Logic Apps, Functions och WebJobs | Microsoft Docs
description: Jämför Microsofts integreringstjänster så blir det lättare att fatta beslut om vilka tjänster du ska använda.
services: functions,app-service\logic
documentationcenter: na
author: tdykstra
manager: wpickett
tags: ''
keywords: microsoft flow, flow, logic apps, azure functions, functions, azure webjobs, webjobs, event processing, dynamic compute, serverless architecture
ms.service: functions
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/20/2018
ms.author: tdykstra
ms.custom: mvc
ms.openlocfilehash: 577031c58e95781dc97721acc71fb22114b1c606
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="choose-between-flow-logic-apps-functions-and-webjobs"></a>Välj mellan Flow, Logic Apps, Functions och WebJobs

I den här artikeln jämförs och kontrasteras följande tjänster i Microsoft-molnet:

* [Microsoft Flow](https://flow.microsoft.com/)
* [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)
* [Azure Functions](https://azure.microsoft.com/services/functions/)
* [Azure App Service WebJobs](../app-service/web-sites-create-web-jobs.md)

Alla dessa tjänster kan lösa integrationsproblem och automatisera affärsprocesser. Samtliga tjänster definierar indata, åtgärder, villkor och utdata. Du kan välja att köra dem enligt ett schema eller med utlösare. Varje tjänst har dock unika fördelar, och den här artikeln förklarar skillnaderna.

## <a name="flow-vs-logic-apps"></a>Flow jämfört med Logic Apps

Microsoft Flow och Azure Logic Apps är båda integrationstjänster där *konfigurationen kommer först*. Båda skapar arbetsflöden som integreras med olika SaaS- och företagsprogram. 

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

<a name="function"></a>

## <a name="functions-vs-webjobs"></a>Functions jämfört med Webbjobb

Liksom Azure Functions är Azure App Service WebJobs med WebJobs-SDK:n en *kodfokuserad* integreringstjänst som är avsedd för utvecklare. Båda bygger på [Azure App Service](../app-service/app-service-web-overview.md) och stöder funktioner som [källkontrollsintegration](../app-service/app-service-continuous-deployment.md), [autentisering](../app-service/app-service-authentication-overview.md) och [övervakning med Application Insights-integration](functions-monitoring.md).

### <a name="webjobs-vs-the-webjobs-sdk"></a>WebJobs jämfört med WebJobs-SDK:n

Med *WebJobs*-funktionen i App Service kan du köra ett skript eller kod i kontexten för en App Service-webbapp. *WebJobs-SDK:n* är ett ramverk som utformats för WebJobs och som förenklar den kod du skriver för att besvara händelser i Azure-tjänsterna. Du kan t.ex. svara på skapandet av en avbildningsblob i Azure Storage genom att skapa en miniatyrbild. WebJobs-SDK:n körs som ett .NET-konsolprogram som du kan distribuera till ett WebJob. 

WebJobs och WebJobs-SDK:n fungerar bäst tillsammans, men du kan använda WebJobs utan WebJobs-SDK:n och vice versa. Ett WebJob kan köra vilket program eller skript som helst som kan köras i App Service-sandboxen. Ett WebJobs-SDK-konsolprogram kan köras var som helst där konsolprogram kan köras, t.ex. på lokala servrar.

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
