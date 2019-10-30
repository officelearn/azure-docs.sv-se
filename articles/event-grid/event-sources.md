---
title: Azure Event Grid händelse källor
description: Beskriver de händelse källor som stöds för Azure Event Grid
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: spelluru
ms.openlocfilehash: 5e8dc6629cfbbc3e42aeeb4dc94f33d13867ca9f
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2019
ms.locfileid: "73024800"
---
# <a name="event-sources-in-azure-event-grid"></a>Händelse källor i Azure Event Grid

En händelse källa är den plats där händelsen inträffar. Flera Azure-tjänster konfigureras automatiskt för att skicka händelser. Du kan också skapa anpassade program som skickar händelser. Anpassade program behöver inte vara värdar i Azure för att använda Event Grid för händelse distribution.

Den här artikeln innehåller länkar till innehåll för varje händelse källa.

## <a name="azure-subscriptions"></a>Azure-prenumerationer

Prenumerera på Azure Subscriptions-händelser för att svara på ändringar i resurser i en Azure-prenumeration.

|Rubrik |Beskrivning  |
|---------|---------|
| [Självstudie: Azure Automation med Event Grid och Microsoft Teams](ensure-tags-exists-on-new-virtual-machines.md) |Skapa en virtuell dator, som skickar en händelse. Händelsen utlöser en Automation-Runbook som Taggar den virtuella datorn och utlöser ett meddelande som skickas till en Microsoft Teams-kanal. |
| [Gör så här: prenumerera på händelser via portalen](subscribe-through-portal.md) | Använd portalen för att prenumerera på händelser för en Azure-prenumeration. |
| [Azure CLI: prenumerera på händelser för en Azure-prenumeration](./scripts/event-grid-cli-azure-subscription.md) |Exempel skript som skapar en Event Grid-prenumeration på en Azure-prenumeration och skickar händelser till en webhook. |
| [PowerShell: prenumerera på händelser för en Azure-prenumeration](./scripts/event-grid-powershell-azure-subscription.md)| Exempel skript som skapar en Event Grid-prenumeration på en Azure-prenumeration och skickar händelser till en webhook. |
| [Händelse schema](event-schema-subscriptions.md) | Visar fält i Azure Subscription-händelser. |

## <a name="container-registry"></a>Container Registry

Prenumerera på Container Registry händelser för att svara på ändringar i avbildningar.

