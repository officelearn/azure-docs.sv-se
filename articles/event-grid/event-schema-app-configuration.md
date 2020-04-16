---
title: Azure App-konfiguration som källa för händelserutnät
description: I den här artikeln beskrivs hur du använder Azure App-konfiguration som en händelserutnätshändelsekälla. Det ger schemat och länkar till handledning och instruktionsartiklar.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: babanisa
ms.openlocfilehash: adb548ef8531698a2cb075fbc742bb20a02a434b
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393434"
---
# <a name="azure-app-configuration-as-an-event-grid-source"></a>Azure App-konfiguration som en händelserutnätskälla
Den här artikeln innehåller egenskaper och schema för Azure App Configuration-händelser. En introduktion till händelsescheman finns i [Azure Event Grid-händelseschema](event-schema.md). Det ger dig också en lista över snabbstarter och självstudier för att använda Azure App Configuration som en händelsekälla.

## <a name="event-grid-event-schema"></a>Händelseschema för händelserutnät

### <a name="available-event-types"></a>Tillgängliga händelsetyper

Azure App-konfigurationen avger följande händelsetyper:

| Händelsetyp | Beskrivning |
| ---------- | ----------- |
| Microsoft.AppConfiguration.KeyValueModified | Utlöses när ett nyckelvärde skapas eller ersätts. |
| Microsoft.AppConfiguration.KeyValueDeleted | Utlöses när ett nyckelvärde tas bort. |

### <a name="example-event"></a>Exempel händelse

I följande exempel visas schemat för en nyckelvärdesmodifierad händelse: 

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

Schemat för en borttagen nyckelvärde-händelse är liknande: 

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
 
### <a name="event-properties"></a>Händelseegenskaper

En händelse har följande data på den högsta nivån:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| ämne | sträng | Fullständig resurssökväg till händelsekällan. Det här fältet kan inte skrivas. Event Grid ger det här värdet. |
| Ämne | sträng | Utgivardefinierad sökväg till händelseobjektet. |
| Händelsetyp | sträng | En av de registrerade händelsetyperna för den här händelsekällan. |
| Händelsetid | sträng | Den tid som händelsen genereras baserat på leverantörens UTC-tid. |
| ID | sträng | Unik identifierare för händelsen. |
| data | objekt | Händelsedata för appkonfiguration. |
| Dataversion | sträng | Dataobjektets schemaversion. Utgivaren definierar schemaversion. |
| Metadataversion | sträng | Schemaversionen av händelsens metadata. Event Grid definierar schemat för de översta egenskaperna. Event Grid ger det här värdet. |

Dataobjektet har följande egenskaper:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| key | sträng | Nyckeln till nyckelvärdet som har ändrats eller tagits bort. |
| etikett | sträng | Etiketten, om sådan finns, av nyckelvärdet som har ändrats eller tagits bort. |
| Etag | sträng | För `KeyValueModified` etag av det nya nyckelvärdet. För `KeyValueDeleted` etag av nyckelvärdet som togs bort. |

## <a name="tutorials-and-how-tos"></a>Självstudier och instruktioner

|Titel | Beskrivning |
|---------|---------|
| [Reagera på Azure App-konfigurationshändelser med hjälp av Event Grid](../azure-app-configuration/concept-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Översikt över integreringen av Azure App Configuration med Event Grid. |
| [Snabbstart: dirigera Azure App-konfigurationshändelser till en anpassad webbslutpunkt med Azure CLI](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Visar hur du använder Azure CLI för att skicka Azure App Configuration-händelser till en WebHook. |

## <a name="next-steps"></a>Nästa steg

* En introduktion till Azure Event Grid finns i [Vad är Event Grid?](overview.md)
* Mer information om hur du skapar en Azure Event Grid-prenumeration finns i [Prenumerationsschema för Event Grid](subscription-creation-schema.md).
* En introduktion till att arbeta med Azure App-konfigurationshändelser finns i [Route Azure App Configuration events - Azure CLI](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json). 