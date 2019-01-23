---
title: Schema för Azure Event Grid-prenumeration
description: Beskriver egenskaperna för att prenumerera på en händelse med Azure Event Grid.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 01/02/2019
ms.author: babanisa
ms.openlocfilehash: 9464ab89e08f53f61cb6f5a4b1e91da35b785af0
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54460830"
---
# <a name="event-grid-subscription-schema"></a>Händelseschema Grid-prenumeration

Om du vill skapa en Event Grid-prenumeration, skicka en begäran att skapa händelse prenumerationen igen. Använd följande format:

```HTTP
PUT /subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/{resource-provider}/{resource-type}/{resource-name}/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

Till exempel att skapa en händelseprenumeration för ett lagringskonto med namnet `examplestorage` i en resursgrupp med namnet `examplegroup`, använder du följande format:

```HTTP
PUT /subscriptions/{subscription-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageaccounts/examplestorage/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

Händelsen prenumerationens namn måste vara 3 – 64 tecken långt och får bara innehålla a – z, A – Z, 0-9 och ”-”. Artikeln beskriver egenskaper och schemat för innehållet i begäran.
 
## <a name="event-subscription-properties"></a>Händelseegenskaper för prenumeration

| Egenskap  | Typ | Beskrivning |
| -------- | ---- | ----------- |
| mål | objekt | Det objekt som definierar slutpunkten. |
| filter | objekt | Ett valfritt fält för att filtrera typerna av händelser. |

### <a name="destination-object"></a>Målobjekt

| Egenskap  | Typ | Beskrivning |
| -------- | ---- | ----------- |
| EndpointType | sträng | Typ av slutpunkt för prenumerationen (webhook/HTTP, Event Hub eller kön). | 
| endpointUrl | sträng | Mål-URL för händelser i den här händelseprenumerationen. | 

### <a name="filter-object"></a>filterobjekt

| Egenskap  | Typ | Beskrivning |
| -------- | ---- | ----------- |
| includedEventTypes | matris | Matchningen när händelsetyp i händelsemeddelandet är en exakt matchning till någon av dessa namn för typen av händelse. Genererar ett fel när händelsenamn inte matchar de registrerade händelsen typnamn för händelsekällan. Standard matchar alla händelsetyper. |
| subjectBeginsWith | sträng | En prefixmatchning filtrera till ämnesfältet i meddelandet. Matchar alla standard- eller tom sträng. | 
| subjectEndsWith | sträng | Ett suffix-match filtrera till ämnesfältet i meddelandet. Matchar alla standard- eller tom sträng. |
| isSubjectCaseSensitive | sträng | Kontroller är skiftlägeskänslig matchning för filter. |


## <a name="example-subscription-schema"></a>Exempel prenumerationsschema

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

* En introduktion till Event Grid finns i [vad är Event Grid?](overview.md)