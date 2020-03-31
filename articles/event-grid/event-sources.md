---
title: Händelsekällor för Azure Event Grid
description: Azure-prenumerationer, behållarregister, anpassade ämnen, eventhubbar, IoT-hubb, nyckelvalv, medietjänster, resursgrupper, servicebuss, lagring, kartor, appkonfiguration, signal R, maskininlärning.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: spelluru
ms.openlocfilehash: d4a426ea1432d0266b7ae9344afefe8ddac1d030
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265004"
---
# <a name="event-sources-in-azure-event-grid"></a>Händelsekällor i Azure Event Grid

En händelsekälla är där händelsen inträffar. Flera Azure-tjänster konfigureras automatiskt för att skicka händelser. Du kan också skapa anpassade program som skickar händelser. Anpassade program behöver inte finnas i Azure för att använda Event Grid för händelsedistribution.

Den här artikeln innehåller länkar till innehåll för varje händelsekälla.

## <a name="azure-subscriptions"></a>Azure-prenumerationer

Prenumerera på Azure-prenumerationer händelser för att svara på ändringar i resurser i en Azure-prenumeration.

|Titel |Beskrivning  |
|---------|---------|
| [Självstudiekurs: Azure Automation med Event Grid och Microsoft Teams](ensure-tags-exists-on-new-virtual-machines.md) |Skapa en virtuell dator som skickar en händelse. Händelsen utlöser en Automation-runbook som taggar den virtuella datorn och utlöser ett meddelande som skickas till en Microsoft Teams-kanal. |
| [Så här prenumererar du på evenemang via portal](subscribe-through-portal.md) | Använd portalen för att prenumerera på händelser för en Azure-prenumeration. |
| [Azure CLI: prenumerera på händelser för en Azure-prenumeration](./scripts/event-grid-cli-azure-subscription.md) |Exempelskript som skapar en Event Grid-prenumeration på en Azure-prenumeration och skickar händelser till en WebHook. |
| [PowerShell: prenumerera på händelser för en Azure-prenumeration](./scripts/event-grid-powershell-azure-subscription.md)| Exempelskript som skapar en Event Grid-prenumeration på en Azure-prenumeration och skickar händelser till en WebHook. |
| [Händelseschema](event-schema-subscriptions.md) | Visar fält i Azure-prenumerationshändelser. |

## <a name="container-registry"></a>Container Registry

Prenumerera på containerregisterhändelser för att svara på ändringar i avbildningar.

