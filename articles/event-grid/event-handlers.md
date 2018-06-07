---
title: Azure händelse rutnätet händelsehanterare
description: Beskriver stöds händelsehanterare för rutnät för Azure-händelse
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/04/2018
ms.author: tomfitz
ms.openlocfilehash: 24cecdc65c45d5d1ee5443740d9874ccfd74e387
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34627723"
---
# <a name="event-handlers-in-azure-event-grid"></a>Händelsehanterare i rutnätet för Azure-händelse

En händelsehanterare är den plats där händelsen att skickas. Hanteraren tar vissa ytterligare åtgärder för att bearbeta händelsen. Flera Azure-tjänster konfigureras automatiskt för att hantera händelser. Du kan också använda en webhook för att hantera händelser. Webhooken behöver inte finnas i Azure för att hantera händelser. Händelsen rutnätet har endast stöd för HTTPS webhook-slutpunkter.

Den här artikeln innehåller länkar till innehåll för varje händelsehanterare.

## <a name="azure-automation"></a>Azure Automation

Använd Azure Automation för att bearbeta händelser med automatisk runbooks.

|Titel  |Beskrivning  |
|---------|---------|
|[Integrera Azure Automation med händelsen rutnätet och Microsoft team](ensure-tags-exists-on-new-virtual-machines.md) |Skapa en virtuell dator som skickar en händelse. Händelsen utlöses en Automation-runbook som taggar den virtuella datorn och utlöser ett meddelande som skickas till en Microsoft-Teams-kanal. |

## <a name="azure-functions"></a>Azure Functions

Använd Azure Functions för serverlösa svar på händelser.

När du använder Azure Functions som hanterare kan du använda Event Grid-utlösaren i stället för allmänna HTTP-utlösare. Event Grid verifierar automatiskt Event Grid Function-utlösare. Med allmänna HTTP-utlösare måste du implementera [verifieringssvaret](security-authentication.md#webhook-event-delivery).

|Titel  |Beskrivning  |
|---------|---------|
| [Händelseutlösare rutnät för Azure Functions](../azure-functions/functions-bindings-event-grid.md) | Översikt över användning av händelse rutnätet utlösaren i funktioner. |
| [Automatisera storleksändring av överförda bilder med Event Grid](resize-images-on-storage-blob-upload-event.md) | Användare ladda upp bilder via webbapp till storage-konto. När en lagringsblob skapas skickar rutnätet händelse en händelse till funktionsapp som ändrar storlek på den överförda avbildningen. |
| [Strömma stordata till ett datalager](event-grid-event-hubs-integration.md) | När Händelsehubbar skapar en Capture-fil, skickar en händelse till en funktionsapp händelse rutnätet. Appen hämtar filen avbildning och migrerar data till data warehouse. |
| [Azure Service Bus Azure händelse rutnätet integration exempel](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Händelsen rutnätet skickar meddelanden från Service Bus-ämne app och logikapp. |

## <a name="hybrid-connections"></a>Hybridanslutningar

Använd Azure Relay Hybridanslutningar för att skicka händelser till program som finns inom ett företagsnätverk och inte har en offentligt tillgänglig slutpunkt.

|Titel  |Beskrivning  |
|---------|---------|
| [Skicka händelser hybridanslutning](custom-event-to-hybrid-connection.md) | Skickar en anpassad händelse till en befintlig hybridanslutning för bearbetning av ett lyssnarprogram för. |

## <a name="logic-apps"></a>Logic Apps

Använd Logic Apps automatisera affärsprocesser för att svara på händelser.

|Titel  |Beskrivning  |
|---------|---------|
| [Övervaka ändringar på virtuella maskiner med Azure Event Grid och Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md) | En logikapp övervakar ändringar till en virtuell dator och skickar e-postmeddelanden om dessa ändringar. |
| [Skicka e-postmeddelanden om Azure IoT Hub-händelser med hjälp av Logic Apps](publish-iot-hub-events-to-logic-apps.md) | En logikapp skickar ett e-postmeddelande varje gång en enhet har lagts till i din IoT-hubb. |
| [Azure Service Bus Azure händelse rutnätet integration exempel](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Händelsen rutnätet skickar meddelanden från Service Bus-ämne app och logikapp. |

## <a name="queue-storage"></a>Queue Storage

Använda Queue storage för att ta emot händelser som behöver ska hämtas.

|Titel  |Beskrivning  |
|---------|---------|
| [Vidarebefordra anpassade händelser till Azure Queue storage med Azure CLI och händelsen rutnätet](custom-event-to-queue-storage.md) | Beskriver hur du skickar anpassade händelser till en Queue storage. |

## <a name="webhooks"></a>WebHooks

Använd webhooks för anpassningsbara slutpunkter som svara på händelser.

|Titel  |Beskrivning  |
|---------|---------|
| [Ta emot händelser till en HTTP-slutpunkt](receive-events.md) | Beskriver hur du verifierar en HTTP-slutpunkt för att ta emot händelser från en händelse-prenumeration och ta emot och deserialisera händelser. |

## <a name="next-steps"></a>Nästa steg

* En introduktion till Event Grid finns i [Om Event Grid](overview.md).
* Om du vill komma igång snabbt med hjälp av händelse rutnätet kan se [skapa och flöde anpassade händelser med Azure händelse rutnätet](custom-event-quickstart.md).
