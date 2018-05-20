---
title: Azure händelse rutnätet prenumeration schema
description: Beskriver egenskaper för att prenumerera på en händelse med Azure händelse rutnätet.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 05/02/2018
ms.author: babanisa
ms.openlocfilehash: cfb4dabea12f2988108d24b025e324cf05afb325
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2018
---
# <a name="event-grid-subscription-schema"></a>Händelseschema rutnätet prenumeration

Om du vill skapa en händelse rutnätet prenumeration skicka en begäran att skapa händelsen prenumerationen igen. Använd följande format:

```HTTP
PUT /subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/{resource-provider}/{resource-type}/{resource-name}/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

Till exempel vill skapa en händelseprenumeration för ett lagringskonto med namnet `examplestorage` i en resursgrupp med namnet `examplegroup`, Använd följande format:

```HTTP
PUT /subscriptions/{subscription-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageaccounts/examplestorage/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

Den här artikeln innehåller egenskaperna och schemat för brödtexten i begäran.
 
## <a name="event-subscription-properties"></a>Händelseegenskaper för prenumeration

| Egenskap  | Typ | Beskrivning |
| -------- | ---- | ----------- |
| Mål | objekt | Det objekt som definierar slutpunkten. |
| filter | objekt | Ett valfritt fält för att filtrera vilka typer av händelser. |

### <a name="destination-object"></a>Målobjekt

| Egenskap  | Typ | Beskrivning |
| -------- | ---- | ----------- |
| endpointType | sträng | Typ av slutpunkt för prenumerationen (webhook/HTTP, Event Hub eller kön). | 
| endpointUrl | sträng | Mål-URL för händelser i den här händelseprenumerationen. | 

### <a name="filter-object"></a>filtreringsobjekt

| Egenskap  | Typ | Beskrivning |
| -------- | ---- | ----------- |
| includedEventTypes | matris | Matcha när händelsetypen i händelsemeddelandet är exakt matchar ett av dessa namn för typen av händelse. Genererar ett fel när händelsenamn inte matchar typnamnen registrerade händelser för händelsekällan. Standard matchar alla händelsetyper. |
| subjectBeginsWith | sträng | En prefixmatchning filtrera ämnesfältet i meddelandet. Matchar alla standard eller tom sträng. | 
| subjectEndsWith | sträng | En suffix matchning filtrera ämnesfältet i meddelandet. Matchar alla standard eller tom sträng. |
| isSubjectCaseSensitive | sträng | Kontroller skiftlägeskänsliga matchningen för filter. |


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
      "includedEventTypes": [ "Microsoft.Storage.BlobCreated", "Microsoft.Storage.BlobDeleted" ],
      "subjectBeginsWith": "blobServices/default/containers/mycontainer/log",
      "subjectEndsWith": ".jpg",
      "isSubjectCaseSensitive ": "true"
    }
  }
}
```

## <a name="next-steps"></a>Nästa steg

* En introduktion till händelse rutnätet finns [vad är händelsen rutnätet?](overview.md)