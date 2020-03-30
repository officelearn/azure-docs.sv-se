---
title: Översikt över varaktiga funktioner - Azure Functions
description: Läs mer om versioner av varaktiga funktioner.
author: cgillum
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: azfuncdf
ms.openlocfilehash: 4a117e7f69647af3ad82f9013bfa40556ccc0dbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77152898"
---
# <a name="durable-functions-versions-overview"></a>Översikt över hållbara funktioner

*Varaktiga funktioner* är ett tillägg av [Azure Functions](../functions-overview.md) och [Azure WebJobs](../../app-service/web-sites-create-web-jobs.md) som låter dig skriva tillståndskänsliga funktioner i en serverlös miljö. Tillägget hanterar tillstånd, kontrollpunkter och omstarter. Om du inte redan är bekant med varaktiga funktioner läser du [översiktsdokumentationen](durable-functions-overview.md).

## <a name="new-features-in-2x"></a>Nya funktioner i 2.x

I det här avsnittet beskrivs funktionerna i varaktiga funktioner som läggs till i version 2.x.

### <a name="durable-entities"></a>Varaktiga enheter

I Durable Functions 2.x introducerade vi ett nytt [enhetsfunktioner](durable-functions-entities.md) koncept.

Entitetsfunktioner definierar åtgärder för läsning och uppdatering av små delar av tillståndet, så kallade *varaktiga entiteter*. Liksom orchestrator-funktioner är entitetsfunktioner funktioner med en speciell utlösartyp, *entitetsutlösare*. Till skillnad från orchestrator-funktioner har entitetsfunktioner inga specifika kodbegränsningar. Entitetsfunktioner hanterar också tillstånd explicit i stället för att implicit representera tillstånd via kontrollflöde.

Mer information finns i artikeln [med varaktiga entiteter.](durable-functions-entities.md)

### <a name="durable-http"></a>Hållbar HTTP

I Durable Functions 2.x introducerade vi en ny [hållbar HTTP-funktion](durable-functions-http-features.md#consuming-http-apis) som gör att du kan:

* Anropa HTTP-API:er direkt från orchestration-funktioner (med vissa dokumenterade begränsningar).
* Implementera automatisk HTTP 202-statusavsökning på klientsidan.
* Inbyggt stöd för [Azure-hanterade identiteter](../../active-directory/managed-identities-azure-resources/overview.md).

Mer information finns i artikeln [med HTTP-funktioner.](durable-functions-http-features.md#consuming-http-apis)

## <a name="migrate-from-1x-to-2x"></a>Migrera från 1.x till 2.x

I det här avsnittet beskrivs hur du migrerar dina befintliga funktioner för version 1.x till version 2.x för att dra nytta av de nya funktionerna.

### <a name="upgrade-the-extension"></a>Uppgradera tillägget

Installera version 2.x av [tillägget Varaktiga funktioner bindningar](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) i projektet. Mer information [finns i Registrera bindningstillägg](../functions-bindings-register.md) för Azure Functions.

### <a name="update-your-code"></a>Uppdatera koden

Varaktiga funktioner 2.x introducerar flera bryta ändringar. Varaktiga funktioner 1.x-program är inte kompatibla med varaktiga funktioner 2.x utan kodändringar. I det här avsnittet visas några av de ändringar du måste göra när du uppgraderar funktionerna version 1.x till 2.x.

#### <a name="hostjson-schema"></a>Host.json-schema

Varaktiga funktioner 2.x använder ett nytt host.json-schema. De viktigaste ändringarna från 1.x inkluderar:

* `"storageProvider"`(och `"azureStorage"` underavsnittet) för lagringsspecifik konfiguration.
* `"tracing"`för att spåra och logga konfiguration.
* `"notifications"`(och `"eventGrid"` underavsnittet) för händelsenätmeddelandekonfiguration.

Mer information finns i [referensdokumentationen för varaktiga funktioner som värd.json.](durable-functions-bindings.md#durable-functions-2-0-host-json)

#### <a name="default-taskhub-name-changes"></a>Standardändringar för taskhub-namn

Om ett aktivitetsnavnamn inte angavs i host.json i version 1.x, var det som standard "DurableFunctionsHub". I version 2.x härleds nu standardaktivitetsnavets namn från namnet på funktionsappen. Om du inte har angett ett aktivitetsnavnamn när du uppgraderar till 2.x kommer koden därför att fungera med en ny aktivitetsnav och alla orkestreringar under flygning inte längre har ett program som bearbetar dem. För att komma runt detta kan du antingen uttryckligen ange aktivitetsnavnamnet till v1.x-standardvärdet för "DurableFunctionsHub", eller så kan du följa vår [distributionsvägledning för noll stilleståndstid](durable-functions-zero-downtime-deployment.md) för mer information om hur du hanterar bryta ändringar för orchestrations under flygning.

#### <a name="public-interface-changes-net-only"></a>Ändringar av det offentliga gränssnittet (endast.NET)

I version 1.x har de olika _kontextobjekt_ som stöds av varaktiga funktioner abstrakta basklasser som är avsedda att användas vid enhetstestning. Som en del av Durable Functions 2.x ersätts dessa abstrakta basklasser med gränssnitt.

Följande tabell representerar de viktigaste ändringarna:

| 1.x | 2.x |
|----------|----------|
| `DurableOrchestrationClientBase` | `IDurableOrchestrationClient` eller `IDurableClient` |
| `DurableOrchestrationContext` eller `DurableOrchestrationContextBase` | `IDurableOrchestrationContext` |
| `DurableActivityContext` eller `DurableActivityContextBase` | `IDurableActivityContext` |
| `OrchestrationClientAttribute` | `DurableClientAttribute` |

Om en abstrakt basklass innehöll virtuella metoder har dessa virtuella metoder ersatts med tilläggsmetoder som definierats i `DurableContextExtensions`.

#### <a name="functionjson-changes-javascript-and-c-script"></a>function.json förändringar (JavaScript och C # Script)

I Varaktiga funktioner 1.x använder orchestration-klientbindningen en `type` av `orchestrationClient`. Version 2.x `durableClient` använder istället.
