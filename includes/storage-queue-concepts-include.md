---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.custom: seo-python-october2019
ms.openlocfilehash: 23b93f507ef6abe19a0202b28afa31d28490b2b3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80151150"
---
## <a name="what-is-queue-storage"></a>Vad är Queue Storage?

Azure Queue Storage är en tjänst för att lagra stora mängder meddelanden som kan nås från var som helst i världen via autentiserade anrop med HTTP eller HTTPS. Ett enda kömeddelande kan vara upp till 64 KB stort och en kö kan innehålla miljontals meddelanden, upp till den totala kapacitetsgränsen för ett lagringskonto. Queue Storage används ofta för att skapa en efter släpning av arbete som ska bearbetas asynkront.

## <a name="queue-service-concepts"></a>Kötjänst begrepp

Azure-Kötjänst innehåller följande komponenter:

![Azure Kötjänst-komponenter](./media/storage-queue-concepts-include/azure-queue-service-components.png)

* **URL-format:** Köer är adresser bara med följande URL-format: http://`<storage account>`. Queue.Core.Windows.net/`<queue>`
  
    Följande URL adresserar en kö i diagrammet:  
  
    `http://myaccount.queue.core.windows.net/incoming-orders`

* **Lagringskonto:** All åtkomst till Azure Storage görs genom ett lagringskonto. Mer information om lagrings konton finns i [Översikt över lagrings konto](../articles/storage/common/storage-account-overview.md).
* **Kö:** en kö innehåller en uppsättning meddelanden. Alla meddelanden måste vara i en kö. Observera att könamnet måste vara helt i gemener. Mer information om namngivning av köer finns i [namngivning av köer och metadata](https://msdn.microsoft.com/library/azure/dd179349.aspx).
* **Meddelande:** ett meddelande i valfritt format, som är upp till 64 KB. Den maximala tid som ett meddelande kan finnas i kön är 7 dagar. För version 2017-07-29 eller senare kan den maximala tiden till Live vara ett positivt tal eller-1 som anger att meddelandet inte upphör att gälla. Om den här parametern utelämnas, är standardvärdet för Time-to-Live sju dagar.

