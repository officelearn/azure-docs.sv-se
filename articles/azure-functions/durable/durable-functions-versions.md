---
title: Översikt över Durable Functions-versioner – Azure Functions
description: Lär dig mer om Durable Functions-versioner.
author: cgillum
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: azfuncdf
ms.openlocfilehash: 4a117e7f69647af3ad82f9013bfa40556ccc0dbd
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152898"
---
# <a name="durable-functions-versions-overview"></a>Översikt över Durable Functions-versioner

*Durable Functions* är ett tillägg till [Azure Functions](../functions-overview.md) och [Azure WebJobs](../../app-service/web-sites-create-web-jobs.md) som låter dig skriva tillstånds känsliga funktioner i en miljö utan server. Tillägget hanterar tillstånd, kontrollpunkter och omstarter. Om du inte redan är bekant med Durable Functions kan du läsa mer i [översikts dokumentationen](durable-functions-overview.md).

## <a name="new-features-in-2x"></a>Nya funktioner i 2. x

I det här avsnittet beskrivs funktionerna i Durable Functions som läggs till i version 2. x.

### <a name="durable-entities"></a>Varaktiga entiteter

Vi introducerade en ny [entitet Functions](durable-functions-entities.md) -koncept i Durable Functions 2. x.

Entitets funktioner definierar åtgärder för att läsa och uppdatera små delar av tillstånd, så kallade *varaktiga entiteter*. Precis som med Orchestrator Functions är enhets funktionerna funktioner med en särskild utlösnings typ, *enhets utlösare*. Till skillnad från Orchestrator-funktioner har enhets funktioner inga speciella kod begränsningar. Enhets funktionerna hanterar också tillstånd explicit snarare än implicit som representerar tillstånd via kontroll flödet.

Mer information finns i artikeln [beständiga entiteter](durable-functions-entities.md) .

### <a name="durable-http"></a>Varaktig HTTP

I Durable Functions 2. x introducerade vi en ny [beständig HTTP-](durable-functions-http-features.md#consuming-http-apis) funktion som gör att du kan:

* Anropa HTTP-API: er direkt från Orchestration-funktioner (med vissa dokumenterade begränsningar).
* Implementera automatisk HTTP 202-avsökning på klient sidan.
* Inbyggt stöd för [Azure Managed identiteter](../../active-directory/managed-identities-azure-resources/overview.md).

Mer information finns i artikeln om [http-funktioner](durable-functions-http-features.md#consuming-http-apis) .

## <a name="migrate-from-1x-to-2x"></a>Migrera från 1. x till 2. x

I det här avsnittet beskrivs hur du migrerar den befintliga version 1. x-Durable Functions till version 2. x för att dra nytta av de nya funktionerna.

### <a name="upgrade-the-extension"></a>Uppgradera tillägget

Installera version 2. x av [tillägget Durable Functions bindningar](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) i projektet. Mer information finns i [registrera Azure Functions bindnings tillägg](../functions-bindings-register.md) .

### <a name="update-your-code"></a>Uppdatera koden

Durable Functions 2. x introducerar flera avbrytande ändringar. Durable Functions 1. x-program är inte kompatibla med Durable Functions 2. x utan kod ändringar. I det här avsnittet visas några av de ändringar du måste göra när du uppgraderar dina version 1. x-funktioner till 2. x.

#### <a name="hostjson-schema"></a>Host. JSON-schema

Durable Functions 2. x använder ett nytt Host. JSON-schema. Huvud ändringarna från 1. x är:

* `"storageProvider"` (och underavsnittet `"azureStorage"`) för den platsspecifika konfigurationen.
* `"tracing"` för spårnings-och loggnings konfiguration.
* `"notifications"` (och `"eventGrid"` underavsnitt) för konfiguration av event Grid-meddelanden.

Mer information finns i [referens dokumentationen för Durable Functions Host. JSON](durable-functions-bindings.md#durable-functions-2-0-host-json) .

#### <a name="default-taskhub-name-changes"></a>Standard namn ändringar för taskhub

I version 1. x, om ett aktivitets nav namn inte har angetts i Host. JSON, var det som standard "DurableFunctionsHub". I version 2. x härleds nu standard namnet för aktivitets navet från namnet på Function-appen. På grund av detta, om du inte har angett ett aktivitets NAVs namn när du uppgraderar till 2. x, kommer din kod att fungera med New Task Hub och all pågående dirigering kommer inte längre att ha något program som bearbetar dem. För att undvika detta kan du antingen uttryckligen ange namnet på ditt aktivitets nav till v1. x-standardvärdet "DurableFunctionsHub", eller så kan du följa vår [distributions vägledning om noll-nedtid](durable-functions-zero-downtime-deployment.md) för att få mer information om hur du hanterar överstigande ändringar för flyg-och Dirigerings åtgärder.

#### <a name="public-interface-changes-net-only"></a>Offentliga gränssnitts ändringar (endast .NET)

I version 1. x har de olika _kontext_ objekt som stöds av Durable Functions abstrakta bas klasser avsedda att användas i enhets testning. Som en del av Durable Functions 2. x ersätts dessa abstrakta bas klasser med gränssnitt.

Följande tabell representerar huvud ändringarna:

| 1.x | 2x |
|----------|----------|
| `DurableOrchestrationClientBase` | `IDurableOrchestrationClient` eller `IDurableClient` |
| `DurableOrchestrationContext` eller `DurableOrchestrationContextBase` | `IDurableOrchestrationContext` |
| `DurableActivityContext` eller `DurableActivityContextBase` | `IDurableActivityContext` |
| `OrchestrationClientAttribute` | `DurableClientAttribute` |

Om en abstrakt basklass innehåller virtuella metoder har de här virtuella metoderna ersatts av tilläggs metoder som definierats i `DurableContextExtensions`.

#### <a name="functionjson-changes-javascript-and-c-script"></a>function. JSON-ändringar (Java C# script och skript)

I Durable Functions 1. x använder Dirigerings klientens bindning en `type` av `orchestrationClient`. Version 2. x använder `durableClient` i stället.
