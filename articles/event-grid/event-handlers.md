---
title: Händelsehanterare i Azure Event Grid
description: Beskriver händelsehanterare som stöds för Azure Event Grid. Azure Automation, Funktioner, Event Hubs, Hybrid-anslutningar, Logic Apps, Service Bus, KöLagring, Webhooks.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 7ea00d663264e902c1818f7a4684e90eccd97b28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265056"
---
# <a name="event-handlers-in-azure-event-grid"></a>Händelsehanterare i Azure Event Grid

En händelsehanterare är den plats där händelsen skickas. Hanteraren vidtar ytterligare åtgärder för att bearbeta händelsen. Flera Azure-tjänster konfigureras automatiskt för att hantera händelser. Du kan också använda valfri WebHook för att hantera händelser. WebHook behöver inte vara värd i Azure för att hantera händelser. Event Grid stöder endast HTTPS WebHook-slutpunkter.

Den här artikeln innehåller länkar till innehåll för varje händelsehanterare.

## <a name="azure-automation"></a>Azure Automation

Använd Azure Automation för att bearbeta händelser med automatiska runbooks.

|Titel  |Beskrivning  |
|---------|---------|
|[Självstudiekurs: Azure Automation med Event Grid och Microsoft Teams](ensure-tags-exists-on-new-virtual-machines.md) |Skapa en virtuell dator som skickar en händelse. Händelsen utlöser en Automation-runbook som taggar den virtuella datorn och utlöser ett meddelande som skickas till en Microsoft Teams-kanal. |

## <a name="azure-functions"></a>Azure Functions

Använd Azure Functions för serverlös svar på händelser.

