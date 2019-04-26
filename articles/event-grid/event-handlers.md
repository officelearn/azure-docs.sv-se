---
title: Azure Event Grid-händelsehanterare
description: Beskriver stöds händelsehanterare för Azure Event Grid
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2019
ms.author: spelluru
ms.openlocfilehash: 33604a16f5895e20d4475d1dd8b27c34184feb72
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60345489"
---
# <a name="event-handlers-in-azure-event-grid"></a>Händelsehanterare i Azure Event Grid

En händelsehanterare är den plats där händelsen skickas. Hanteraren tar några ytterligare åtgärder för att bearbeta händelsen. Flera Azure-tjänster konfigureras automatiskt för att hantera händelser. Du kan också använda en WebHook för att hantera händelser. Webhooken behöver inte finnas i Azure för att hantera händelser. Event Grid stöder endast WebHook för HTTPS-slutpunkter.

Den här artikeln innehåller länkar till innehåll för varje händelsehanterare.

## <a name="azure-automation"></a>Azure Automation

Använd Azure Automation för att bearbeta händelser med automatiserade runbooks.

|Rubrik  |Beskrivning  |
|---------|---------|
|[Självstudie: Azure Automation med Event Grid och Microsoft Teams](ensure-tags-exists-on-new-virtual-machines.md) |Skapa en virtuell dator som skickar en händelse. Händelsen utlöses en Automation-runbook som den virtuella datorn med taggar och utlöser ett meddelande som skickas till en Microsoft Teams-kanal. |

## <a name="azure-functions"></a>Azure Functions

Använd Azure Functions för serverlös svar på händelser.

När du använder Azure Functions som hanterare kan du använda Event Grid-utlösaren i stället för allmänna HTTP-utlösare. Event Grid verifierar automatiskt Event Grid Function-utlösare. Med allmänna HTTP-utlösare måste du implementera [verifieringssvaret](security-authentication.md#webhook-event-delivery).

|Rubrik  |Beskrivning  |
|---------|---------|
| [Event Grid-utlösare för Azure Functions](../azure-functions/functions-bindings-event-grid.md) | Översikt över användning av Event Grid-utlösaren i funktioner. |
| [Självstudie: automatisera storleksändring av överförda bilder med Event Grid](resize-images-on-storage-blob-upload-event.md) | Användare ladda upp bilder genom webbapp till storage-konto. När en lagringsblob skapas, skickar en händelse i Event Grid till funktionsapp, som ändrar storlek på den uppladdade avbildningen. |
| [Självstudie: strömma stordata till ett data warehouse](event-grid-event-hubs-integration.md) | När Händelsehubbar skapar en avbildning, skickar en händelse i Event Grid till en funktionsapp. Appen hämtar filen avbildning och migrerar data till ett data warehouse. |
| [Självstudie: Azure Service Bus till Azure Event Grid integrationsexempel](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid skickar meddelanden från Service Bus-ämne app och logikapp. |

## <a name="event-hubs"></a>Event Hubs

Använda Event Hubs när din lösning hämtar händelser snabbare än den kan bearbeta händelser. Ditt program bearbetar händelser från Event Hubs på den egna schema. Du kan skala dina händelsebearbetning för att hantera inkommande händelser.

Händelsehubbar fungerar som en händelsekälla eller händelsehanterare. I följande artikel visar hur du använder Event Hubs som en hanterare.

|Rubrik  |Beskrivning  |
|---------|---------|
| [Snabbstart: Dirigera anpassade händelser till Azure Event Hubs med Azure CLI och Event Grid](custom-event-to-eventhub.md) | Skickar en anpassad händelse till en händelsehubb för bearbetning av ett program. |
| [Resource Manager-mall: anpassat ämne och Event Hubs-slutpunkt](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| En Resource Manager-mall som skapar en prenumeration för ett anpassat ämne. Den skickar händelser till en Azure Event Hubs. |

Exempel på Händelsehubbar som en källa finns [Händelsehubbar källa](event-sources.md#event-hubs).

## <a name="hybrid-connections"></a>Hybridanslutningar

Använd Azure Relay-Hybridanslutningar för att skicka händelser till program som finns inom ett företagsnätverk och inte har en offentligt tillgänglig slutpunkt.

|Rubrik  |Beskrivning  |
|---------|---------|
| [Självstudie: skicka händelser till hybridanslutning](custom-event-to-hybrid-connection.md) | Skickar en anpassad händelse till en befintlig hybridanslutning för bearbetning av ett lyssnarprogram. |

## <a name="logic-apps"></a>Logic Apps

Använda Logic Apps automatisera affärsprocesser för att svara på händelser.

|Rubrik  |Beskrivning  |
|---------|---------|
| [Självstudie: övervaka ändringar av virtuell dator med Azure Event Grid och Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md) | En logikapp övervakar ändringar till en virtuell dator och skickar e-postmeddelanden om dessa ändringar. |
| [Självstudie: skicka e-postaviseringar om Azure IoT Hub-händelser med hjälp av Logic Apps](publish-iot-hub-events-to-logic-apps.md) | En logikapp skickar ett e-postmeddelande varje gång som en enhet har lagts till i din IoT-hubb. |
| [Självstudie: Azure Service Bus till Azure Event Grid integrationsexempel](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid skickar meddelanden från Service Bus-ämne app och logikapp. |

## <a name="queue-storage"></a>Queue Storage

Använd Queue storage för att ta emot händelser som behöver hämtas. Du kan använda Queue storage när du har en tidskrävande process som tar för lång tid att svara. Genom att skicka händelser till Queue storage appen kan hämta och bearbeta händelser i sitt eget schema.

|Rubrik  |Beskrivning  |
|---------|---------|
| [Snabbstart: Dirigera anpassade händelser till Azure Queue storage med Azure CLI och Event Grid](custom-event-to-queue-storage.md) | Beskriver hur du skickar anpassade händelser till en Queue storage. |

## <a name="webhooks"></a>WebHooks

Använda webhooks för anpassningsbara slutpunkter som svarar på aktiviteter.

|Rubrik  |Beskrivning  |
|---------|---------|
| Snabbstart: skapa och dirigera anpassade händelser med - [Azure CLI](custom-event-quickstart.md), [PowerShell](custom-event-quickstart-powershell.md), och [portal](custom-event-quickstart-portal.md). | Lär dig mer om att skicka anpassade händelser till en WebHook. |
| Snabbstart: Dirigera Blob storage-händelser till en anpassad webbslutpunkt med - [Azure CLI](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json), [PowerShell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json), och [portal](blob-event-quickstart-portal.md). | Visar hur du skickar blob storage-händelser till en WebHook. |
| [Snabbstart: skicka händelser för container registry](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Visar hur du använder Azure CLI för att skicka händelser för Container Registry. |
| [Översikt: ta emot händelser till en HTTP-slutpunkt](receive-events.md) | Beskriver hur du verifierar en HTTP-slutpunkt för att ta emot händelser från en händelseprenumeration och ta emot och deserialisera händelserna. |

## <a name="next-steps"></a>Nästa steg

* En introduktion till Event Grid finns i [Om Event Grid](overview.md).
* Kom igång snabbt med Event Grid, se [skapa och dirigera anpassade händelser med Azure Event Grid](custom-event-quickstart.md).
