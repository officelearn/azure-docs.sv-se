---
title: Introduktion till Azure-köer | Microsoft Docs
description: Introduktion till Azure-köer
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 02/06/2019
ms.author: tamram
ms.subservice: queues
ms.openlocfilehash: 2ae0d3993df54e1c9e5a9bf93619e8f9faa8a917
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55873605"
---
# <a name="what-are-azure-queues"></a>Vad är Azure-köer?

Azure Queue Storage är en tjänst för att lagra stora mängder meddelanden som kan nås från var som helst i världen via autentiserade anrop med HTTP eller HTTPS. Ett enda kömeddelande kan vara upp till 64 KB stort och en kö kan innehålla miljontals meddelanden, upp till den totala kapacitetsgränsen för ett lagringskonto.

## <a name="common-uses"></a>Vanliga användningsområden

Vanliga användningsområden för Queue Storage är:

* Skapa en lista med kvarvarande uppgifter att bearbeta asynkront
* Skicka meddelanden från en Azure-webbroll till en Azure-arbetsroll

## <a name="queue-service-concepts"></a>Kötjänst-koncept

Kötjänsten består av följande komponenter:

![Kö-begrepp](./media/storage-queues-introduction/queue1.png)

* **URL-format:** Köer är adresserbara via följande URL-format:   
    https://`<storage account>`.queue.core.windows.net/`<queue>` 
  
    Följande URL adresserar en kö i diagrammet:  
  
    `https://myaccount.queue.core.windows.net/images-to-download`

* **Lagringskonto:** All åtkomst till Azure Storage görs genom ett lagringskonto. Se [Skalbarhets- och prestandamål för Azure Storage](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) för information om kapacitet för lagringskonton.

* **Kö:** En kö innehåller en uppsättning meddelanden. Alla meddelanden måste vara i en kö. Observera att könamnet måste vara helt i gemener. Mer information om namngivning av köer finns i [namngivning av köer och metadata](https://msdn.microsoft.com/library/azure/dd179349.aspx).

* **meddelande:** Ett meddelande i valfritt format, upp till 64 KB. Den maximala tid som ett meddelande kan finnas i kön är sju dagar.

## <a name="next-steps"></a>Nästa steg

* [Skapa ett lagringskonto](../storage-create-storage-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
* [Komma igång med köer med hjälp av .NET](storage-dotnet-how-to-use-queues.md)
