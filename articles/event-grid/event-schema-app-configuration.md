---
title: Händelseschema för Azure Event Grid Azure-Appkonfiguration
description: Beskriver de egenskaper som har angetts för konfiguration av Azure-händelser med Azure Event Grid
services: event-grid
author: jimmyca
ms.service: event-grid
ms.topic: reference
ms.date: 05/30/2019
ms.author: jimmyca
ms.openlocfilehash: fe0274f723692eea3cfd25cc0e9e146b35dce2ae
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66735788"
---
# <a name="azure-event-grid-event-schema-for-azure-app-configuration"></a>Azure Event Grid-Händelseschema för konfiguration av Azure

Den här artikeln innehåller egenskaperna och schemat för konfiguration av Azure-händelser. En introduktion till Händelsescheman i [Azure Event Grid Händelseschema](event-schema.md).

En lista över exempel på skript och självstudier finns i [konfiguration av Azure-händelsekälla](event-sources.md#app-configuration).

## <a name="available-event-types"></a>Tillgängliga händelsetyper

Konfiguration av Azure genererar följande händelsetyper:

| eventType | Beskrivning |
| ---------- | ----------- |
| Microsoft.AppConfiguration.KeyValueModified | Utlöses när en nyckel / värde-skapas eller ersatts. |
| Microsoft.AppConfiguration.KeyValueDeleted | Utlöses när en nyckel / värde-tas bort. |

## <a name="example-event"></a>Exempel-händelse

I följande exempel visar schemat för en ändrad nyckel / värde-händelse: 

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

Schemat för en borttagen händelse nyckel / värde-liknar: 

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
  "eventType": "Microsoft.AppConfiguration.KeyValueDeleted",
  "eventTime": "2019-05-31T20:05:03Z",
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```
 
## <a name="event-properties"></a>Egenskaper för händelse

En händelse har följande översta data:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| topic | string | Fullständig resurssökväg till händelsekällan. Det här fältet är skrivskyddat. Event Grid ger det här värdet. |
| topic | string | Publisher-definierade sökvägen till ämne för händelsen. |
| eventType | string | En av typerna som registrerade händelsen för den här händelsekällan. |
| eventTime | string | Den tid som händelsen genereras baserat på leverantörens UTC-tid. |
| id | string | Unik identifierare för händelsen. |
| data | objekt | Händelsedata för App-konfiguration. |
| dataVersion | string | Dataobjektets schemaversion. Utgivaren definierar schemaversion. |
| metadataVersion | string | Schemaversion för händelsemetadata. Event Grid definierar schemat för de översta egenskaperna. Event Grid ger det här värdet. |

Dataobjektet har följande egenskaper:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| key | string | Nyckeln för nyckel / värde som har ändrats eller tagits bort. |
| label | string | Etiketten, om något av nyckel / värde som har ändrats eller tagits bort. |
| etag | string | För `KeyValueModified` etag för det nya nyckel-värdet. För `KeyValueDeleted` etag nyckel / värde som har tagits bort. |
 
## <a name="next-steps"></a>Nästa steg

* En introduktion till Azure Event Grid finns i [vad är Event Grid?](overview.md)
* Läs mer om hur du skapar en Azure Event Grid-prenumeration, [Event Grid prenumerationsschema](subscription-creation-schema.md).
* Läs en introduktion till Azure App-konfigurationshändelser [väg konfiguration av Azure-händelser – Azure CLI](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json). 