|Rubrik |Beskrivning  |
|---------|---------|
| [Snabb start: skicka behållar register händelser](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Visar hur du använder Azure CLI för att skicka Container Registry händelser. |
| [Händelse schema](event-schema-container-registry.md) | Visar fält i Container Registry händelser. |

## <a name="custom-topics"></a>Anpassade ämnen

Prenumerera på anpassade ämnen för att svara på program händelser.

|Rubrik  |Beskrivning  |
|---------|---------|
| [Snabb start: skapa och dirigera anpassade händelser med Azure CLI](custom-event-quickstart.md) | Visar hur du använder Azure CLI för att skicka anpassade händelser. |
| [Snabb start: skapa och dirigera anpassade händelser med Azure PowerShell](custom-event-quickstart-powershell.md) | Visar hur du använder Azure PowerShell för att skicka anpassade händelser. |
| [Snabb start: skapa och dirigera anpassade händelser med Azure Portal](custom-event-quickstart-portal.md) | Visar hur du använder portalen för att skicka anpassade händelser. |
| [Snabb start: dirigera anpassade händelser till Azure Queue Storage](custom-event-to-queue-storage.md) | Beskriver hur du skickar anpassade händelser till en Queue Storage. |
| [Gör så här: skicka till anpassat ämne](post-to-custom-topic.md) | Visar hur du publicerar en händelse i ett anpassat ämne. |
| [Azure CLI: skapa Event Grid anpassat ämne](./scripts/event-grid-cli-create-custom-topic.md)|Exempel skript som skapar ett anpassat ämne. Skriptet hämtar slut punkten och en nyckel.|
| [Azure CLI: prenumerera på händelser för ett anpassat ämne](./scripts/event-grid-cli-subscribe-custom-topic.md)|Exempel skript som skapar en prenumeration för ett anpassat ämne. Den skickar händelser till en webhook.|
| [PowerShell: skapa Event Grid anpassat ämne](./scripts/event-grid-powershell-create-custom-topic.md)|Exempel skript som skapar ett anpassat ämne. Skriptet hämtar slut punkten och en nyckel.|
| [PowerShell: prenumerera på händelser för ett anpassat ämne](./scripts/event-grid-powershell-subscribe-custom-topic.md)|Exempel skript som skapar en prenumeration för ett anpassat ämne. Den skickar händelser till en webhook.|
| [Resource Manager-mall: anpassad ämne och webhook-slutpunkt](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid) | En Resource Manager-mall som skapar ett anpassat ämne och en prenumeration för det anpassade ämnet. Den skickar händelser till en webhook. |
|
| [Resource Manager-mall: anpassat ämne och Event Hubs slut punkt](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| En Resource Manager-mall som skapar en prenumeration för ett anpassat ämne. Den skickar händelser till en Azure-Event Hubs. |
| [Händelse schema](event-schema.md) | Visar fält i anpassade händelser. |

## <a name="event-hubs"></a>Händelsehubbar

Prenumerera på Event Hubs händelser för att svara på avbilda fil händelser. Event Hubs kan fungera som en händelse källa eller händelse hanterare. I följande artiklar visas hur du använder Event Hubs som källa.

|Rubrik  |Beskrivning  |
|---------|---------|
| [Självstudie: strömma Big data till ett informations lager](event-grid-event-hubs-integration.md) | När Event Hubs skapar en Infångnings fil, Event Grid skickar en händelse till en Function-app. Appen hämtar insamlings filen och migrerar data till ett data lager. |
| [Händelse schema](event-schema-event-hubs.md) | Visar fält i Event Hubs händelser. |

Exempel på Event Hubs som hanterare finns i [Event Hubs hanterare](event-handlers.md#event-hubs).

## <a name="iot-hub"></a>IoT Hub

Prenumerera på IoT Hub händelser för att svara på enhet som skapats, tagits bort, anslutna, frånkopplade och telemetri-händelser.

|Rubrik  |Beskrivning  |
|---------|---------|
| [Skicka e-postaviseringar om Azure IoT Hub händelser med Logic Apps](publish-iot-hub-events-to-logic-apps.md) | En Logic App skickar ett e-postmeddelande varje gång en enhet läggs till i din IoT Hub. |
| [Reagera på IoT Hub händelser genom att använda Event Grid för att utlösa åtgärder](../iot-hub/iot-hub-event-grid.md) | Översikt över att integrera IoT Hub med Event Grid. |
| [Händelse schema](event-schema-iot-hub.md) | Visar fält i IoT Hub händelser. |
| [Beställ enhet ansluten och avkopplade enhets händelser](../iot-hub/iot-hub-how-to-order-connection-state-events.md) | Visar hur du beställer händelser i enhetens anslutnings tillstånd. |

## <a name="key-vault-preview"></a>Key Vault (för hands version)

Key Vault integration med Event Grid är för närvarande en för hands version. 

Prenumerera på Key Vault händelser för att bli meddelad när en hemlighet håller på att gå ut, en hemlig förfaller eller en hemlighet har en ny version som är tillgänglig. 

|Rubrik  |Beskrivning  |
|---------|---------|
| [Övervaka Key Vault händelser med Azure Event Grid](../key-vault/event-grid-overview.md) | Översikt över att integrera Key Vault med Event Grid. |
| [Självstudie: skapa och övervaka Key Vault händelser med Event Grid](../key-vault/event-grid-tutorial.md) | Lär dig hur du konfigurerar Event Grid-meddelanden för Key Vault. |
| [Händelse schema](event-schema-key-vault.md) | Visar fält i Key Vault händelser. |

## <a name="media-services"></a>Media Services

Prenumerera på Media Services händelser för att svara på händelser i jobb tillstånd.

|Rubrik  |Beskrivning  |
|---------|---------|
| [Översikt: reagerar på Media Services händelser](../media-services/latest/reacting-to-media-services-events.md) | Översikt över att integrera Media Services med Event Grid. |
| [Självstudie: dirigera Azure Media Services händelser till en anpassad webb slut punkt med hjälp av CLI](../media-services/latest/job-state-events-cli-how-to.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Visar hur du skickar händelser från Media Services. |
| [Händelse schema](../media-services/latest/media-services-event-schemas.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Visar fält i Media Services händelser. |

## <a name="resource-groups"></a>Resursgrupper

Prenumerera på resurs grupp händelser för att svara på ändringar i resurser i en resurs grupp.

|Rubrik  |Beskrivning  |
|---------|---------|
| [Självstudie: övervaka ändringar av virtuella datorer med Azure Event Grid och Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md) | En Logic app övervakar ändringar i en virtuell dator och skickar e-postmeddelanden om ändringarna. |
| [Azure CLI: prenumerera på händelser för en resurs grupp](./scripts/event-grid-cli-resource-group.md)| Exempel skript som prenumererar på händelser för en resurs grupp. Den skickar händelser till en webhook. |
| [Azure CLI: prenumerera på händelser för en resurs grupp och filtrera efter en resurs](./scripts/event-grid-cli-resource-group-filter.md) | Exempel skript som prenumererar på händelser för en resurs grupp och filtrerar händelser för en resurs. |
| [PowerShell: prenumerera på händelser för en resurs grupp](./scripts/event-grid-powershell-resource-group.md) | Exempel skript som prenumererar på händelser för en resurs grupp. Den skickar händelser till en webhook. |
| [PowerShell: prenumerera på händelser för en resurs grupp och filtrera efter en resurs](./scripts/event-grid-powershell-resource-group-filter.md) | Exempel skript som prenumererar på händelser för en resurs grupp och filtrerar händelser för en resurs. |
| [Resource Manager-mall: resurs prenumeration](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-resource-events-to-webhook) | Prenumererar på händelser för en Azure-prenumeration eller resurs grupp. Den skickar händelser till en webhook. |
| [Händelseschema](event-schema-resource-groups.md) | Visar fält i resurs grupps händelser. |

## <a name="service-bus"></a>Service Bus

Prenumerera på Service Bus händelser för att svara på meddelanden utan en aktiv lyssnare.

|Rubrik  |Beskrivning  |
|---------|---------|
| [Självstudie: Azure Service Bus till Azure Event Grid integrations exempel](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid skickar meddelanden från Service Bus ämne till att fungera som app-och Logic-appen. |
| [Översikt: Azure Service Bus till Event Grid-integrering](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md) | Översikt över att integrera Service Bus med Event Grid. |
| [Händelse schema](event-schema-service-bus.md) | Visar fält i Service Bus händelser. |

## <a name="storage"></a>Lagring

Prenumerera på Blob Storage händelser för att svara på BLOB-skapade och borttagna händelser.

|Rubrik  |Beskrivning  |
|---------|---------|
| [Snabb start: dirigera Blob Storage-händelser till en anpassad webb slut punkt med Azure CLI](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Visar hur du använder Azure CLI för att skicka Blob Storage-händelser till en webhook. |
| [Snabb start: dirigera Blob Storage-händelser till en anpassad webb slut punkt med PowerShell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Visar hur du använder Azure PowerShell för att skicka Blob Storage-händelser till en webhook. |
| [Snabb start: skapa och dirigera Blob Storage-händelser med Azure Portal](blob-event-quickstart-portal.md) | Visar hur du använder portalen för att skicka Blob Storage-händelser till en webhook. |
| [Azure CLI: prenumerera på händelser för ett Blob Storage-konto](./scripts/event-grid-cli-blob.md) | Exempel skript som prenumererar på en händelse för ett Blob Storage-konto. Den skickar händelsen till en webhook. |
| [PowerShell: prenumerera på händelser för ett Blob Storage-konto](./scripts/event-grid-powershell-blob.md) | Exempel skript som prenumererar på en händelse för ett Blob Storage-konto. Den skickar händelsen till en webhook. |
| [Resource Manager-mall: skapa blob-lagring och prenumeration](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage) | Distribuerar ett Azure Blob storage-konto och prenumererar på händelser för detta lagringskonto. Den skickar händelser till en webhook. |
| [Översikt: reagerar på Blob Storage-händelser](../storage/blobs/storage-blob-event-overview.md) | Översikt över integrering av Blob Storage med Event Grid. |
| [Händelse schema](event-schema-blob-storage.md) | Visar fält i Blob Storage händelser. |

## <a name="maps"></a>Maps
Prenumerera på Azure Maps händelser för att svara på händelser på en inhägnad. Ett program kan till exempel leverera ett e-postmeddelande varje gång en enhet går in eller avslutar ett avgränsnings tecken.

|Rubrik  |Beskrivning  |
|---------|---------|
| [Reagera på Azure Maps händelser med Event Grid](../azure-maps/azure-maps-event-grid-integration.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Översikt över att integrera Azure Maps med Event Grid. |
| [Självstudie: Konfigurera en gräns](../azure-maps/tutorial-geofence.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Den här självstudien vägleder dig igenom de grundläggande stegen för att konfigurera geofence med hjälp av Azure Maps. Du använder Azure Event Grid för att strömma de gränsade resultaten och ställa in ett meddelande baserat på de gränser som anges i det här resultatet. |
| [Händelse schema](event-schema-azure-maps.md) | Visar fält i Azure Maps händelser. |

## <a name="app-configuration"></a>App Configuration
Prenumerera på Azure App konfigurations händelser för att svara på nyckel värdes ändrings händelser.

|Rubrik | Beskrivning |
|---------|---------|
| [Reagera på Azure App konfigurations händelser med Event Grid](../azure-app-configuration/concept-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Översikt över att integrera Azure App-konfiguration med Event Grid. |
| [Snabb start: dirigera Azure App konfigurations händelser till en anpassad webb slut punkt med Azure CLI](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Visar hur du använder Azure CLI för att skicka Azure App konfigurations händelser till en webhook. |
| [Händelse schema](event-schema-app-configuration.md) | Visar fält i Azure App konfigurations händelser. |

## <a name="azure-signalr"></a>Azure SignalR
Prenumerera på händelser i Azure SignalR service och svara på klient anslutnings händelser.

|Rubrik | Beskrivning |
|---------|---------|
| [Reagera på händelser i Azure SignalR service genom att använda Event Grid](../azure-signalr/signalr-concept-event-grid-integration.md) | Översikt över integrering av Azure SignalR service med Event Grid. |
| [Så här skickar du Azure SignalR service-händelser till Event Grid](../azure-signalr/signalr-howto-event-grid-integration.md) | Visar hur du skickar Azure SignalR service-händelser till ett program via Event Grid. |
| [Händelse schema](event-schema-azure-signalr.md) | Visar fält i Azure SignalR service-händelser. |



## <a name="next-steps"></a>Nästa steg

* En introduktion till Event Grid finns i [Om Event Grid](overview.md).
* Information om hur du snabbt kommer igång med Event Grid finns i [skapa och dirigera anpassade händelser med Azure Event Grid](custom-event-quickstart.md).
