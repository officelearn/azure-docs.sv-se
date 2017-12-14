---
title: "Välj mellan Flow, Logic Apps, Functions och WebJobs | Microsoft Docs"
description: "Jämför Microsofts integreringstjänster så blir det lättare att fatta beslut om vilka tjänster du ska använda."
services: functions,app-service\logic
documentationcenter: na
author: ggailey777
manager: wpickett
tags: 
keywords: microsoft flow, flow, logic apps, azure functions, functions, azure webjobs, webjobs, event processing, dynamic compute, serverless architecture
ms.assetid: e9ccf7ad-efc4-41af-b9d3-584957b1515d
ms.service: functions
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/03/2017
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 03246846484878f7155449ad11b009aeffe8a576
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2017
---
# <a name="choose-between-flow-logic-apps-functions-and-webjobs"></a>Välj mellan Flow, Logic Apps, Functions och WebJobs
I den här artikeln jämförs molntjänster från Microsoft. Samtliga löser problem med integrering och automatisering av affärsprocesser:

* [Microsoft Flow](https://flow.microsoft.com/)
* [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)
* [Azure Functions](https://azure.microsoft.com/services/functions/)
* [Azure App Service WebJobs](../app-service/web-sites-create-web-jobs.md)

Samtliga tjänster kan komma till nytta när man ska ”limma ihop” vitt skilda system. Samtliga tjänster definierar indata, åtgärder, villkor och utdata. Du kan välja att köra dem enligt ett schema eller med utlösare. Varje tjänst har sina unika fördelar och en jämförelse handlar inte om vilken tjänst som är bäst utan vilken tjänst som passar bäst för den givna situationen. En kombination av dessa tjänster är ofta bäst när man snabbt ska skapa en komplett och skalbar integrationslösning.

<a name="flow"></a>

## <a name="flow-vs-logic-apps"></a>Flow jämfört med Logic Apps
Det finns en poäng i att presentera Microsoft Flow och Azure Logic Apps tillsammans eftersom båda är integreringstjänster där *konfigurationen hamnar i fokus*. De gör det enkelt att skapa processer och arbetsflöden och de kan också integreras med olika SaaS- och företagsprogram. 

* Flow bygger på Logic Apps
* De har samma arbetsflödesdesigner
* [Anslutningar](../connectors/apis-list.md) som fungerar i en tjänst fungerar ibland även i den andra

Flow ger kontorspersonalen möjlighet att göra enkla integreringar (till exempel skicka ut SMS om viktiga e-postmeddelanden) utan att behöva samråda med utvecklarna eller IT-avdelningen. I kontrast till detta finns Logic Apps, som används för att göra avancerade eller verksamhetskritiska integreringar (till exempel B2B-processer) där DevOps och säkerhetsåtgärder på företagsnivå krävs. Det är vanligt att arbetsflöden inom företagen växer i komplexitet över tid. Därför går det bra att börja med ett enkelt flöde och sedan konvertera det till en logikapp efter behov.

Tabellen nedan hjälper dig att avgöra om Flow eller Logic Apps passar bäst för en specifik integration.

|  | Flöde | Logic Apps |
| --- | --- | --- |
| Målgrupp |Kontorspersonal, företagsanvändare |IT-proffs, utvecklare |
| Scenarier |Självbetjäning |Verksamhetskritiskt |
| Designverktyg |I webbläsaren och mobilappen, endast användargränssnitt |I webbläsaren och [Visual Studio](../logic-apps/logic-apps-deploy-from-vs.md), [kodvyer](../logic-apps/logic-apps-author-definitions.md) är tillgängliga |
| DevOps |Ad hoc, utveckling under pågående produktion |källkontroll, testning, support, automatisering och hanterbarhet i [Azure Resource Manager](../logic-apps/logic-apps-create-deploy-azure-resource-manager-templates.md) |
| Administratörsupplevelse |[https://flow.microsoft.com](https://flow.microsoft.com) |[https://portal.azure.com](https://portal.azure.com) |
| Säkerhet |Standardmetoder: [datasuveränitet](https://wikipedia.org/wiki/Technological_Sovereignty), [kryptering i vila](https://wikipedia.org/wiki/Data_at_rest#Encryption), känsliga data osv. |Säkerhetskontroller i Azure: [Azure-säkerhet](https://www.microsoft.com/trustcenter/Security/AzureSecurity), [Security Center](https://azure.microsoft.com/services/security-center/), [spårningsloggar](https://azure.microsoft.com/blog/azure-audit-logs-ux-refresh/) med mera. |

<a name="function"></a>

## <a name="functions-vs-webjobs"></a>Functions jämfört med Webbjobb
Det finns en poäng i att presentera Azure Functions och Azure App Service WebJobs tillsammans eftersom båda är integreringstjänster för utvecklare där *koden hamnar i fokus*. Du kan köra ett skript eller en typ av kod i dem som svar på olika händelser, såsom [nya Storage-blobbar](functions-bindings-storage.md) eller [en WebHook-begäran](functions-bindings-http-webhook.md). De liknar varandra på flera sätt: 

* Båda bygger på [Azure App Service](../app-service/app-service-web-overview.md) och tillhandahåller funktioner såsom [källkontroll](../app-service/app-service-continuous-deployment.md), [autentisering](../app-service/app-service-authentication-overview.md) och [övervakning](../app-service/web-sites-monitor.md).
* Båda är tjänster riktade till utvecklare.
* Båda har stöd för standardskript och programmeringsspråk.
* Båda har stöd för NuGet och NPM.

Functions är en naturlig vidareutveckling av WebJobs eftersom Functions har tagit över alla fördelar med WebJobs fördelarna och förbättrat dem. Förbättringarna innefattar: 

* [Serverfria ](https://azure.microsoft.com/overview/serverless-computing/) appmodeller.
* Strömlinjeformad utveckling, testning, och körning av kod direkt i webbläsaren.
* Inbyggd integrering med flera Azure-tjänster och tredjepartstjänster såsom [GitHub WebHooks](https://developer.github.com/webhooks/creating/).
* Betala per tillfälle, du behöver inte betala för någon [App Service-plan](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).
* Automatisk [dynamisk skalning](functions-scale.md).
* Befintliga App Service-kunder kan fortfarande köra vidare med en App Service-plan (för att dra nytta av outnyttjade resurser).
* Integrering med Logic Apps.

I följande tabell sammanfattas skillnaderna mellan Functions och WebJobs:

|  | Funktioner | Webbjobb |
| --- | --- | --- |
| Skalning |Skalning utan konfiguration |Skalning med App Service-plan |
| Prissättning |Betala per användning eller som en del av en App Service-plan |Som del av en App Service-plan |
| Typ av körning |Utlösare, schemalagda (med timerutlösare) |Utlösare, kontinuerliga, schemalagda |
| Utlösarhändelser |[Timer](functions-bindings-timer.md), [Azure Cosmos DB](functions-bindings-documentdb.md), [Azure Event Hubs](functions-bindings-event-hubs.md), [HTTP/WebHook (GitHub, Slack)](functions-bindings-http-webhook.md), [Azure App Service Mobile Apps](functions-bindings-mobile-apps.md), [Azure Event Hubs](functions-bindings-event-hubs.md), [Azure Storage-köer och blobbar](functions-bindings-storage-blob.md), [Azure Service Bus-köer och ämnen](functions-bindings-service-bus.md) |[Azure Storage-köer och blobbar](functions-bindings-storage-blob.md), [Azure Service Bus-köer och ämnen](functions-bindings-service-bus.md) |
| Utveckling i webbläsare |Stöds |Stöds inte |
| C# |Stöds |Stöds |
| F# |Stöds |Stöds inte |
| JavaScript |Stöds |Stöds |
| Java |Förhandsversion | Stöds inte |
| Bash |Experimentell |Stöds |
| Windows-skript (.cmd, .bat) |Experimentell |Stöds |
| PowerShell |Experimentell |Stöds |
| PHP |Experimentell |Stöds |
| Python |Experimentell |Stöds |
| TypeScript |Experimentell |Stöds inte |

Om du vill använda Functions eller WebJobs beror på vad du redan gör med App Service. Om du har en App Service-app som du vill köra kodavsnitt i, och vill hantera dem tillsammans i samma DevOps-miljö, ska du använda WebJobs. Använd Functions i scenarierna nedan.

* Du vill köra kodavsnitt för andra Azure-tjänster eller tredjepartsappar.
* Du vill hantera integreringskoden separat från dina App Service-appar.
* Du vill anropa kodavsnitt från en logikapp. 

<a name="together"></a>

## <a name="flow-logic-apps-and-functions-together"></a>Flow, Logic Apps och Functions tillsammans
Som tidigare nämnts bör du välja tjänst efter situation. 

* Använd Flow för enkel optimering inom företaget.
* Om ditt integreringsscenario är för avancerat för Flow ska du istället använda Logic Apps. Samma sak om du behöver DevOps-funktioner och eller har säkerhetskrav att uppfylla.
* Om ett steg i ditt integreringsscenario kräver omfattande anpassning eller specialkodning kan du skriva en funktion som en åtgärd i din logikapp.

Det går att anropa en logikapp i ett flöde. Du kan också anropa en funktion i en logikapp och en logikapp i en funktion. Integreringen mellan Flow, Logic Apps och Functions förbättras ständigt. Det går bra att skapa något i en tjänst och sedan använda detsamma i de andra tjänsterna. Med andra ord är ingen investering i någon av dessa tre tekniker någonsin bortkastade pengar.

## <a name="next-steps"></a>Nästa steg
Kom igång med tjänsterna genom att skapa ditt första flöde, logikapp, funktionsapp eller WebJob. Klicka på någon av följande länkar:

* [Kom igång med Microsoft Flow](https://flow.microsoft.com/en-us/documentation/getting-started/)
* [Skapa en logikapp](../logic-apps/logic-apps-create-a-logic-app.md)
* [Skapa din första Azure-funktion](functions-create-first-azure-function.md)
* [Distribuera WebJobs med hjälp av Visual Studio](../app-service/websites-dotnet-deploy-webjobs.md)

Du hittar mer information om dessa integreringstjänster via följande länkar:

* [Artikel av Christopher Anderson om hur man utnyttjar Azure Functions och Azure App Service i integreringsscenarier](http://www.biztalk360.com/integrate-2016-resources/leveraging-azure-functions-azure-app-service-integration-scenarios/)
* [Artikel av Charles Lamanna om hur man förenklar integreringar](http://www.biztalk360.com/integrate-2016-resources/integrations-made-simple/)
* [Webbsändning om Logic Apps](http://aka.ms/logicappslive)
* [Vanliga frågor och svar om Microsoft Flow](https://flow.microsoft.com/documentation/frequently-asked-questions/)

