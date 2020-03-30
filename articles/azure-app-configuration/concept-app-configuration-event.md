---
title: Reagera på nyckelvärdehändelser för Azure App-konfiguration
description: Använd Azure Event Grid för att prenumerera på appkonfigurationshändelser.
services: azure-app-configuration,event-grid
author: jimmyca
ms.author: jimmyca
ms.date: 02/20/2020
ms.topic: article
ms.service: azure-app-configuration
ms.openlocfilehash: a4f61d147ba1abf73ada6360b8d0d965d8e063a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77523806"
---
# <a name="reacting-to-azure-app-configuration-events"></a>Reagera på Azure App-konfigurationshändelser

Azure App-konfigurationshändelser gör det möjligt för program att reagera på ändringar i nyckelvärden. Detta görs utan behov av komplicerad kod eller dyra och ineffektiva opinionsundersökningar. I stället överförs händelser via [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) till prenumeranter som Azure [Functions,](https://azure.microsoft.com/services/functions/) [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)eller till och med till din egen anpassade http-lyssnare. Kritiskt, betalar du bara för vad du använder.

Azure App-konfigurationshändelser skickas till Azure Event Grid som tillhandahåller tillförlitliga leveranstjänster till dina program via avancerade principer för återförsök och leverans av obeställbara brev. Mer information finns i [Leverans av meddelande från Event Grid och försök igen](https://docs.microsoft.com/azure/event-grid/delivery-and-retry).

Vanliga appkonfigurationshändelsescenarier är uppdatering av programkonfiguration, utlösande distributioner eller konfigurationsorienterade arbetsflöden. När ändringar är ovanliga, men ditt scenario kräver omedelbar svarstid, kan händelsebaserad arkitektur vara särskilt effektiv.

Ta en titt på [Route Azure App Configuration händelser till en anpassad webbslutpunkt - CLI](./howto-app-configuration-event.md) för ett snabbt exempel. 

![Modell för händelserutnät](./media/event-grid-functional-model.png)

## <a name="available-azure-app-configuration-events"></a>Tillgängliga konfigurationshändelser för Azure-appar
Event grid använder [händelseprenumerationer](../event-grid/concepts.md#event-subscriptions) för att dirigera händelsemeddelanden till prenumeranter. Azure App Configuration-händelseprenumerationer kan innehålla två typer av händelser:  

> |Händelsenamn|Beskrivning|
> |----------|-----------|
> |`Microsoft.AppConfiguration.KeyValueModified`|Utlöses när ett nyckelvärde skapas eller ersätts|
> |`Microsoft.AppConfiguration.KeyValueDeleted`|Utlöses när ett nyckelvärde tas bort|

## <a name="event-schema"></a>Händelseschema
Azure App-konfigurationshändelser innehåller all information du behöver för att svara på ändringar i dina data. Du kan identifiera en appkonfigurationshändelse eftersom eventType-egenskapen börjar med "Microsoft.AppConfiguration". Ytterligare information om användningen av händelserutnätshändelseegenskaper dokumenteras i [händelseschemat för Händelserutnät](../event-grid/event-schema.md).  

> |Egenskap|Typ|Beskrivning|
> |-------------------|------------------------|-----------------------------------------------------------------------|
> |ämne|sträng|Fullständigt Azure Resource Manager-ID för appkonfigurationen som avger händelsen.|
> |Ämne|sträng|URI för nyckelvärdet som är ämnet för händelsen.|
> |Händelsetid|sträng|Datum/tid då händelsen genererades i ISO 8601-format.|
> |Händelsetyp|sträng|"Microsoft.AppConfiguration.KeyValueModified" eller "Microsoft.AppConfiguration.KeyValueDeleted".|
> |Id|sträng|En unik identifierare för den här händelsen.|
> |Dataversion|sträng|Dataobjektets schemaversion.|
> |Metadataversion|sträng|Schemaversionen av egenskaper på den högsta nivån.|
> |data|objekt|Insamling av specifika händelsedata för Azure App-konfiguration|
> |data.key|sträng|Nyckeln till nyckelvärdet som har ändrats eller tagits bort.|
> |data.etikett|sträng|Etiketten, om sådan finns, av nyckelvärdet som har ändrats eller tagits bort.|
> |data.etag|sträng|För `KeyValueModified` etag av det nya nyckelvärdet. För `KeyValueDeleted` etag av nyckelvärdet som togs bort.|

Här är ett exempel på en KeyValueModified händelse:
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

Mer information finns i [Azure App Configuration events schema](../event-grid/event-schema-app-configuration.md).

## <a name="practices-for-consuming-events"></a>Metoder för att konsumera händelser
Program som hanterar appkonfigurationshändelser bör följa dessa rekommenderade metoder:
> [!div class="checklist"]
> * Flera prenumerationer kan konfigureras för att dirigera händelser till samma händelsehanterare, så anta inte att händelser kommer från en viss källa. Kontrollera i stället meddelandets ämne för att säkerställa att appkonfigurationsinstansen skickar händelsen.
> * Kontrollera eventType och anta inte att alla händelser du får kommer att vara de typer du förväntar dig.
> * Använd etag-fälten för att förstå om din information om objekt fortfarande är uppdaterad.  
> * Använd sequencer-fälten för att förstå ordningen på händelser på ett visst objekt.
> * Använd ämnesfältet för att komma åt nyckelvärdet som har ändrats.


## <a name="next-steps"></a>Nästa steg

Läs mer om Event Grid och ge Azure App Configuration-händelser ett försök:

- [Om Event Grid](../event-grid/overview.md)
- [Dirigera Azure App-konfigurationshändelser till en anpassad webbslutpunkt](./howto-app-configuration-event.md)