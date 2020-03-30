---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 629de079f7cc7d95d10f8ff951a47b8b8fc62dad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77474184"
---
Fel som tas upp i en Azure-funktioner kan komma från något av följande ursprung:

- Användning av inbyggda Azure [Functions-utlösare och bindningar](..\articles\azure-functions\functions-triggers-bindings.md)
- Anrop till API:er för underliggande Azure-tjänster
- Anropar till REST-slutpunkter
- Anrop till klientbibliotek, paket eller API:er från tredje part

Att följa fasta felhanteringsmetoder är viktigt för att undvika förlust av data eller missade meddelanden. Rekommenderade felhanteringsmetoder omfattar följande åtgärder:

- [Aktivera Application Insights](../articles/azure-functions/functions-monitoring.md)
- [Använda strukturerad felhantering](#use-structured-error-handling)
- [Design för idempotens](../articles/azure-functions/functions-idempotent.md)
- [Implementera principer för återförsök](../articles/azure-functions/functions-reliable-event-processing.md) (i förekommande fall)

### <a name="use-structured-error-handling"></a>Använda strukturerad felhantering

Att samla in och publicera fel är avgörande för att övervaka hälsotillståndet för ditt program. Den översta nivån i en funktionskod bör innehålla ett try/catch-block. I catch-blocket kan du fånga och publicera fel.

### <a name="retry-support"></a>Stöd för återförsök

Följande utlösare har inbyggt återförsöksstöd:

* [Azure Blob-lagring](../articles/azure-functions/functions-bindings-storage-blob.md)
* [Lagring av Azure-kö](../articles/azure-functions/functions-bindings-storage-queue.md)
* [Azure Service Bus (kö/ämne)](../articles/azure-functions/functions-bindings-service-bus.md)

Som standard kan dessa utlösare försöka igen begäranden upp till fem gånger. Efter det femte återförsöket skriver både Azure Queue storage och Azure Service Bus-utlösare ett meddelande till en [giftkö](..\articles\azure-functions\functions-bindings-storage-queue-trigger.md#poison-messages).

Du måste implementera principer för återförsök manuellt för andra utlösare eller bindningar. Manuella implementeringar kan omfatta att skriva felinformation till en [giftmeddelandekö](..\articles\azure-functions\functions-bindings-storage-blob-trigger.md#poison-blobs). Genom att skriva till en giftkö har du möjlighet att försöka igen vid ett senare tillfälle. Den här metoden är samma som används av Blob-lagringsutlösaren.
