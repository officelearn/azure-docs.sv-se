---
title: Använda Blob Storage som kontrollpunktsarkiv på Azure Stack Hub (förhandsversion)
description: I den här artikeln beskrivs hur du använder Blob Storage som en kontrollpunktsbutik i eventhubbar på Azure Stack Hub (förhandsversion).
services: event-hubs
documentationcenter: na
author: spelluru
ms.service: event-hubs
ms.topic: how-to
ms.date: 03/18/2020
ms.author: spelluru
ms.openlocfilehash: 1f0e4dea44007ef82cb4b700ff0be4a5579541d8
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398927"
---
# <a name="use-blob-storage-as-checkpoint-store---event-hubs-on-azure-stack-hub-preview"></a>Använd Blob Storage som kontrollpunktsarkiv – eventhubbar på Azure Stack Hub (förhandsversion)
Om du använder Azure Blob Storage som kontrollpunktsarkiv i en miljö som stöder en annan version av Storage Blob SDK än de som vanligtvis är tillgängliga på Azure, måste du använda koden för att ändra API-versionen för lagringstjänsten till den specifika versionen som stöds av den miljön. Om du till exempel kör [Event Hubs på en Azure Stack Hub version 2002](https://docs.microsoft.com/azure-stack/user/event-hubs-overview)är den högsta tillgängliga versionen för lagringstjänsten version 2017-11-09. I det här fallet måste du använda kod för att rikta api-versionen för lagringstjänsten till 2017-11-09. Ett exempel på hur du inriktar dig på en specifik Storage API-version finns i dessa exempel på GitHub: 

- [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample10_RunningWithDifferentStorageVersion.cs)
- [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorWithOlderStorageVersion.java). 
- [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/receiveEventsWithDownleveledStorage.js) eller [TypeScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/receiveEventsWithDownleveledStorage.ts), 
- Python - [Synkron](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob/samples/event_processor_blob_storage_example_with_storage_api_version.py), [Asynkron](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/event_processor_blob_storage_example_with_storage_api_version.py)

> [!IMPORTANT]
> Event Hubs på Azure Stack Hub är för närvarande i [förhandsversion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) och är gratis. 

Om du kör Mottagare av Event Hubs som använder Blob Storage som kontrollpunktsarkiv utan att inrikta dig på den version som Azure Stack Hub stöder visas följande felmeddelande:

```
The value for one of the HTTP headers is not in the correct format
```


## <a name="sample-error-message-in-python"></a>Exempel på felmeddelande i Python
För Python skickas `azure.core.exceptions.HttpResponseError` ett fel i `on_error(partition_context, error)` felhanteraren `EventHubConsumerClient.receive()`för . Men metoden `receive()` tar inte upp ett undantag. `print(error)`kommer att skriva ut följande undantagsinformation:

```bash
The value for one of the HTTP headers is not in the correct format.

RequestId:f048aee8-a90c-08ba-4ce1-e69dba759297
Time:2020-03-17T22:04:13.3559296Z
ErrorCode:InvalidHeaderValue
Error:None
HeaderName:x-ms-version
HeaderValue:2019-07-07
```

Loggern loggar två varningar som följande:

```bash
WARNING:azure.eventhub.extensions.checkpointstoreblobaio._blobstoragecsaio: 
An exception occurred during list_ownership for namespace '<namespace-name>.eventhub.<region>.azurestack.corp.microsoft.com' eventhub 'python-eh-test' consumer group '$Default'. 

Exception is HttpResponseError('The value for one of the HTTP headers is not in the correct format.\nRequestId:f048aee8-a90c-08ba-4ce1-e69dba759297\nTime:2020-03-17T22:04:13.3559296Z\nErrorCode:InvalidHeaderValue\nError:None\nHeaderName:x-ms-version\nHeaderValue:2019-07-07')

WARNING:azure.eventhub.aio._eventprocessor.event_processor:EventProcessor instance '26d84102-45b2-48a9-b7f4-da8916f68214' of eventhub 'python-eh-test' consumer group '$Default'. An error occurred while load-balancing and claiming ownership. 

The exception is HttpResponseError('The value for one of the HTTP headers is not in the correct format.\nRequestId:f048aee8-a90c-08ba-4ce1-e69dba759297\nTime:2020-03-17T22:04:13.3559296Z\nErrorCode:InvalidHeaderValue\nError:None\nHeaderName:x-ms-version\nHeaderValue:2019-07-07'). Retrying after 71.45254944090853 seconds
```



## <a name="next-steps"></a>Nästa steg

Se följande artikel lär dig mer om partitionering och kontrollpunkter: [Balansera partitionsbelastning över flera instanser av ditt program](event-processor-balance-partition-load.md)
