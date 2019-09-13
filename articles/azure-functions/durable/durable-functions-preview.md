---
title: Durable Functions för hands versions funktioner – Azure Functions
description: Lär dig mer om förhands gransknings funktioner för Durable Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: article
ms.date: 09/04/2019
ms.author: azfuncdf
ms.openlocfilehash: 8f2560141eac4e7d9fed267d347990e65bfe9c26
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2019
ms.locfileid: "70933248"
---
# <a name="durable-functions-20-preview-azure-functions"></a>Durable Functions 2,0 för hands version (Azure Functions)

*Durable Functions* är ett tillägg till [Azure Functions](../functions-overview.md) och [Azure WebJobs](../../app-service/web-sites-create-web-jobs.md) som låter dig skriva tillstånds känsliga funktioner i en miljö utan server. Tillägget hanterar tillstånd, kontrollpunkter och omstarter. Om du inte redan är bekant med Durable Functions kan du läsa mer i [översikts dokumentationen](durable-functions-overview.md).

Durable Functions 1. x är en GA (allmänt tillgänglig) funktion i Azure Functions, men innehåller också flera underfunktioner som för närvarande finns i en offentlig för hands version. I den här artikeln beskrivs nyligen utgivna för hands versions funktioner och information om hur de fungerar och hur du kan börja använda dem.

> [!NOTE]
> Dessa förhands gransknings funktioner ingår i en Durable Functions 2,0-version, som för närvarande är en för hands versions **kvalitet** med flera större ändringar. Azure Functions beständiga tilläggs paket versioner finns på nuget.org med versioner i form av **2.0.0-betaX**. Dessa versioner är inte avsedda för produktions arbets belastningar och senare versioner kan innehålla ytterligare avbrytande ändringar.

## <a name="breaking-changes"></a>Icke-bakåtkompatibla ändringar

Flera brytande ändringar introduceras i Durable Functions 2,0. Befintliga program förväntas inte vara kompatibla med Durable Functions 2,0 utan kod ändringar. I det här avsnittet visas några av ändringarna:

### <a name="hostjson-schema"></a>Host. JSON-schema

Följande fragment visar det nya schemat för Host. JSON. De viktigaste ändringarna som är medvetna om är de nya underavsnitten:

* `"storageProvider"`(och `"azureStorage"` underavsnittet) för Storage-speciell konfiguration
* `"tracking"`för spårnings-och loggnings konfiguration
* `"notifications"`(och `"eventGrid"` underavsnittet) för konfiguration av event Grid-meddelanden

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "azureStorage": {
          "connectionStringName": <string>,
          "controlQueueBatchSize": <int?>,
          "partitionCount": <int?>,
          "controlQueueVisibilityTimeout": <hh:mm:ss?>,
          "workItemQueueVisibilityTimeout": <hh:mm:ss?>,
          "trackingStoreConnectionStringName": <string?>,
          "trackingStoreNamePrefix": <string?>,
          "maxQueuePollingInterval": <hh:mm:ss?>
        }
      },
      "tracking": {
        "traceInputsAndOutputs": <bool?>,
        "traceReplayEvents": <bool?>,
      },
      "notifications": {
        "eventGrid": {
          "topicEndpoint": <string?>,
          "keySettingName": <string?>,
          "publishRetryCount": <string?>,
          "publishRetryInterval": <hh:mm:ss?>,
          "publishRetryHttpStatus": <int[]?>,
          "publishEventTypes": <string[]?>,
        }
      },
      "maxConcurrentActivityFunctions": <int?>,
      "maxConcurrentOrchestratorFunctions": <int?>,
      "extendedSessionsEnabled": <bool?>,
      "extendedSessionIdleTimeoutInSeconds": <int?>,
      "customLifeCycleNotificationHelperType": <string?>
  }
}
```

Eftersom Durable Functions 2,0 fortsätter att stabiliseras kommer fler ändringar att införas i `durableTask` avsnittet Host. JSON. Mer information om dessa ändringar finns i [det här GitHub-problemet](https://github.com/Azure/azure-functions-durable-extension/issues/641).

### <a name="public-interface-changes"></a>Offentliga gränssnitts ändringar

De olika kontext objekt som stöds av Durable Functions hade abstrakta bas klasser avsedda att användas vid enhets testning. Som en del av Durable Functions 2,0 har dessa abstrakta bas klasser bytts ut mot gränssnitt. Funktions kod som använder konkreta typer direkt påverkas inte.

Följande tabell representerar huvud ändringarna:

| Gammal typ | Ny typ |
|----------|----------|
| DurableOrchestrationClientBase | IDurableOrchestrationClient |
| DurableOrchestrationContextBase | IDurableOrchestrationContext |
| DurableActivityContextBase | IDurableActivityContext |
| OrchestrationClientAttribute | DurableClientAttribute |

Om en abstrakt basklass innehåller virtuella metoder har de här virtuella metoderna ersatts av tilläggs metoder som definierats i `DurableContextExtensions`.

## <a name="entity-functions"></a>Enhets funktioner

Från och med Durable Functions v 2.0.0-alpha har vi introducerat en ny [entitet Functions](durable-functions-entities.md) -koncept.

Entitets funktioner definierar åtgärder för att läsa och uppdatera små delar av tillstånd,så kallade varaktiga entiteter. Precis som med Orchestrator Functions är enhets funktionerna funktioner med en särskildutlösnings typ, enhets utlösare. Till skillnad från Orchestrator-funktioner har enhets funktioner inga speciella kod begränsningar. Enhets funktionerna hanterar också tillstånd explicit snarare än implicit som representerar tillstånd via kontroll flödet.

Baserat på feedback från första användare lade vi senare till stöd för en klass-baserad programmerings modell för entiteter i Durable Functions v-2.0.0-beta1.

Mer information finns i artikeln [entitet Functions](durable-functions-entities.md) .

## <a name="durable-http"></a>Varaktig HTTP

Från och med Durable Functions v 2.0.0-beta2 har vi introducerat en ny [BESTÄNDIG http](durable-functions-http-features.md) -funktion som gör att du kan:

* Anropa HTTP-API: er direkt från Orchestration-funktioner (med vissa dokumenterade begränsningar)
* Implementerar automatisk status avsökning för HTTP 202 på klient Sidan
* Inbyggt stöd för [Azure Managed Identities](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

Mer information finns i artikeln om [http-funktioner](durable-functions-http-features.md#consuming-http-apis) .

## <a name="alternate-storage-providers"></a>Alternativa lagringsprovider

Det tåliga aktivitets ramverket har stöd för flera lagringsprovider idag, inklusive [Azure Storage](https://github.com/Azure/durabletask/tree/master/src/DurableTask.AzureStorage), [Azure Service Bus](https://github.com/Azure/durabletask/tree/master/src/DurableTask.ServiceBus), en [intern emulator](https://github.com/Azure/durabletask/tree/master/src/DurableTask.Emulator)och en experimentell [Redis](https://github.com/Azure/durabletask/tree/redis/src/DurableTask.Redis) -Provider. Men fram till nu stöds den varaktiga åtgärds utökningen för Azure Functions endast Azure Storage-providern. Från och med Durable Functions 2,0 läggs stöd för alternativa lagringsprovider till, från och med Redis-providern.

> [!NOTE]
> Durable Functions 2,0 stöder endast .NET standard 2,0-kompatibla providers.

### <a name="emulator"></a>Torn

[DurableTask. emulator](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Emulator/) -providern är ett lokalt minne, en icke-beständig lagringsprovider som lämpar sig för lokala testnings scenarier. Den kan konfigureras med följande minimala **värd. JSON** -schema:

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "emulator": { "enabled": true }
      }
    }
  }
}
```

### <a name="redis-experimental"></a>Redis (experimentell)

[DurableTask. Redis](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Redis/) -providern behåller alla Dirigerings tillstånd till ett konfigurerat Redis-kluster.

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "redis": {
          "connectionStringName": <string>,
        }
      }
    }
  }
}
```

`connectionStringName` Måste referera till namnet på en app-inställning eller en miljö variabel. Appens inställning eller miljö variabel ska innehålla ett Redis-anslutnings sträng värde i formatet *Server: port*. Till exempel `localhost:6379` för att ansluta till ett lokalt Redis-kluster.

> [!NOTE]
> Redis-providern är för närvarande experimentell och stöder bara Function-appar som körs på en enda nod. Det garanterar inte att Redis-leverantören någonsin kommer att göras allmänt tillgänglig och kan tas bort i en framtida version.
