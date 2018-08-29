---
title: Azure Event Grid-händelsekällor
description: Beskriver stöds händelsekällor för Azure Event Grid
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: conceptual
ms.date: 08/21/2018
ms.author: tomfitz
ms.openlocfilehash: 15fe97e39fbc1c669762ddb7523cfd043638d3de
ms.sourcegitcommit: a1140e6b839ad79e454186ee95b01376233a1d1f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2018
ms.locfileid: "43144237"
---
# <a name="event-sources-in-azure-event-grid"></a>Händelsekällor i Azure Event Grid

En händelsekälla är där händelsen inträffar. Flera Azure-tjänster konfigureras automatiskt för att skicka händelser. Du kan också skapa anpassade program som skickar händelser. Anpassade program behöver inte finnas i Azure för att använda Event Grid för händelsedistribution.

Den här artikeln innehåller länkar till innehåll för varje händelsekälla.

## <a name="azure-subscriptions"></a>Azure-prenumerationer

Prenumerera på händelser för Azure-prenumerationer att reagera på förändringar resurser i en Azure-prenumeration.

|Titel |Beskrivning  |
|---------|---------|
| [Självstudier: Azure Automation med Event Grid och Microsoft Teams](ensure-tags-exists-on-new-virtual-machines.md) |Skapa en virtuell dator som skickar en händelse. Händelsen utlöses en Automation-runbook som den virtuella datorn med taggar och utlöser ett meddelande som skickas till en Microsoft Teams-kanal. |
| [Så här: prenumerera på händelser via portalen](subscribe-through-portal.md) | Använda portalen för att prenumerera på händelser för en Azure-prenumeration. |
| [Azure CLI: prenumerera på händelser för en Azure-prenumeration](./scripts/event-grid-cli-azure-subscription.md) |Exempelskript som skapar en Event Grid-prenumeration till en Azure-prenumeration och skickar händelser till en WebHook. |
| [PowerShell: prenumerera på händelser för en Azure-prenumeration](./scripts/event-grid-powershell-azure-subscription.md)| Exempelskript som skapar en Event Grid-prenumeration till en Azure-prenumeration och skickar händelser till en WebHook. |
| [Händelseschema](event-schema-subscriptions.md) | Visar fälten i Azure-prenumerationshändelser. |

## <a name="container-registry"></a>Container Registry

Prenumerera på händelser för Container Registry att reagera på förändringar i bilder.

