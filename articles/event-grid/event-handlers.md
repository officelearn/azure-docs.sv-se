---
title: Azure Event Grid händelse hanterare
description: Beskriver händelse hanterare som stöds för Azure Event Grid
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: spelluru
ms.openlocfilehash: 279d7f2ac6481f3aa3ebd8e5a18a52b9e52f6201
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2019
ms.locfileid: "74169307"
---
# <a name="event-handlers-in-azure-event-grid"></a>Händelse hanterare i Azure Event Grid

En händelse hanterare är den plats där händelsen skickas. Hanteraren vidtar ytterligare åtgärder för att bearbeta händelsen. Flera Azure-tjänster konfigureras automatiskt för att hantera händelser. Du kan också använda en webhook för att hantera händelser. Webhooken behöver inte vara värd för Azure för att hantera händelser. Event Grid stöder endast HTTPS webhook-slutpunkter.

Den här artikeln innehåller länkar till innehåll för varje händelse hanterare.

## <a name="azure-automation"></a>Azure Automation

Använd Azure Automation för att bearbeta händelser med automatiserade Runbooks.

|Rubrik  |Beskrivning  |
|---------|---------|
|[Självstudie: Azure Automation med Event Grid och Microsoft Teams](ensure-tags-exists-on-new-virtual-machines.md) |Skapa en virtuell dator, som skickar en händelse. Händelsen utlöser en Automation-Runbook som Taggar den virtuella datorn och utlöser ett meddelande som skickas till en Microsoft Teams-kanal. |

## <a name="azure-functions"></a>Azure Functions

Använd Azure Functions för Server lös svar på händelser.

