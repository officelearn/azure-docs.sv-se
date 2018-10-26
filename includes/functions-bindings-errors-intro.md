---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 9fb462b83eccde3d2b486d491517973fe6bac4f1
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2018
ms.locfileid: "50133694"
---
Azure Functions [utlösare och bindningar](..\articles\azure-functions\functions-triggers-bindings.md) kommunicera med olika Azure-tjänster. När du integrerar med dessa tjänster, kanske fel som kommer från API: er för de underliggande Azure-tjänsterna. Fel kan också inträffa när du försöker kommunicera med andra tjänster från Funktionskoden med hjälp av REST eller klientbiblioteken. För att undvika förlust av data och se till att bra beteendet för dina funktioner, är det viktigt att hantera fel från varken med källan.

Följande utlösare har inbyggda stöd:

* [Azure Blob Storage](../articles/azure-functions/functions-bindings-storage-blob.md)
* [Azure-kölagring](../articles/azure-functions/functions-bindings-storage-queue.md)
* [Azure Service Bus (kö/ämne)](../articles/azure-functions/functions-bindings-service-bus.md)

Som standard är de här utlösarna göras upp till fem gånger. Efter det femte återförsöket dessa utlösare skriva ett meddelande till en särskild [skadliga kö](..\articles\azure-functions\functions-bindings-storage-queue.md#trigger---poison-messages). 

För de andra Functions-utlösarna finns inga inbyggda återförsök när fel uppstår under körning av funktion. För att förhindra förlust av information om utlösaren ska uppstå ett fel i din funktion, rekommenderar vi att du använder trycatch-block i Funktionskoden att fånga upp eventuella fel. När ett fel uppstår, skriver du den information som skickas till funktionen av utlösaren till en särskild ”skadliga” meddelandekö. Den här metoden är samma som används av den [Blob storage-utlösare](..\articles\azure-functions\functions-bindings-storage-blob.md#trigger---poison-blobs). 

På så sätt kan avbilda du utlösande händelser som kan gå förlorade på grund av fel och återförsök vid ett senare tillfälle med hjälp av en annan funktion du bearbetar meddelanden från skadliga kön med hjälp av den lagrade informationen.  
