---
ms.openlocfilehash: 3fa71085d649ace95aa24ac87c8714a7268f5386
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60598480"
---
För att beräkna kostnaderna för exaktare förbrukning måste du överväga att möjliga antalet meddelanden eller händelser som kan tas emot på en viss dag, snarare än basera din beräkningar på endast avsökningsintervall. När en händelse eller meddelandet uppfyller villkor för utlösare, försöker många utlösare omedelbart läsa eventuella och alla andra väntande händelser eller meddelanden som uppfyller villkoren. Detta innebär att även när du väljer en längre avsökningsintervallet, utlöses baserat på antalet väntande händelser eller meddelanden som uppfyller kraven för att starta arbetsflöden. Utlösare som följer det här beteendet är Azure Service Bus och Azure Event Hub.

Därför Anta exempelvis att du ställer in utlösare som kontrollerar varje dag för en slutpunkt. När utlösaren kontrollerar slutpunkten och söker efter 15 händelser som uppfyller villkoren, utlöses utlösaren och kör motsvarande arbetsflödet 15 gånger. Logic Apps-mätare alla åtgärder som de 15 arbetsflödena utföra, inklusive utlösarbegäranden. Om du vill beräkna dina kostnader för potentiell testa den [Azures priskalkylator](https://azure.microsoft.com/pricing/calculator/).