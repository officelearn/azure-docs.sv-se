---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.custom: seo-python-october2019
ms.openlocfilehash: 23b93f507ef6abe19a0202b28afa31d28490b2b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80151150"
---
## <a name="what-is-queue-storage"></a>Vad är kölagring?

Azure Queue Storage är en tjänst för att lagra stora mängder meddelanden som kan nås från var som helst i världen via autentiserade anrop med HTTP eller HTTPS. Ett enda kömeddelande kan vara upp till 64 KB stort och en kö kan innehålla miljontals meddelanden, upp till den totala kapacitetsgränsen för ett lagringskonto. Kölagring används ofta för att skapa en eftersläpning av arbete för att bearbeta asynkront.

## <a name="queue-service-concepts"></a>Kötjänstbegrepp

Azure-kötjänsten innehåller följande komponenter:

![Tjänstkomponenter för Azure-kö](./media/storage-queue-concepts-include/azure-queue-service-components.png)

* **URL-format:** Köer kan adresseras med hjälp av`<storage account>`följande URL-format: http:// .queue.core.windows.net/`<queue>`
  
    Följande URL adresserar en kö i diagrammet:  
  
    `http://myaccount.queue.core.windows.net/incoming-orders`

* **Lagringskonto:** All åtkomst till Azure Storage görs genom ett lagringskonto. Mer information om lagringskonton finns i [Översikt över lagringskonto](../articles/storage/common/storage-account-overview.md).
* **Kö:** en kö innehåller en uppsättning meddelanden. Alla meddelanden måste vara i en kö. Observera att könamnet måste vara helt i gemener. Mer information om namngivning av köer finns i [namngivning av köer och metadata](https://msdn.microsoft.com/library/azure/dd179349.aspx).
* **Meddelande:** ett meddelande i valfritt format, som är upp till 64 KB. Den maximala tid som ett meddelande kan finnas i kön är 7 dagar. För version 2017-07-29 eller senare kan den maximala tiden för att leva vara valfritt positivt tal, eller -1 som anger att meddelandet inte upphör att gälla. Om den här parametern utelämnas är standardtiden att leva sju dagar.

