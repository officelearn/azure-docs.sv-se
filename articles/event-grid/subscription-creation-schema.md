---
title: "Azure händelse rutnätet prenumeration schema"
description: "Beskriver egenskaper för att prenumerera på en händelse med Azure händelse rutnätet."
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 01/30/2018
ms.author: babanisa
ms.openlocfilehash: a915473c67a7577582837b56d1a9ccec4d21c461
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
---
# <a name="event-grid-subscription-schema"></a>Händelseschema rutnätet prenumeration

Om du vill skapa en händelse rutnätet prenumeration skicka en begäran att skapa händelsen prenumerationen igen. Använd följande format:

```
PUT /subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/{resource-provider}/{resource-type}/{resource-name}/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

Till exempel vill skapa en händelseprenumeration för ett lagringskonto med namnet `examplestorage` i en resursgrupp med namnet `examplegroup`, Använd följande format:

```
PUT /subscriptions/{subscription-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageaccounts/examplestorage/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

Den här artikeln innehåller egenskaperna och schemat för brödtexten i begäran.
 
## <a name="event-subscription-properties"></a>Händelseegenskaper för prenumeration

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| destination | objekt | Det objekt som definierar slutpunkten. |
| filter | objekt | Ett valfritt fält för att filtrera vilka typer av händelser. |

### <a name="destination-object"></a>Målobjekt

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| endpointType | sträng | Typ av slutpunkt för prenumerationen (webhook/HTTP, Event Hub eller kön). | 
| endpointUrl | sträng | Mål-URL för händelser i den här händelseprenumerationen. | 

### <a name="filter-object"></a>filtreringsobjekt

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| includedEventTypes | matris | Matcha när händelsetypen i händelsemeddelandet är exakt matchar ett av dessa namn för typen av händelse. Genererar ett fel när händelsenamn inte matchar typnamnen registrerade händelser för händelsekällan. Standard matchar alla händelsetyper. |
| subjectBeginsWith | sträng | En prefixmatchning filtrera ämnesfältet i meddelandet. Matchar alla standard eller tom sträng. | 
| subjectEndsWith | sträng | En suffix matchning filtrera ämnesfältet i meddelandet. Matchar alla standard eller tom sträng. |
| subjectIsCaseSensitive | sträng | Kontroller skiftlägeskänsliga matchningen för filter. |


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