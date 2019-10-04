---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 178fa7d5f129a12736ec068fca605ba24cd37839
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/04/2019
ms.locfileid: "71955881"
---
Fel som har Aktiver ATS i en Azure Functions kan komma från något av följande ursprung:

- Användning av inbyggda Azure Functions [utlösare och bindningar](..\articles\azure-functions\functions-triggers-bindings.md)
- Anrop till API: er för underliggande Azure-tjänster
- Anrop till REST-slutpunkter
- Anrop till klient bibliotek, paket eller API: er från tredje part

Följande metoder för solid-fel hantering är viktigt för att undvika förlust av data eller missade meddelanden. Rekommenderade fel hanterings metoder omfattar följande åtgärder:

- [Aktivera Application Insights](../articles/azure-functions/functions-monitoring.md)
- [Använd strukturerad fel hantering](#use-structured-error-handling)
- [Design för idempotens](../articles/azure-functions/functions-idempotent.md)
- [Implementera principer för återförsök](../articles/azure-functions/functions-reliable-event-processing.md) (där det är lämpligt)

### <a name="use-structured-error-handling"></a>Använd strukturerad fel hantering

Det är viktigt att fånga och publicera fel för att övervaka hälso tillståndet för programmet. Den översta nivån i en funktions kod ska innehålla ett try/catch-block. I catch-blocket kan du samla in och publicera fel.

### <a name="retry-support"></a>Stöd för nytt försök

Följande utlösare har inbyggt stöd för återförsök:

* [Azure Blob Storage](../articles/azure-functions/functions-bindings-storage-blob.md)
* [Azure Queue Storage](../articles/azure-functions/functions-bindings-storage-queue.md)
* [Azure Service Bus (kö/ämne)](../articles/azure-functions/functions-bindings-service-bus.md)

Som standard begär de här utlösarna nya försök upp till fem gånger. Efter det femte försöket utlöser båda utlösarna Skriv ett meddelande till en [Poison-kö](..\articles\azure-functions\functions-bindings-storage-queue.md#trigger---poison-messages).

Du måste implementera principer för omförsök manuellt för alla andra utlösare eller bindningar. Manuella implementeringar kan innehålla information om att skriva fel till en [skadlig meddelandekö](..\articles\azure-functions\functions-bindings-storage-blob.md#trigger---poison-blobs). Genom att skriva till en Poison-kö har du möjlighet att försöka utföra åtgärder vid ett senare tillfälle. Den här metoden är samma som används av Blob Storage-utlösaren.
