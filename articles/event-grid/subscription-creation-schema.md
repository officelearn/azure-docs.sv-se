---
title: Schema för Azure Event Grid-prenumeration
description: I den här artikeln beskrivs egenskaperna för att prenumerera på en händelse med Azure Event Grid. Event Grid prenumerations schema.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: reference
ms.date: 01/23/2020
ms.author: babanisa
ms.openlocfilehash: 4bb04d22b762f31a02515549b698030a5267e4cd
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720766"
---
# <a name="event-grid-subscription-schema"></a>Händelseschema Grid-prenumeration

Om du vill skapa en Event Grid-prenumeration, skicka en begäran att skapa händelse prenumerationen igen. Använd följande format:

```HTTP
PUT /subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/{resource-provider}/{resource-type}/{resource-name}/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

Om du till exempel vill skapa en händelse prenumeration för ett lagrings konto med namnet `examplestorage` i en resurs grupp med namnet `examplegroup`använder du följande format:

```HTTP
PUT /subscriptions/{subscription-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageaccounts/examplestorage/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

Händelsen prenumerationens namn måste vara 3 – 64 tecken långt och får bara innehålla a – z, A – Z, 0-9 och ”-”. Artikeln beskriver egenskaper och schemat för innehållet i begäran.
 
## <a name="event-subscription-properties"></a>Händelseegenskaper för prenumeration

| Egenskap | Typ | Description |
| -------- | ---- | ----------- |
| mål | object | Det objekt som definierar slutpunkten. |
| filter | object | Ett valfritt fält för att filtrera typerna av händelser. |

### <a name="destination-object"></a>Målobjekt

| Egenskap | Typ | Description |
| -------- | ---- | ----------- |
| endpointType | sträng | Typ av slutpunkt för prenumerationen (webhook/HTTP, Event Hub eller kön). | 
| endpointUrl | sträng | Mål-URL för händelser i den här händelseprenumerationen. | 

### <a name="filter-object"></a>filterobjekt

| Egenskap | Typ | Description |
| -------- | ---- | ----------- |
| includedEventTypes | array | Matchningen när händelsetyp i händelsemeddelandet är en exakt matchning till någon av dessa namn för typen av händelse. Genererar ett fel när händelsenamn inte matchar de registrerade händelsen typnamn för händelsekällan. Standard matchar alla händelsetyper. |
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
      "subjectBeginsWith": "/blobServices/default/containers/mycontainer/log",
      "subjectEndsWith": ".jpg",
      "isSubjectCaseSensitive ": "true"
    }
  }
}
```

## <a name="next-steps"></a>Nästa steg

* En introduktion till Event Grid finns i [Vad är event Grid?](overview.md)
