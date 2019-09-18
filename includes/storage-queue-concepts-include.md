---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 935a6201e24e97deaa0a1a65e499310f74a49e2d
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71059049"
---
## <a name="what-is-queue-storage"></a>Vad är Queue Storage?

Azure Queue Storage är en tjänst för att lagra stora mängder meddelanden som kan nås från var som helst i världen via autentiserade anrop med HTTP eller HTTPS. Ett enda kömeddelande kan vara upp till 64 KB stort och en kö kan innehålla miljontals meddelanden, upp till den totala kapacitetsgränsen för ett lagringskonto. Queue Storage används ofta för att skapa en efter släpning av arbete som ska bearbetas asynkront.

## <a name="queue-service-concepts"></a>Kötjänst begrepp

Azure-Kötjänst innehåller följande komponenter:

![Kö1](./media/storage-queue-concepts-include/queue1.png)

* **URL-format:** Köer är adresser bara med följande URL-format:   
    http://`<storage account>`.queue.core.windows.net/`<queue>` 
  
    Följande URL adresserar en kö i diagrammet:  
  
    `http://myaccount.queue.core.windows.net/images-to-download`

* **Lagrings konto:** All åtkomst till Azure Storage görs via ett lagrings konto. Se [Skalbarhets- och prestandamål för Azure Storage](../articles/storage/common/storage-scalability-targets.md) för information om kapacitet för lagringskonton.
* **Köjobb** En kö innehåller en uppsättning meddelanden. Alla meddelanden måste vara i en kö. Observera att könamnet måste vara helt i gemener. Mer information om namngivning av köer finns i [namngivning av köer och metadata](https://msdn.microsoft.com/library/azure/dd179349.aspx).
* **Meddelande** Ett meddelande i valfritt format, upp till 64 KB. Den maximala tid som ett meddelande kan finnas i kön är 7 dagar.

