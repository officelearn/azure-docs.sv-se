---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/09/2018
ms.openlocfilehash: 89c2467843d7abc7c005804fd5263fe3beb668b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "74793435"
---
Om du vill uppskatta mer exakta förbrukningskostnader bör du tänka på det möjliga antalet meddelanden eller händelser som kan komma fram på en viss dag, i stället för att basera beräkningarna på endast avsökningsintervallet. När en händelse eller ett meddelande uppfyller utlösarvillkoren försöker många utlösare omedelbart att läsa alla andra väntande händelser eller meddelanden som uppfyller villkoren. Det innebär att även när du väljer ett längre avsökningsintervall utlöses utlösaren baserat på antalet väntande händelser eller meddelanden som är kvalificerade för startarbetsflöden. Utlösare som följer det här beteendet inkluderar Azure Service Bus och Azure Event Hub.

Anta till exempel att du ställer in utlösare som kontrollerar en slutpunkt varje dag. När utlösaren kontrollerar slutpunkten och hittar 15 händelser som uppfyller villkoren utlöses utlösaren och körs motsvarande arbetsflöde 15 gånger. Logic Apps mäter alla åtgärder som dessa 15 arbetsflöden utför, inklusive utlösarbegäranden. Om du vill beräkna dina potentiella kostnader provar du [Azure-priskalkylatorn](https://azure.microsoft.com/pricing/calculator/).