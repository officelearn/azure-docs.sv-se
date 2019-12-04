---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/09/2018
ms.openlocfilehash: 89c2467843d7abc7c005804fd5263fe3beb668b6
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74793435"
---
Om du vill beräkna mer exakta förbruknings kostnader bör du ta hänsyn till det möjliga antalet meddelanden eller händelser som kan komma att komma till en bestämd dag, i stället för att basera dina beräkningar enbart på avsöknings intervallet. När en händelse eller ett meddelande uppfyller utlösarens villkor försöker många utlösare omedelbart läsa eventuella och alla andra väntande händelser eller meddelanden som uppfyller villkoren. Det innebär att även om du väljer ett längre avsöknings intervall utlöses utlösaren baserat på antalet väntande händelser eller meddelanden som är kvalificerade för start av arbets flöden. Utlösare som följer detta beteende är Azure Service Bus och Azure Event Hub.

Anta till exempel att du konfigurerar utlösare som kontrollerar en slut punkt varje dag. När utlösaren kontrollerar slut punkten och hittar 15 händelser som uppfyller villkoren, utlöses utlösaren och kör motsvarande arbets flöde 15 gånger. Logic Apps mäta alla åtgärder som dessa 15 arbets flöden utför, inklusive utlösare begär Anden. Testa [pris Kalkylatorn för Azure](https://azure.microsoft.com/pricing/calculator/)för att beräkna dina potentiella kostnader.