---
title: Azure Event Grid händelse hanterare
description: Beskriver händelse hanterare som stöds för Azure Event Grid. Azure Automation, funktioner, Event Hubs, Hybridanslutningar, Logic Apps, Service Bus, Queue Storage, Webhooks.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: c29526bdee5ad70b3acb23117ee003877f52a55e
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592465"
---
# <a name="event-handlers-in-azure-event-grid"></a>Händelse hanterare i Azure Event Grid
En händelse hanterare är den plats där händelsen skickas. Hanteraren vidtar ytterligare åtgärder för att bearbeta händelsen. Flera Azure-tjänster konfigureras automatiskt för att hantera händelser. Du kan också använda en webhook för att hantera händelser. Webhooken behöver inte vara värd för Azure för att hantera händelser. Event Grid stöder endast HTTPS webhook-slutpunkter.

## <a name="supported-event-handlers"></a>Händelse hanterare som stöds
Följande är de händelse hanterare som stöds: 

- [Webhooks](handler-webhooks.md). Azure Automation runbooks och Logic Apps stöds via Webhooks. 
- [Azure Functions](handler-functions.md)
- [Event Hub](handler-event-hubs.md)
- [Relä hybrid anslutningar](handler-relay-hybrid-connections.md)
- [Service Bus köer och ämnen](handler-service-bus.md)
- [Lagrings köer](handler-storage-queues.md)

## <a name="next-steps"></a>Nästa steg
- En introduktion till Event Grid finns i [Om Event Grid](overview.md).
