---
title: Introduktion till Azure-köer – Azure Storage
description: Introduktion till Azure-köer
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/18/2020
ms.service: storage
ms.subservice: queues
ms.topic: overview
ms.reviewer: cbrooks
ms.openlocfilehash: 4a2bea77578282d68d86bc1a8cea765aa2cbd555
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "80060853"
---
# <a name="what-are-azure-queues"></a>Vad är Azure-köer?

Azure Queue Storage är en tjänst för lagring av ett stort antal meddelanden. Du kommer åt meddelanden från var som helst i världen via autentiserade samtal med HTTP eller HTTPS. Ett kömeddelande kan vara upp till 64 kB i storlek. En kö kan innehålla miljontals meddelanden, upp till den totala kapacitetsgränsen för ett lagringskonto. Köer används ofta för att skapa en eftersläpning av arbete för att bearbeta asynkront.

## <a name="queue-service-concepts"></a>Kötjänstbegrepp

Kötjänsten består av följande komponenter:

![Diagram som visar relationen mellan ett lagringskonto, köer och meddelanden](./media/storage-queues-introduction/queue1.png)

* **URL-format:** köer är adresserbara via följande URL-format:

    `https://<storage account>.queue.core.windows.net/<queue>`
  
    Följande URL adresserar en kö i diagrammet:  
  
    `https://myaccount.queue.core.windows.net/images-to-download`

* **Lagringskonto:** All åtkomst till Azure Storage sker via ett lagringskonto. Information om lagringskontokapacitet finns i [Skalbarhets- och prestandamål för standardlagringskonton](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).

* **Kö:** en kö innehåller en uppsättning meddelanden. Könamnet **måste** vara gemen. Mer information om namngivning av köer finns i [namngivning av köer och metadata](https://msdn.microsoft.com/library/azure/dd179349.aspx).

* **Meddelande:** ett meddelande i valfritt format, som är upp till 64 KB. Före version 2017-07-29 är den högsta tillåtna tiden sju dagar. För version 2017-07-29 eller senare kan den maximala tiden för att leva vara valfritt positivt tal, eller -1 som anger att meddelandet inte upphör att gälla. Om den här parametern utelämnas är standardtiden att leva sju dagar.

## <a name="next-steps"></a>Nästa steg

* [skapar ett lagringskonto](../storage-create-storage-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
* [Komma igång med köer med .NET](storage-dotnet-how-to-use-queues.md)
