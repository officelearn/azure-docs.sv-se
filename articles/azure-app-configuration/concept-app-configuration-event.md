---
title: Reagerar på Azure App konfigurations nyckel-värde händelser | Microsoft Docs
description: Använd Azure Event Grid för att prenumerera på program konfigurations händelser.
services: azure-app-configuration,event-grid
author: jimmyca
ms.author: jimmyca
ms.date: 05/30/2019
ms.topic: article
ms.service: azure-app-configuration
ms.openlocfilehash: 5da64155f2823712eee7a60427b1c1e80abec068
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185287"
---
# <a name="reacting-to-azure-app-configuration-events"></a>Reagerar på Azure App konfigurations händelser

Azure App konfigurations händelser gör det möjligt för program att reagera på ändringar i nyckel värden. Detta görs utan behov av komplicerad kod eller dyra och ineffektiva avsöknings tjänster. I stället flyttas händelser via [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) till prenumeranter som [Azure Functions](https://azure.microsoft.com/services/functions/), [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)eller till och med din egen anpassade http-lyssnare och du betalar bara för det du använder.

Azure App konfigurations händelser skickas till Azure Event Grid som tillhandahåller pålitliga leverans tjänster till dina program genom omfattande principer för återförsök och leverans av obeställbara meddelanden. Läs mer i [Event Grid meddelande leverans och försök igen](https://docs.microsoft.com/azure/event-grid/delivery-and-retry).

Vanliga konfigurations scenarier för appar omfattar uppdatering av program konfiguration, aktivering av distributioner eller konfigurations-orienterade arbets flöden. När ändringar är ovanliga, men scenariot kräver omedelbar svars tid, kan den händelsebaserade arkitekturen vara särskilt effektiv.

Ta en titt på [Route Azure App konfigurations händelser till en anpassad webb slut punkt – CLI](./howto-app-configuration-event.md) för ett enkelt exempel. 

![Event Grid modell](./media/event-grid-functional-model.png)

## <a name="available-azure-app-configuration-events"></a>Tillgängliga Azure App konfigurations händelser
Event Grid använder [händelse prenumerationer](../event-grid/concepts.md#event-subscriptions) för att dirigera händelse meddelanden till prenumeranter. Azure App konfigurations händelse prenumerationer kan innehålla två typer av händelser:  

> |Händelse namn|Beskrivning|
> |----------|-----------|
> |`Microsoft.AppConfiguration.KeyValueModified`|Utlöses när ett nyckel värde skapas eller ersätts|
> |`Microsoft.AppConfiguration.KeyValueDeleted`|Utlöses när ett nyckel värde tas bort|

## <a name="event-schema"></a>Händelseschema
Azure App konfigurations händelser innehåller all information som du behöver för att svara på ändringar i dina data. Du kan identifiera en konfigurations händelse för appen eftersom egenskapen eventType börjar med "Microsoft. AppConfiguration". Ytterligare information om användningen av Event Grid händelse egenskaper dokumenteras i [Event Grid händelse schema](../event-grid/event-schema.md).  

> |Egenskap|Typ|Beskrivning|
> |-------------------|------------------------|-----------------------------------------------------------------------|
> |subject|string|Fullständigt Azure Resource Manager-ID för den app-konfiguration som utsänder händelsen.|
> |subject|string|URI för det nyckel värde som är ämnet för händelsen.|
> |eventTime|string|Datum/tid då händelsen genererades, i ISO 8601-format.|
> |eventType|string|"Microsoft. AppConfiguration. KeyValueModified" eller "Microsoft. AppConfiguration. KeyValueDeleted".|
> |ID|string|En unik identifierare för den här händelsen.|
> |dataVersion|string|Data objektets schema version.|
> |metadataVersion|string|Schema versionen för toppnivå egenskaper.|
> |data|object|Samling med Azure App konfiguration av vissa händelse data|
> |data.key|string|Nyckeln till det nyckel värde som ändrades eller togs bort.|
> |data. label|string|Etiketten, om det finns, för det nyckel värde som ändrades eller togs bort.|
> |data.etag|string|För `KeyValueModified` etag för det nya nyckel värdet. För `KeyValueDeleted` etag för det nyckel värde som har tagits bort.|

Här är ett exempel på en KeyValueModified-händelse:
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

Mer information finns i [schemat för Azure App konfigurations händelser](../event-grid/event-schema-app-configuration.md).

## <a name="practices-for-consuming-events"></a>Metoder för att konsumera händelser
Program som hanterar konfigurations händelser för appar bör följa några rekommenderade metoder:
> [!div class="checklist"]
> * Eftersom flera prenumerationer kan konfigureras för att dirigera händelser till samma händelse hanterare, är det viktigt att inte anta att händelser kommer från en viss källa, men för att kontrol lera ämnet i meddelandet för att se till att det kommer från den app-konfiguration som du förväntar dig.
> * På samma sätt kan du kontrol lera att eventType är att du är för beredd att bearbeta och inte förutsätter att alla händelser som du tar emot är de typer som du förväntar dig.
> * När meddelanden kan komma in i rätt ordning och efter en viss fördröjning använder du etag-fälten för att förstå om din information om objekt fortfarande är uppdaterad.  Använd också sekvenserare-fälten för att förstå ordningen på händelser för ett visst objekt.
> * Använd fältet ämne för att få åtkomst till det nyckel värde som ändrades.


## <a name="next-steps"></a>Nästa steg

Läs mer om Event Grid och ge Azure App konfigurations händelser ett försök:

- [Om Event Grid](../event-grid/overview.md)
- [Dirigera Azure App konfigurations händelser till en anpassad webb slut punkt](./howto-app-configuration-event.md)