---
title: Introduktion till Azure-köer – Azure Storage
description: Introduktion till Azure-köer
services: storage
author: mhopkins-msft
ms.service: storage
ms.topic: overview
ms.date: 06/07/2019
ms.author: mhopkins
ms.reviewer: cbrooks
ms.subservice: queues
ms.openlocfilehash: bc3045e3d3b6977555818fcdb3dcaf3246ebd200
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2019
ms.locfileid: "66754805"
---
# <a name="what-are-azure-queues"></a>Vad är Azure-köer?

Azure Queue storage är en tjänst för att lagra stora mängder meddelanden. Du kan komma åt meddelanden överallt i världen via autentiserade anrop via HTTP eller HTTPS. Ett kömeddelande kan vara upp till 64 KB stora. En kö kan innehålla miljontals meddelanden, upp till den totala kapacitetsgränsen för ett lagringskonto.

## <a name="common-uses"></a>Vanliga användningsområden

Vanliga användningsområden för Queue Storage är:

* Skapa en lista med kvarvarande uppgifter att bearbeta asynkront
* Skicka meddelanden från en Azure-webbroll till en Azure-arbetsroll

## <a name="queue-service-concepts"></a>Kötjänst-koncept

Kötjänsten består av följande komponenter:

![Kö-begrepp](./media/storage-queues-introduction/queue1.png)

* **URL-format:** Köer är adresserbara via följande URL-format:

    `https://<storage account>.queue.core.windows.net/<queue>`
  
    Följande URL adresserar en kö i diagrammet:  
  
    `https://myaccount.queue.core.windows.net/images-to-download`

* **Lagringskonto:** All åtkomst till Azure Storage görs genom ett lagringskonto. Se [Skalbarhets- och prestandamål för Azure Storage](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) för information om kapacitet för lagringskonton.

* **Kö:** En kö innehåller en uppsättning meddelanden. Namnet på kön **måste** skrivas med små bokstäver. Mer information om namngivning av köer finns i [namngivning av köer och metadata](https://msdn.microsoft.com/library/azure/dd179349.aspx).

* **meddelande:** Ett meddelande i valfritt format, upp till 64 KB. Före version 2017-07-29 är maxantalet time-to-live sju dagar. För version 2017-07-29 eller senare, den maximala time-to-live kan vara ett positivt tal eller -1 som anger att meddelandet inte upphör att gälla. Om den här parametern utelämnas, standard time-to-live är sju dagar.

## <a name="next-steps"></a>Nästa steg

* [Skapa ett lagringskonto](../storage-create-storage-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
* [Komma igång med köer med hjälp av .NET](storage-dotnet-how-to-use-queues.md)