|Titel |Beskrivning  |
|---------|---------|
| [Snabbstart: skicka händelser för container registry](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Visar hur du använder Azure CLI för att skicka händelser för Container Registry. |
| [Händelseschema](event-schema-container-registry.md) | Visar fälten i Container Registry-händelser. |

## <a name="custom-topics"></a>Anpassade ämnen

Prenumerera på anpassade ämnen att svara på händelser för programmet.

|Titel  |Beskrivning  |
|---------|---------|
| [Snabbstart: skapa och dirigera anpassade händelser med Azure CLI](custom-event-quickstart.md) | Visar hur du använder Azure CLI för att skicka anpassade händelser. |
| [Snabbstart: skapa och dirigera anpassade händelser med Azure PowerShell](custom-event-quickstart-powershell.md) | Visar hur du använder Azure PowerShell för att skicka anpassade händelser. |
| [Snabbstart: skapa och dirigera anpassade händelser med Azure-portalen](custom-event-quickstart-portal.md) | Visar hur du använder portalen för att skicka anpassade händelser. |
| [Snabbstart: Dirigera anpassade händelser till Azure Queue storage](custom-event-to-queue-storage.md) | Beskriver hur du skickar anpassade händelser till en Queue storage. |
| [Så här: Skicka till anpassat ämne](post-to-custom-topic.md) | Visar hur man publicerar en händelse till ett anpassat ämne. |
| [Azure CLI: Skapa anpassat Event Grid-ämne](./scripts/event-grid-cli-create-custom-topic.md)|Exempelskript som skapar ett anpassat ämne. Skriptet hämtar slutpunkten och en nyckel.|
| [Azure CLI: prenumerera på händelser för ett anpassat ämne](./scripts/event-grid-cli-subscribe-custom-topic.md)|Exempelskript som skapar en prenumeration för ett anpassat ämne. Den skickar händelser till en WebHook.|
| [PowerShell: Skapa anpassat Event Grid-ämne](./scripts/event-grid-powershell-create-custom-topic.md)|Exempelskript som skapar ett anpassat ämne. Skriptet hämtar slutpunkten och en nyckel.|
| [PowerShell: prenumerera på händelser för ett anpassat ämne](./scripts/event-grid-powershell-subscribe-custom-topic.md)|Exempelskript som skapar en prenumeration för ett anpassat ämne. Den skickar händelser till en WebHook.|
| [Resource Manager-mall: anpassat ämne och WebHook-slutpunkt](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid) | En Resource Manager-mall som skapar ett anpassat ämne och en prenumeration för det anpassade ämnet. Den skickar händelser till en WebHook. |
|
| [Resource Manager-mall: anpassat ämne och Event Hubs-slutpunkt](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/subscribeCustomTopicToEventHub.json)| En Resource Manager-mall som skapar en prenumeration för ett anpassat ämne. Den skickar händelser till en Azure Event Hubs. |
| [Händelseschema](event-schema.md) | Visar fälten i anpassade händelser. |

## <a name="event-hubs"></a>Event Hubs

Prenumerera på händelser för Event Hubs svara för att samla in händelser för filen. Händelsehubbar fungerar som en händelsekälla eller händelsehanterare. I följande artiklar visar hur du använder Event Hubs som källa.

|Titel  |Beskrivning  |
|---------|---------|
| [Självstudie: strömma stordata till ett data warehouse](event-grid-event-hubs-integration.md) | När Händelsehubbar skapar en avbildning, skickar en händelse i Event Grid till en funktionsapp. Appen hämtar filen avbildning och migrerar data till ett data warehouse. |
| [Händelseschema](event-schema-event-hubs.md) | Visar fälten i Event Hubs-händelser. |

Exempel på Händelsehubbar som en hanterare finns [Händelsehubbar hanteraren](event-handlers.md#event-hubs).

## <a name="iot-hub"></a>IoT Hub

Prenumerera på IoT Hub-händelser att svara på en enhet har skapats, har tagits bort, ansluten och frånkopplade händelser.

|Titel  |Beskrivning  |
|---------|---------|
| [Skicka e-postaviseringar om Azure IoT Hub-händelser med hjälp av Logic Apps](publish-iot-hub-events-to-logic-apps.md) | En logikapp skickar ett e-postmeddelande varje gång som en enhet har lagts till i din IoT-hubb. |
| [Reagera på IoT Hub-händelser med Event Grid för att utlösaråtgärder](../iot-hub/iot-hub-event-grid.md) | Översikt över integrering av IoT Hub med Event Grid. |
| [Händelseschema](event-schema-iot-hub.md) | Visar fälten i IoT Hub-händelser. |
| [Ordna enheten ansluten och enheten frånkopplad händelser](../iot-hub/iot-hub-how-to-order-connection-state-events.md) | Lär dig mer om att ordna enhetsanslutning händelser. |

## <a name="media-services"></a>Media Services

Prenumerera på händelser för Media Services att svara på händelser för jobbet.

|Titel  |Beskrivning  |
|---------|---------|
| [Översikt: reagera på Media Services-händelser](../media-services/latest/reacting-to-media-services-events.md) | Översikt över integrering av Media Services med Event Grid. |
| [Självstudie: Dirigera Azure Media Services-händelser till en anpassad webbslutpunkt med CLI](../media-services/latest/job-state-events-cli-how-to.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Lär dig mer om att skicka händelser från Media Services. |
| [Händelseschema](../media-services/latest/media-services-event-schemas.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Visar fälten i Media Services-händelser. |

## <a name="resource-groups"></a>Resursgrupper

Prenumerera på resursen gruppera händelser att svara på förändringar i resurser i en resursgrupp.

|Titel  |Beskrivning  |
|---------|---------|
| [Självstudie: övervaka ändringar av virtuell dator med Azure Event Grid och Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md) | En logikapp övervakar ändringar till en virtuell dator och skickar e-postmeddelanden om dessa ändringar. |
| [Azure CLI: prenumerera på händelser för en resursgrupp](./scripts/event-grid-cli-resource-group.md)| Exempelskript som prenumererar på händelser för en resursgrupp. Den skickar händelser till en WebHook. |
| [Azure CLI: prenumerera på händelser för en resursgrupp och filter för en resurs](./scripts/event-grid-cli-resource-group-filter.md) | Exempelskriptet som prenumererar på händelser för en resursgrupp och filtrerar händelser för en resurs. |
| [PowerShell: prenumerera på händelser för en resursgrupp](./scripts/event-grid-powershell-resource-group.md) | Exempelskript som prenumererar på händelser för en resursgrupp. Den skickar händelser till en WebHook. |
| [PowerShell: prenumerera på händelser för en resursgrupp och filter för en resurs](./scripts/event-grid-powershell-resource-group-filter.md) | Exempelskriptet som prenumererar på händelser för en resursgrupp och filtrerar händelser för en resurs. |
| [Resource Manager-mall: prenumeration på resursgrupp](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/subscribeResourceGroupToWebHook.json) | Prenumererar på händelser för en resursgrupp. Den skickar händelser till en WebHook. |
| [Händelseschema](event-schema-resource-groups.md) | Visar fälten i resursen gruppera händelser. |

## <a name="service-bus"></a>Service Bus

Prenumerera på Service Bus-händelser ska svara på meddelanden utan någon aktiv lyssnare.

|Titel  |Beskrivning  |
|---------|---------|
| [Självstudier: Azure Service Bus till Azure Event Grid integrationsexempel](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid skickar meddelanden från Service Bus-ämne app och logikapp. |
| [Översikt: Azure Service Bus till Event Grid-integration](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md) | Översikt över integrering av Service Bus med Event Grid. |
| [Händelseschema](event-schema-service-bus.md) | Visar fälten i Service Bus-händelser. |

## <a name="storage"></a>Storage

Prenumerera på Blob Storage-händelser att reagera på blob skapas och borttagna händelser.

|Titel  |Beskrivning  |
|---------|---------|
| [Snabbstart: Dirigera Blob storage-händelser till en anpassad webbslutpunkt med Azure CLI](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Visar hur du använder Azure CLI för att skicka blob storage-händelser till en WebHook. |
| [Snabbstart: Dirigera Blob storage-händelser till en anpassad webbslutpunkt med PowerShell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Visar hur du använder Azure PowerShell för att skicka blob storage-händelser till en WebHook. |
| [Snabbstart: skapa och dirigera Blob storage-händelser med Azure portal](blob-event-quickstart-portal.md) | Visar hur du använder portalen för att skicka blob storage-händelser till en WebHook. |
| [Azure CLI: prenumerera på händelser för ett Blob storage-konto](./scripts/event-grid-cli-blob.md) | Exempelskriptet som prenumererar på händelser för ett Blob storage-konto. Skickas händelsen till en WebHook. |
| [PowerShell: prenumerera på händelser för ett Blob storage-konto](./scripts/event-grid-powershell-blob.md) | Exempelskriptet som prenumererar på händelser för ett Blob storage-konto. Skickas händelsen till en WebHook. |
| [Resource Manager-mall: skapa Blob-lagring och en prenumeration](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/createBlobAndSubscribe.json) | Distribuerar ett Azure Blob storage-konto och prenumererar på händelser för detta lagringskonto. Den skickar händelser till en WebHook. |
| [Översikt: reagera på Blob storage-händelser](../storage/blobs/storage-blob-event-overview.md) | Översikt över integrering av Blob-lagring med Event Grid. |
| [Händelseschema](event-schema-blob-storage.md) | Visar fälten i Blob Storage-händelser. |

## <a name="next-steps"></a>Nästa steg

* En introduktion till Event Grid finns i [Om Event Grid](overview.md).
* Kom igång snabbt med Event Grid, se [skapa och dirigera anpassade händelser med Azure Event Grid](custom-event-quickstart.md).