När du använder Azure Functions som hanterare kan du använda Event Grid-utlösaren i stället för allmänna HTTP-utlösare. Event Grid verifierar automatiskt Event Grid Function-utlösare. Med allmänna HTTP-utlösare måste du implementera [verifieringssvaret](security-authentication.md#webhook-event-delivery).

|Rubrik  |Beskrivning  |
|---------|---------|
| [Snabb start: hantera händelser med funktion](custom-event-to-function.md) | Skickar en anpassad händelse till en funktion för bearbetning. |
| [Event Grid utlösare för Azure Functions](../azure-functions/functions-bindings-event-grid.md) | Översikt över hur du använder Event Grid-utlösaren i functions. |
| [Självstudie: automatisera storleks ändring av överförda bilder med hjälp av Event Grid](resize-images-on-storage-blob-upload-event.md) | Användare laddar upp bilder via webbapp till lagrings kontot. När en lagrings-BLOB skapas skickar Event Grid en händelse till Function-appen, som ändrar storlek på den överförda avbildningen. |
| [Självstudie: strömma Big data till ett informations lager](event-grid-event-hubs-integration.md) | När Event Hubs skapar en Infångnings fil, Event Grid skickar en händelse till en Function-app. Appen hämtar insamlings filen och migrerar data till ett data lager. |
| [Självstudie: Azure Service Bus till Azure Event Grid integrations exempel](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid skickar meddelanden från Service Bus ämne till att fungera som app-och Logic-appen. |

## <a name="event-hubs"></a>Event Hubs

Använd Event Hubs när din lösning hämtar händelser snabbare än den kan bearbeta händelserna. Programmet bearbetar händelserna från Event Hubs enligt schemat. Du kan skala händelse bearbetningen för att hantera inkommande händelser.

Event Hubs kan fungera som en händelse källa eller händelse hanterare. Följande artikel visar hur du använder Event Hubs som hanterare.

|Rubrik  |Beskrivning  |
|---------|---------|
| [Snabb start: dirigera anpassade händelser till Azure Event Hubs med Azure CLI och Event Grid](custom-event-to-eventhub.md) | Skickar en anpassad händelse till en händelsehubben för bearbetning av ett program. |
| [Resource Manager-mall: anpassat ämne och Event Hubs slut punkt](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| En Resource Manager-mall som skapar en prenumeration för ett anpassat ämne. Den skickar händelser till en Azure-Event Hubs. |

Exempel på Event Hubs som källa finns i [Event Hubs källa](event-sources.md#event-hubs).

## <a name="hybrid-connections"></a>Hybridanslutningar

Använd Azure Relay Hybridanslutningar för att skicka händelser till program som finns i ett företags nätverk och inte har en offentligt tillgänglig slut punkt.

|Rubrik  |Beskrivning  |
|---------|---------|
| [Självstudie: skicka händelser till hybrid anslutning](custom-event-to-hybrid-connection.md) | Skickar en anpassad händelse till en befintlig hybrid anslutning för bearbetning av ett lyssnar program. |

## <a name="logic-apps"></a>Logic Apps

Använd Logic Apps för att automatisera affärs processer för att svara på händelser.

|Rubrik  |Beskrivning  |
|---------|---------|
| [Självstudie: övervaka ändringar av virtuella datorer med Azure Event Grid och Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md) | En Logic app övervakar ändringar i en virtuell dator och skickar e-postmeddelanden om ändringarna. |
| [Självstudie: skicka e-postaviseringar om Azure IoT Hub-händelser med hjälp av Logic Apps](publish-iot-hub-events-to-logic-apps.md) | En Logic App skickar ett e-postmeddelande varje gång en enhet läggs till i IoT Hub. |
| [Självstudie: Azure Service Bus till Azure Event Grid integrations exempel](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid skickar meddelanden från Service Bus ämne till att fungera som app-och Logic-appen. |

## <a name="service-bus"></a>Service Bus

### <a name="service-bus-queues"></a>Service Bus-köer

Du kan dirigera händelser i Event Grid direkt till Service Bus köer för användning i buffert-eller kommando & kontroll scenarier i företags program.

När du skapar en händelse prenumeration i Azure Portal väljer du Service Bus kö som typ av slut punkt och klickar sedan på Välj och slut punkt för att välja en Service Bus kö.

#### <a name="using-cli-to-add-a-service-bus-queue-handler"></a>Använda CLI för att lägga till en Service Bus Queue-hanterare

I följande exempel prenumererar och ansluter ett event Grid-ämne till en Service Bus kö för Azure CLI:

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

### <a name="service-bus-topics"></a>Service Bus-ämnen

Du kan dirigera händelser i Event Grid direkt till Service Bus ämnen för att hantera Azure system Events med Service Bus ämnen, eller för kommando & kontroll meddelande scenarier.

När du skapar en händelse prenumeration i Azure Portal väljer du "Service Bus ämne" som typ av slut punkt och klickar sedan på Välj och slut punkt för att välja ett Service Bus ämne.

#### <a name="using-cli-to-add-a-service-bus-topic-handler"></a>Använda CLI för att lägga till en Service Bus ämnes hanterare

I följande exempel prenumererar och ansluter ett event Grid-ämne till en Service Bus kö för Azure CLI:

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

Använd Queue Storage för att ta emot händelser som behöver hämtas. Du kan använda Queue Storage när du har en lång körnings process som tar för lång tid att svara. Genom att skicka händelser till Queue Storage kan appen Hämta och bearbeta händelser enligt sitt eget schema.

|Rubrik  |Beskrivning  |
|---------|---------|
| [Snabb start: dirigera anpassade händelser till Azure Queue Storage med Azure CLI och Event Grid](custom-event-to-queue-storage.md) | Beskriver hur du skickar anpassade händelser till en Queue Storage. |

## <a name="webhooks"></a>WebHooks

Använd Webhooks för anpassningsbara slut punkter som svarar på händelser.

|Rubrik  |Beskrivning  |
|---------|---------|
| Snabb start: skapa och dirigera anpassade händelser med- [Azure CLI](custom-event-quickstart.md), [PowerShell](custom-event-quickstart-powershell.md)och [Portal](custom-event-quickstart-portal.md). | Visar hur du skickar anpassade händelser till en webhook. |
| Snabb start: dirigera Blob Storage-händelser till en anpassad webb slut punkt med- [Azure CLI](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json), [PowerShell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json)och [Portal](blob-event-quickstart-portal.md). | Visar hur du skickar Blob Storage-händelser till en webhook. |
| [Snabb start: skicka behållar register händelser](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Visar hur du använder Azure CLI för att skicka Container Registry händelser. |
| [Översikt: ta emot händelser till en HTTP-slutpunkt](receive-events.md) | Beskriver hur du verifierar en HTTP-slutpunkt för att ta emot händelser från en händelse prenumeration och ta emot och deserialisera händelser. |

## <a name="next-steps"></a>Nästa steg

* En introduktion till Event Grid finns i [Om Event Grid](overview.md).
* Kom igång snabbt med Event Grid, se [skapa och dirigera anpassade händelser med Azure Event Grid](custom-event-quickstart.md).
