---
title: "Azure händelse rutnätet prenumeration schema"
description: "Beskriver egenskaper för att prenumerera på en händelse med Azure händelse rutnätet."
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 08/17/2017
ms.author: babanisa
ms.openlocfilehash: eff2352066a76010d6d882a7b7e1961870cd2d46
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="event-grid-subscription-schema"></a>Händelseschema rutnätet prenumeration

Om du vill skapa en händelse rutnätet prenumeration skicka en begäran att skapa händelsen prenumerationen igen. Använd följande format:

```
PUT /subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/{resource-provider}/{resource-type}/{resource-name}/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2017-06-15-preview
``` 

Till exempel vill skapa en händelseprenumeration för ett lagringskonto med namnet `examplestorage` i en resursgrupp med namnet `examplegroup`, Använd följande format:

```
PUT /subscriptions/{subscription-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageaccounts/examplestorage/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2017-06-15-preview
``` 

Den här artikeln innehåller egenskaperna och schemat för brödtexten i begäran.
 
## <a name="event-subscription-properties"></a>Händelseegenskaper för prenumeration

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| Mål | Objektet | Det objekt som definierar slutpunkten. |
| Filter | Objektet | Ett valfritt fält för att filtrera vilka typer av händelser. |

### <a name="destination-object"></a>Målobjekt

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| endpointType | Sträng | Typ av slutpunkt för prenumerationen (webhook/HTTP, Event Hub eller kön). | 
| endpointUrl | Sträng |  | 

### <a name="filter-object"></a>filtreringsobjekt

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| includedEventTypes | matris | Matcha när händelsetypen i händelsemeddelandet är exakt matchar ett av dessa namn för typen av händelse. Genererar ett fel när händelsenamn inte matchar typnamnen registrerade händelser för händelsekällan. Standard matchar alla händelsetyper. |
| subjectBeginsWith | Sträng | En prefixmatchning filtrera ämnesfältet i meddelandet. Matchar alla standard eller tom sträng. | 
| subjectEndsWith | Sträng | En suffix matchning filtrera ämnesfältet i meddelandet. Matchar alla standard eller tom sträng. |
| subjectIsCaseSensitive | Sträng | Kontroller skiftlägeskänsliga matchningen för filter. |


## <a name="example-subscription-schema"></a>Exempel prenumeration schema

```json
{
  "properties": {
    "destination": {
      "endpointType": "webhook",
      "properties": {
          "endpointUrl": "https://example.azurewebsites.net/api/HttpTriggerCSharp1?code=VXbGWce53l48Mt8wuotr0GPmyJ/nDT4hgdFj9DpBiRt38qqnnm5OFg=="
      }
    },
    "filter": {
      "includedEventTypes": [ "blobCreated", "blobDeleted" ],
      "subjectBeginsWith": "blobServices/default/containers/mycontainer/log",
      "subjectEndsWith": ".jpg",
      "subjectIsCaseSensitive": "true"
    }
  }
}
```

## <a name="next-steps"></a>Nästa steg

* En introduktion till händelse rutnätet finns [vad är händelsen rutnätet?](overview.md)