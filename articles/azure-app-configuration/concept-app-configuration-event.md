---
title: Korsreagerande på Azure-Appkonfiguration nyckel / värde-händelser | Microsoft Docs
description: Använd Azure Event Grid för att prenumerera på händelser för Appkonfiguration.
services: azure-app-configuration,event-grid
author: jimmyca
ms.author: jimmyca
ms.date: 05/30/2019
ms.topic: article
ms.service: azure-app-configuration
ms.openlocfilehash: 601124aef37d2b285db71130f5c63b3620c7768f
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/06/2019
ms.locfileid: "66735653"
---
# <a name="reacting-to-azure-app-configuration-events"></a>Reagera på händelser för konfiguration av Azure

Azure App-konfigurationshändelser gör det möjligt att reagera på förändringar i nyckel-värden. Detta görs utan behov av komplicerade kod eller dyrt och ineffektiv avsökningen tjänster. I stället händelser skickas [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) till prenumeranter som [Azure Functions](https://azure.microsoft.com/services/functions/), [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/), eller till och med dina egna anpassade http-lyssnare och du bara betala för det du använder.

Azure Appkonfiguration händelser skickas till Azure Event Grid som tillhandahåller pålitlig leveranstjänster till dina program via omfattande försök principer och förlorade leverans. Mer information finns i [Event Grid meddelandeleverans och försök igen](https://docs.microsoft.com/azure/event-grid/delivery-and-retry).

Vanliga app configuration händelse scenarier är uppdatera programkonfiguration, utlösa distributioner eller något configuration inriktad arbetsflöde. När ändringarna är ovanliga, men din situation kräver omedelbar svarstider, kan händelsebaserad arkitektur vara särskilt effektivt.

Ta en titt på [väg konfiguration av Azure-händelser till en anpassad webb-endpoint - CLI](./howto-app-configuration-event.md) för ett enkelt exempel. 

![Event Grid-modell](./media/event-grid-functional-model.png)

## <a name="available-azure-app-configuration-events"></a>Tillgängliga konfiguration av Azure-händelser
Händelserutnät använder [händelseprenumerationer](../event-grid/concepts.md#event-subscriptions) händelsen skicka meddelanden till prenumeranter. Azure Appkonfiguration händelseprenumerationer kan innehålla två typer av händelser:  

> |Händelsenamn|Beskrivning|
> |----------|-----------|
> |`Microsoft.AppConfiguration.KeyValueModified`|När en nyckel / värde-skapas eller ersatts|
> |`Microsoft.AppConfiguration.KeyValueDeleted`|När en nyckel / värde-har tagits bort|

## <a name="event-schema"></a>Händelseschema
Azure App-konfigurationshändelser innehåller all information du behöver för att svara på ändringar i dina data. Du kan identifiera en händelse för konfiguration av appen eftersom egenskapen händelsetyp börjar med ”Microsoft.AppConfiguration”. Mer information om användningen av egenskaper för Event Grid-händelse dokumenteras i [Event Grid Händelseschema](../event-grid/event-schema.md).  

> |Egenskap|Typ|Beskrivning|
> |-------------------|------------------------|-----------------------------------------------------------------------|
> |topic|string|Fullständig Azure Resource Manager-id för den konfiguration som genererar händelsen.|
> |topic|string|URI för nyckel / värde som omfattas av händelsen.|
> |eventTime|string|Datum och tid som händelsen har genererats i ISO 8601-format.|
> |eventType|string|”Microsoft.AppConfiguration.KeyValueModified” eller ”Microsoft.AppConfiguration.KeyValueDeleted”.|
> |Id|string|En unik identifierare för den här händelsen.|
> |dataVersion|string|Dataobjektets schemaversion.|
> |metadataVersion|string|Schemaversion för översta egenskaper.|
> |data|objekt|Insamling av data för konfiguration av Azure-specifik händelse|
> |data.key|string|Nyckeln för nyckel / värde som har ändrats eller tagits bort.|
> |data.label|string|Etiketten, om något av nyckel / värde som har ändrats eller tagits bort.|
> |data.etag|string|För `KeyValueModified` etag för det nya nyckel-värdet. För `KeyValueDeleted` etag nyckel / värde som har tagits bort.|

Här är ett exempel på en händelse som KeyValueModified:
```json
[{
  "id": "84e17ea4-66db-4b54-8050-df8f7763f87b",
  "topic": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/microsoft.appconfiguration/configurationstores/contoso",
  "subject": "https://contoso.azconfig.io/kv/Foo?label=FizzBuzz",
  "data": {
    "key": "Foo",
    "label": "FizzBuzz",
    "etag": "FnUExLaj2moIi4tJX9AXn9sakm0"
  },
  "eventType": "Microsoft.AppConfiguration.KeyValueModified",
  "eventTime": "2019-05-31T20:05:03Z",
  "dataVersion": "1",
  "metadataVersion": "1"
}]

```

Mer information finns i [händelseschemat för konfiguration av Azure](../event-grid/event-schema-app-configuration.md).

## <a name="practices-for-consuming-events"></a>Metoder för att använda händelser
Program som hanterar app-konfigurationshändelser bör följa några rekommenderade metoder:
> [!div class="checklist"]
> * När flera prenumerationer kan konfigureras för att dirigera händelser till samma händelsehanterare, är det viktigt inte att anta händelser är från en viss källa, utan att kontrollera ämne för meddelandet att se till att det kommer från den konfiguration du förväntade dig.
> * Kontrollera dessutom att händelsetyp är något du är beredd på att processen och förutsätter inte att alla händelser som visas är de typer som du förväntar dig.
> * När meddelanden kan tas emot i fel ordning och efter ett tag, använder du fälten etag för att förstå om din information om objekt är fortfarande aktuell.  Använd dessutom fälten sequencer för att förstå händelseordningen till ett visst objekt.
> * Använda ämnesfältet för att komma åt nyckel / värde som har ändrats.


## <a name="next-steps"></a>Nästa steg

Läs mer om Event Grid och testa konfiguration av Azure-händelser:

- [Om Event Grid](../event-grid/overview.md)
- [Dirigera konfiguration av Azure-händelser till en anpassad webbslutpunkt](./howto-app-configuration-event.md)