När du använder Azure Functions som hanterare kan du använda Event Grid-utlösaren i stället för allmänna HTTP-utlösare. Event Grid verifierar automatiskt Event Grid Function-utlösare. Med allmänna HTTP-utlösare måste du implementera [verifieringssvaret](security-authentication.md#webhook-event-delivery).

|Titel  |Beskrivning  |
|---------|---------|
| [Snabbstart: Hantera händelser med funktion](custom-event-to-function.md) | Skickar en anpassad händelse till en funktion för bearbetning. |
| [Utlösare av händelserutnät för Azure-funktioner](../azure-functions/functions-bindings-event-grid.md) | Översikt över hur du använder utlösaren för händelserutnät i Funktioner. |
| [Självstudiekurs: automatisera storleksändring av uppladdade bilder med Event Grid](resize-images-on-storage-blob-upload-event.md) | Användare laddar upp bilder via webbapp till lagringskonto. När en lagringsblob skapas skickar Event Grid en händelse till funktionsappen, som ändrar storlek på den uppladdade avbildningen. |
| [Självstudiekurs: strömma stordata till ett informationslager](event-grid-event-hubs-integration.md) | När Event Hubs skapar en capture-fil skickar Event Grid en händelse till en funktionsapp. Appen hämtar capture-filen och migrerar data till ett informationslager. |
| [Självstudiekurs: Exempel på integrering av Azure Service Bus till Azure Event Grid](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid skickar meddelanden från Service Bus-avsnittet för att fungera app och logikapp. |

## <a name="event-hubs"></a>Händelsehubbar

Använd eventhubbar när din lösning får händelser snabbare än den kan bearbeta händelserna. Ditt program bearbetar händelserna från Event Hubs enligt det egna schemat. Du kan skala händelsebearbetningen så att den hanterar inkommande händelser.

Event Hubs kan fungera som antingen en händelsekälla eller händelsehanterare. Följande artikel visar hur du använder Event Hubs som hanterare.

|Titel  |Beskrivning  |
|---------|---------|
| [Snabbstart: dirigera anpassade händelser till Azure Event Hubs med Azure CLI och Event Grid](custom-event-to-eventhub.md) | Skickar en anpassad händelse till en händelsehubb för bearbetning av ett program. |
| [Resurshanterarens mall: anpassade avsnitt och slutpunkt för händelsehubbar](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| En Resource Manager-mall som skapar en prenumeration för ett anpassat ämne. Den skickar händelser till en Azure Event Hubs. |

Exempel på händelsehubbar som källa finns i [källan Event Hubs](event-sources.md#event-hubs).

## <a name="hybrid-connections"></a>Hybridanslutningar

Använd Azure Relay Hybrid-anslutningar för att skicka händelser till program som finns i ett företagsnätverk och inte har en allmänt tillgänglig slutpunkt.

|Titel  |Beskrivning  |
|---------|---------|
| [Självstudiekurs: skicka händelser till hybridanslutning](custom-event-to-hybrid-connection.md) | Skickar en anpassad händelse till en befintlig hybridanslutning för bearbetning av ett avlyssnarprogram. |

## <a name="logic-apps"></a>Logic Apps

Använd Logic Apps för att automatisera affärsprocesser för att svara på händelser.

|Titel  |Beskrivning  |
|---------|---------|
| [Självstudiekurs: övervaka ändringar av virtuella datorer med Azure Event Grid och Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md) | En logikapp övervakar ändringar på en virtuell dator och skickar e-postmeddelanden om dessa ändringar. |
| [Självstudiekurs: skicka e-postmeddelanden om Azure IoT Hub-händelser med Hjälp av Logic Apps](publish-iot-hub-events-to-logic-apps.md) | En logikapp skickar ett e-postmeddelande varje gång en enhet läggs till i din IoT-hubb. |
| [Självstudiekurs: Exempel på integrering av Azure Service Bus till Azure Event Grid](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid skickar meddelanden från Service Bus-avsnittet för att fungera app och logikapp. |

## <a name="service-bus"></a>Service Bus

### <a name="service-bus-queues"></a>Service Bus-köer

Du kan dirigera händelser i Händelserutnät direkt till Service Bus-köer för användning i buffring eller kommando & kontrollscenarier i företagsprogram.

När du skapar en händelseprenumeration i Azure-portalen väljer du "Service Bus Queue" som slutpunktstyp och klickar sedan på "välj en slutpunkt" för att välja en servicebusskö.

#### <a name="using-cli-to-add-a-service-bus-queue-handler"></a>Använda CLI för att lägga till en servicebussköhanterare

För Azure CLI prenumererar och ansluter följande exempel ett händelserutnätsämne till en servicebusskö:

```azurecli-interactive
# If you haven't already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid event-subscription create \
    --name <my-event-subscription> \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1 \
    --endpoint-type servicebusqueue \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.ServiceBus/namespaces/ns1/queues/queue1
```

### <a name="service-bus-topics"></a>Service Bus ämnen

Du kan dirigera händelser i Händelserutnät direkt till Service Bus-ämnen för att hantera Azure-systemhändelser med Service Bus-ämnen eller för kommando & styra meddelandescenarier.

När du skapar en händelseprenumeration i Azure-portalen väljer du "Service Bus Topic" som slutpunktstyp och klickar sedan på "välj och slutpunkt" för att välja ett Service Bus-ämne.

#### <a name="using-cli-to-add-a-service-bus-topic-handler"></a>Använda CLI för att lägga till en servicebussämneshanterare

För Azure CLI prenumererar och ansluter följande exempel ett händelserutnätsämne till en servicebusskö:

```azurecli-interactive
# If you haven't already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid event-subscription create \
    --name <my-event-subscription> \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1 \
    --endpoint-type servicebustopic \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.ServiceBus/namespaces/ns1/topics/topic1
```

## <a name="queue-storage"></a>Queue Storage

Använd Kölagring för att ta emot händelser som måste hämtas. Du kan använda kölagring när du har en tidskrävande process som tar för lång tid att svara. Genom att skicka händelser till Kölagring kan appen hämta och bearbeta händelser enligt sitt eget schema.

|Titel  |Beskrivning  |
|---------|---------|
| [Snabbstart: dirigera anpassade händelser till Azure-kölagring med Azure CLI och Event Grid](custom-event-to-queue-storage.md) | Beskriver hur du skickar anpassade händelser till ett kölagringsutrymme. |

## <a name="webhooks"></a>WebHooks

Använd webhooks för anpassningsbara slutpunkter som svarar på händelser.

|Titel  |Beskrivning  |
|---------|---------|
| Snabbstart: skapa och dirigera anpassade händelser med - [Azure CLI,](custom-event-quickstart.md) [PowerShell](custom-event-quickstart-powershell.md)och [portal](custom-event-quickstart-portal.md). | Visar hur du skickar anpassade händelser till en WebHook. |
| Snabbstart: dirigera Blob-lagringshändelser till en anpassad webbslutpunkt med - [Azure CLI,](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) [PowerShell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json)och [portal](blob-event-quickstart-portal.md). | Visar hur du skickar blob-lagringshändelser till en WebHook. |
| [Snabbstart: skicka händelser för behållarregister](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Visar hur du använder Azure CLI för att skicka containerregisterhändelser. |
| [Översikt: ta emot händelser till en HTTP-slutpunkt](receive-events.md) | Beskriver hur du validerar en HTTP-slutpunkt för att ta emot händelser från en händelseprenumeration och ta emot och avserialisera händelser. |

## <a name="next-steps"></a>Nästa steg

* En introduktion till Event Grid finns i [Om Event Grid](overview.md).
* Information om hur du snabbt kommer igång med Event Grid finns i [Skapa och dirigera anpassade händelser med Azure Event Grid](custom-event-quickstart.md).
