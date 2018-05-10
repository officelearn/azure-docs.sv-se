---
title: Azure händelse rutnätet händelsekällor
description: Beskriver stöds händelsekällor för rutnät för Azure-händelse
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 04/25/2018
ms.author: tomfitz
ms.openlocfilehash: ce5c6ccd4c2e51375bbbb4df677ee96c839b30f6
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="event-sources-in-azure-event-grid"></a>Händelsekällan i rutnätet för Azure-händelse

En händelsekälla är där händelsen inträffar. Flera Azure-tjänster konfigureras automatiskt för att skicka händelser. Du kan också skapa anpassade program som skickar händelser. Anpassade program behöver inte finnas i Azure för att använda händelsen rutnätet för händelsedistribution.

Den här artikeln innehåller länkar till innehåll för varje händelsekälla.

## <a name="azure-subscriptions"></a>Azure-prenumerationer

Prenumerera på Azure-prenumerationer händelser att svara på förändringar i resurser i en Azure-prenumeration.

|Namn |Beskrivning  |
|---------|---------|
| [Integrera Azure Automation med händelsen rutnätet och Microsoft team](ensure-tags-exists-on-new-virtual-machines.md) |Skapa en virtuell dator som skickar en händelse. Händelsen utlöses en Automation-runbook som taggar den virtuella datorn och utlöser ett meddelande som skickas till en Microsoft-Teams-kanal. |
| [Händelseschema](event-schema-subscriptions.md) | Visar fält i Azure-prenumerationshändelser. |

## <a name="custom-topics"></a>Anpassade avsnitt

Prenumerera på anpassade avsnitt för att svara på händelser för programmet.

|Namn  |Beskrivning  |
|---------|---------|
| [Skapa och dirigera anpassade händelser med Azure CLI](custom-event-quickstart.md) | Visar hur du använder Azure CLI för att skicka anpassade händelser. |
| [Skapa och dirigera anpassade händelser med Azure PowerShell](custom-event-quickstart-powershell.md) | Visar hur du använder Azure PowerShell för att skicka anpassade händelser. |
| [Skapa och dirigera anpassade händelser med Azure-portalen](custom-event-quickstart-portal.md) | Visar hur du använder portalen för att skicka anpassade händelser. |
| [Efter att anpassade avsnittet](post-to-custom-topic.md) | Visar hur du publicerar en händelse till ett eget avsnitt. |
| [Vidarebefordra anpassade händelser till Azure Queue storage](custom-event-to-queue-storage.md) | Beskriver hur du skickar anpassade händelser till en Queue storage. |
| [Händelseschema](event-schema.md) | Visar fält i anpassade händelser. |

## <a name="event-hubs"></a>Event Hubs

Prenumerera på Händelsehubbar händelser du vill samla in händelser för filen.

|Namn  |Beskrivning  |
|---------|---------|
| [Strömma stordata till ett datalager](event-grid-event-hubs-integration.md) | När Händelsehubbar skapar en Capture-fil, skickar en händelse till en funktionsapp händelse rutnätet. Appen hämtar filen avbildning och migrerar data till data warehouse. |
| [Händelseschema](event-schema-event-hubs.md) | Visar fält i Händelsehubbar händelser. |

## <a name="iot-hub"></a>IoT Hub

Prenumerera på IoT-hubb händelser att svara på enheten skapades och togs bort händelser.

|Namn  |Beskrivning  |
|---------|---------|
| [Skicka e-postmeddelanden om Azure IoT Hub-händelser med hjälp av Logic Apps](publish-iot-hub-events-to-logic-apps.md) | En logikapp skickar ett e-postmeddelande varje gång en enhet har lagts till i din IoT-hubb. |
| [Ta hänsyn till IoT-hubb händelser med händelse rutnätet för att utlösaråtgärder](../iot-hub/iot-hub-event-grid.md) | Översikt över integrering av Iot-hubbar med händelsen rutnätet. |
| [Händelseschema](event-schema-iot-hub.md) | Innehåller fält i IoT-hubb händelser. |

## <a name="media-services"></a>Media Services

Prenumerera på Media Services händelser att svara på händelser från jobb.

|Namn  |Beskrivning  |
|---------|---------|
| [Reagera på Media Services-händelser](../media-services/latest/reacting-to-media-services-events.md) | Översikt över integrering av Media Services med händelsen rutnätet. |
| [Vidarebefordra Azure Media Services-händelser till en anpassad webbplats-slutpunkten med hjälp av CLI](../media-services/latest/job-state-events-cli-how-to.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Visar hur du skickar händelser från Media Services. |
| [Händelseschema](../media-services/latest/media-services-event-schemas.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Visar fält i Media Services-händelser. |

## <a name="resource-groups"></a>Resursgrupper

Prenumerera på resursen gruppera händelser att svara på förändringar i resurser i en resursgrupp.

|Namn  |Beskrivning  |
|---------|---------|
| [Övervaka ändringar på virtuella maskiner med Azure Event Grid och Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md) | En logikapp övervakar ändringar till en virtuell dator och skickar e-postmeddelanden om dessa ändringar. |
| [Händelseschema](event-schema-resource-groups.md) | Visar fält i resursen gruppera händelser. |

## <a name="service-bus"></a>Service Bus

Prenumerera på Service Bus händelser ska svara på meddelanden utan en aktiva lyssnare.

|Namn  |Beskrivning  |
|---------|---------|
| [Azure Service Bus Azure händelse rutnätet integration exempel](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Händelsen rutnätet skickar meddelanden från Service Bus-ämne app och logikapp. |
| [Azure Service Bus händelse rutnätet integrering, översikt](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md) | Översikt över integrera Service Bus med händelsen rutnätet. |
| [Händelseschema](event-schema-service-bus.md) | Visar fält i Service Bus-händelser. |

## <a name="storage"></a>Storage

Prenumerera på Blob Storage-händelser och svarar blob-händelser som skapats och tagits bort.

|Namn  |Beskrivning  |
|---------|---------|
| [Vidarebefordra Blob storage-händelser till en anpassad webbplats slutpunkt med Azure CLI](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Visar hur du använder Azure CLI för att skicka blob storage-händelser. |
| [Vidarebefordra Blob storage-händelser till en anpassad webbplats slutpunkt med PowerShell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Visar hur du använder Azure PowerShell för att skicka blob storage-händelser. |
| [Reagera på Blob Storage-händelser](../storage/blobs/storage-blob-event-overview.md) | Översikt över integrera Blob storage med händelsen rutnätet. |
| [Händelseschema](event-schema-blob-storage.md) | Visar fält i Blob Storage-händelser. |

## <a name="next-steps"></a>Nästa steg

* En introduktion till händelse rutnätet finns [om händelsen rutnätet](overview.md).
* Om du vill komma igång snabbt med hjälp av händelse rutnätet kan se [skapa och flöde anpassade händelser med Azure händelse rutnätet](custom-event-quickstart.md).