|Titel |Beskrivning  |
|---------|---------|
| [Snabbstart: skicka händelser för behållarregister](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Visar hur du använder Azure CLI för att skicka containerregisterhändelser. |
| [Händelseschema](event-schema-container-registry.md) | Visar fält i containerregisterhändelser. |

## <a name="custom-topics"></a>Anpassade ämnen

Prenumerera på anpassade ämnen för att svara på programhändelser.

|Titel  |Beskrivning  |
|---------|---------|
| [Snabbstart: skapa och dirigera anpassade händelser med Azure CLI](custom-event-quickstart.md) | Visar hur du använder Azure CLI för att skicka anpassade händelser. |
| [Snabbstart: skapa och dirigera anpassade händelser med Azure PowerShell](custom-event-quickstart-powershell.md) | Visar hur du använder Azure PowerShell för att skicka anpassade händelser. |
| [Snabbstart: skapa och dirigera anpassade händelser med Azure-portalen](custom-event-quickstart-portal.md) | Visar hur du använder portalen för att skicka anpassade händelser. |
| [Snabbstart: dirigera anpassade händelser till Azure-kölagring](custom-event-to-queue-storage.md) | Beskriver hur du skickar anpassade händelser till ett kölagringsutrymme. |
| [Så här lägger du upp i anpassat ämne](post-to-custom-topic.md) | Visar hur du publicerar en händelse i ett anpassat ämne. |
| [Anpassat ämne för Azure CLI: skapa det anpassade avsnittet Händelserutnät](./scripts/event-grid-cli-create-custom-topic.md)|Exempelskript som skapar ett anpassat ämne. Skriptet hämtar slutpunkten och en nyckel.|
| [Azure CLI: prenumerera på händelser för ett anpassat ämne](./scripts/event-grid-cli-subscribe-custom-topic.md)|Exempelskript som skapar en prenumeration för ett anpassat ämne. Den skickar händelser till en WebHook.|
| [Anpassat ämne för PowerShell: skapa anpassat ämne för händelserutnät](./scripts/event-grid-powershell-create-custom-topic.md)|Exempelskript som skapar ett anpassat ämne. Skriptet hämtar slutpunkten och en nyckel.|
| [PowerShell: prenumerera på händelser för ett anpassat ämne](./scripts/event-grid-powershell-subscribe-custom-topic.md)|Exempelskript som skapar en prenumeration för ett anpassat ämne. Den skickar händelser till en WebHook.|
| [Resource Manager-mall: anpassat ämne och WebHook-slutpunkt](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid) | En Resource Manager-mall som skapar ett anpassat ämne och en prenumeration för det anpassade avsnittet. Den skickar händelser till en WebHook. |
|
| [Resurshanterarens mall: anpassade avsnitt och slutpunkt för händelsehubbar](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| En Resource Manager-mall som skapar en prenumeration för ett anpassat ämne. Den skickar händelser till en Azure Event Hubs. |
| [Händelseschema](event-schema.md) | Visar fält i anpassade händelser. |

## <a name="event-hubs"></a>Händelsehubbar

Prenumerera på eventhubbar händelser för att svara på Capture-filhändelser. Event Hubs kan fungera som antingen en händelsekälla eller händelsehanterare. Följande artiklar visar hur du använder Event Hubs som källa.

|Titel  |Beskrivning  |
|---------|---------|
| [Självstudiekurs: strömma stordata till ett informationslager](event-grid-event-hubs-integration.md) | När Event Hubs skapar en capture-fil skickar Event Grid en händelse till en funktionsapp. Appen hämtar capture-filen och migrerar data till ett informationslager. |
| [Händelseschema](event-schema-event-hubs.md) | Visar fält i händelser i händelsehubbar. |

Exempel på eventhubbar som hanterare finns i [hanteraren för händelsehubbar](event-handlers.md#event-hubs).

## <a name="iot-hub"></a>IoT Hub

Prenumerera på IoT Hub-händelser för att svara på enhetsskapade, borttagna, anslutna, frånkopplade och telemetrihändelser.

|Titel  |Beskrivning  |
|---------|---------|
| [Skicka e-postmeddelanden om Azure IoT Hub-händelser med hjälp av Logic Apps](publish-iot-hub-events-to-logic-apps.md) | En logikapp skickar ett e-postmeddelande varje gång en enhet läggs till i din IoT Hub. |
| [Reagera på IoT Hub-händelser med hjälp av Händelserutnät för att utlösa åtgärder](../iot-hub/iot-hub-event-grid.md) | Översikt över integrering av IoT Hub med Event Grid. |
| [Händelseschema](event-schema-iot-hub.md) | Visar fält i IoT Hub-händelser. |
| [Beställa enhetsansluten och frånkopplad enhet](../iot-hub/iot-hub-how-to-order-connection-state-events.md) | Visar hur du beställer enhetsanslutningstillståndshändelser. |

## <a name="key-vault-preview"></a>Key Vault (förhandsgranskning)

Key Vault-integrering med Event Grid förhandsgranskas för närvarande. 

Prenumerera på Key Vault-händelser som ska meddelas när en hemlighet håller på att upphöra att gälla, en hemlighet upphör att gälla eller en hemlighet har en ny version tillgänglig. 

|Titel  |Beskrivning  |
|---------|---------|
| [Övervaka nyckelvalvshändelser med Azure Event Grid](../key-vault/event-grid-overview.md) | Översikt över att integrera Key Vault med Event Grid. |
| [Självstudiekurs: Skapa och övervaka key vault-händelser med händelserutnät](../key-vault/event-grid-tutorial.md) | Läs om hur du konfigurerar aviseringar om händelserutnät för Key Vault. |
| [Händelseschema](event-schema-key-vault.md) | Visar fält i Key Vault-händelser. |

## <a name="media-services"></a>Media Services

Prenumerera på Media Services-händelser för att svara på jobbtillståndshändelser.

|Titel  |Beskrivning  |
|---------|---------|
| [Översikt: reagera på Media Services-händelser](../media-services/latest/reacting-to-media-services-events.md) | Översikt över integrering av medietjänster med Event Grid. |
| [Självstudiekurs: dirigera Azure Media Services-händelser till en anpassad webbslutpunkt med CLI](../media-services/latest/job-state-events-cli-how-to.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Visar hur du skickar händelser från Media Services. |
| [Händelseschema](../media-services/latest/media-services-event-schemas.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Visar fält i Media Services-händelser. |

## <a name="resource-groups"></a>Resursgrupper

Prenumerera på resursgruppshändelser för att svara på ändringar i resurser i en resursgrupp.

|Titel  |Beskrivning  |
|---------|---------|
| [Självstudiekurs: övervaka ändringar av virtuella datorer med Azure Event Grid och Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md) | En logikapp övervakar ändringar på en virtuell dator och skickar e-postmeddelanden om dessa ändringar. |
| [Azure CLI: prenumerera på händelser för en resursgrupp](./scripts/event-grid-cli-resource-group.md)| Exempel på skript som prenumererar på händelser för en resursgrupp. Den skickar händelser till en WebHook. |
| [Azure CLI: prenumerera på händelser för en resursgrupp och filtrera efter en resurs](./scripts/event-grid-cli-resource-group-filter.md) | Exempelskript som prenumererar på händelser för en resursgrupp och filtrerar händelser för en resurs. |
| [PowerShell: prenumerera på händelser för en resursgrupp](./scripts/event-grid-powershell-resource-group.md) | Exempel på skript som prenumererar på händelser för en resursgrupp. Den skickar händelser till en WebHook. |
| [PowerShell: prenumerera på händelser för en resursgrupp och filtrera efter en resurs](./scripts/event-grid-powershell-resource-group-filter.md) | Exempelskript som prenumererar på händelser för en resursgrupp och filtrerar händelser för en resurs. |
| [Resource Manager-mall: resursprenumeration](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-resource-events-to-webhook) | Prenumererar på händelser för en Azure-prenumeration eller resursgrupp. Den skickar händelser till en WebHook. |
| [Händelseschema](event-schema-resource-groups.md) | Visar fält i resursgrupphändelser. |

## <a name="service-bus"></a>Service Bus

Prenumerera på Service Bus-händelser för att svara på meddelanden utan aktiv lyssnare.

|Titel  |Beskrivning  |
|---------|---------|
| [Självstudiekurs: Exempel på integrering av Azure Service Bus till Azure Event Grid](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid skickar meddelanden från Service Bus-avsnittet för att fungera app och logikapp. |
| [Översikt: Integrering av Azure Service Bus to Event Grid](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md) | Översikt över att integrera Service Bus med Event Grid. |
| [Händelseschema](event-schema-service-bus.md) | Visar fält i Service Bus-händelser. |

## <a name="storage"></a>Lagring

Prenumerera på Blob Storage-händelser för att svara på blob skapade och borttagna händelser.

>[!NOTE]
> Endast lagringskonton av typen **StorageV2 (general purpose v2)** och **BlobStorage** stöder händelseintegrering. **Lagring (allmänt v1)** stöder *inte* integrering med Event Grid.

|Titel  |Beskrivning  |
|---------|---------|
| [Snabbstart: dirigera Blob-lagringshändelser till en anpassad webbslutpunkt med Azure CLI](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Visar hur du använder Azure CLI för att skicka blob-lagringshändelser till en WebHook. |
| [Snabbstart: dirigera Blob-lagringshändelser till en anpassad webbslutpunkt med PowerShell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Visar hur du använder Azure PowerShell för att skicka blob-lagringshändelser till en WebHook. |
| [Snabbstart: skapa och dirigera Blob-lagringshändelser med Azure-portalen](blob-event-quickstart-portal.md) | Visar hur du använder portalen för att skicka blob-lagringshändelser till en WebHook. |
| [Azure CLI: prenumerera på händelser för ett Blob-lagringskonto](./scripts/event-grid-cli-blob.md) | Exempelskript som prenumererar på händelsen för ett Blob-lagringskonto. Händelsen skickas till en WebHook. |
| [PowerShell: prenumerera på händelser för ett Blob-lagringskonto](./scripts/event-grid-powershell-blob.md) | Exempelskript som prenumererar på händelsen för ett Blob-lagringskonto. Händelsen skickas till en WebHook. |
| [Resource Manager-mall: Skapa Blob-lagring och prenumeration](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage) | Distribuerar ett Azure Blob storage-konto och prenumererar på händelser för detta lagringskonto. Den skickar händelser till en WebHook. |
| [Översikt: reagera på Blob-lagringshändelser](../storage/blobs/storage-blob-event-overview.md) | Översikt över integrering av Blob-lagring med Event Grid. |
| [Händelseschema](event-schema-blob-storage.md) | Visar fält i Blob Storage-händelser. |

## <a name="maps"></a>Kartor
Prenumerera på Azure Maps-händelser för att svara på geofence-händelser. Ett program kan till exempel leverera ett e-postmeddelande varje gång en enhet går in i eller avslutar en geofence.

|Titel  |Beskrivning  |
|---------|---------|
| [Reagera på Azure Maps-händelser med hjälp av Event Grid](../azure-maps/azure-maps-event-grid-integration.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Översikt över hur du integrerar Azure Maps med Event Grid. |
| [Självstudiekurs: Skapa en geofence](../azure-maps/tutorial-geofence.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Den här självstudien vägleder dig igenom de grundläggande stegen för att konfigurera geofence med hjälp av Azure Maps. Du använder Azure Event Grid för att strömma geofence-resultaten och ställa in ett meddelande baserat på geofence-resultaten. |
| [Händelseschema](event-schema-azure-maps.md) | Visar fält i Azure Maps-händelser. |

## <a name="app-configuration"></a>App Configuration
Prenumerera på Azure App-konfigurationshändelser för att svara på nyckelvärdesändringar.

|Titel | Beskrivning |
|---------|---------|
| [Reagera på Azure App-konfigurationshändelser med hjälp av Event Grid](../azure-app-configuration/concept-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Översikt över integreringen av Azure App Configuration med Event Grid. |
| [Snabbstart: dirigera Azure App-konfigurationshändelser till en anpassad webbslutpunkt med Azure CLI](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Visar hur du använder Azure CLI för att skicka Azure App Configuration-händelser till en WebHook. |
| [Händelseschema](event-schema-app-configuration.md) | Visar fält i Azure App Configuration-händelser. |

## <a name="azure-signalr"></a>Azure SignalR
Prenumerera på Azure SignalR-tjänsthändelser för att svara på klientanslutningshändelser.

|Titel | Beskrivning |
|---------|---------|
| [Reagera på Azure SignalR-tjänsthändelser med hjälp av Event Grid](../azure-signalr/signalr-concept-event-grid-integration.md) | Översikt över integrering av Azure SignalR-tjänsten med Event Grid. |
| [Så här skickar du Azure SignalR-tjänsthändelser till Event Grid](../azure-signalr/signalr-howto-event-grid-integration.md) | Visar hur du skickar Azure SignalR-tjänsthändelser till ett program via Event Grid. |
| [Händelseschema](event-schema-azure-signalr.md) | Visar fält i Azure SignalR-tjänsthändelser. |

## <a name="azure-machine-learning"></a>Azure Machine Learning

Prenumerera på Azure Machine Learning-arbetsytehändelser för att svara på modellregistrering.

| Titel | Beskrivning |
| ----- | ----- |
| [Använda Azure Machine Learning-händelser](../machine-learning/concept-event-grid-integration.md) | Översikt över hur du integrerar Azure Machine Learning med Event Grid. |
| [Azure Event Grid-händelseschema för Azure Machine Learning](event-schema-machine-learning.md) | Visar fält i Azure Machine Learning-händelserna. |

## <a name="next-steps"></a>Nästa steg

* En introduktion till Event Grid finns i [Om Event Grid](overview.md).
* Information om hur du snabbt kommer igång med Event Grid finns i [Skapa och dirigera anpassade händelser med Azure Event Grid](custom-event-quickstart.md).
