---
title: Vidarebefordra hybrid anslutning som en händelse hanterare för Azure Event Grid händelser
description: Beskriver hur du kan använda Azure Relay hybrid anslutningar som händelse hanterare för Azure Event Grid händelser.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: spelluru
ms.openlocfilehash: 04fbc7b739fa8ea7b08a3341c2f78244c445e721
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "83800885"
---
# <a name="relay-hybrid-connection-as-an-event-handler-for-azure-event-grid-events"></a>Vidarebefordra hybrid anslutning som en händelse hanterare för Azure Event Grid händelser
En händelse hanterare är den plats där händelsen skickas. Hanteraren vidtar ytterligare åtgärder för att bearbeta händelsen. Flera Azure-tjänster konfigureras automatiskt för att hantera händelser och **Azure Relay** är en av dem. 

Använd Azure **Relay-hybridanslutningar** för att skicka händelser till program som finns i ett företags nätverk och inte har en offentligt tillgänglig slut punkt.

## <a name="tutorials"></a>Självstudier
I följande självstudie finns ett exempel på hur du använder en Azure Relay hybrid anslutning som händelse hanterare. 

|Titel  |Beskrivning  |
|---------|---------|
| [Självstudie: skicka händelser till hybrid anslutning](custom-event-to-hybrid-connection.md) | Skickar en anpassad händelse till en befintlig hybrid anslutning för bearbetning av ett lyssnar program. |

## <a name="rest-example-for-put"></a>REST-exempel (för placering)

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "HybridConnection",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Relay/namespaces/<RELAY NAMESPACE NAME>/hybridconnections/<HYBRID CONNECTION NAME>"
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="next-steps"></a>Nästa steg
En lista över händelse hanterare som stöds finns i artikeln [händelse hanterare](event-handlers.md) . 