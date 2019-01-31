---
title: Introduktion till Azure Queue storage | Microsoft Docs
description: Introduktion till Azure Queue storage
services: storage
author: tamram
ms.service: storage
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/07/2017
ms.author: tamram
ms.subservice: queues
ms.openlocfilehash: b173934db17b8c3ac5a48e599b75478fb214c240
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55458405"
---
# <a name="introduction-to-queues"></a>Introduktion till köer

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
