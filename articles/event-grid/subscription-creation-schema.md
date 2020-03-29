---
title: Azure Event Grid-prenumerationsschema
description: I den här artikeln beskrivs egenskaperna för att prenumerera på en händelse med Azure Event Grid. Prenumerationsschema för Event Grid.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: reference
ms.date: 01/23/2020
ms.author: babanisa
ms.openlocfilehash: 4bb04d22b762f31a02515549b698030a5267e4cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720766"
---
# <a name="event-grid-subscription-schema"></a>Prenumerationsschema för Event Grid

Om du vill skapa en Event Grid-prenumeration skickar du en begäran till prenumerationen Skapa händelse. Använd följande format:

```HTTP
PUT /subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/{resource-provider}/{resource-type}/{resource-name}/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

Om du till exempel vill skapa en `examplestorage` händelseprenumeration för ett lagringskonto som namnges i en resursgrupp med namnet `examplegroup`använder du följande format:

```HTTP
PUT /subscriptions/{subscription-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageaccounts/examplestorage/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

Namnet på händelseprenumerationen måste vara 3-64 tecken långt och kan bara innehålla a-z, A-Ö, 0-9 och "-". I artikeln beskrivs egenskaperna och schemat för brödtexten för begäran.
 
## <a name="event-subscription-properties"></a>Egenskaper för händelseprenumeration

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| mål | objekt | Objektet som definierar slutpunkten. |
| filter | objekt | Ett valfritt fält för filtrering av typer av händelser. |

### <a name="destination-object"></a>målobjekt

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| slutpunktTyp | sträng | Typ av slutpunkt för prenumerationen (webhook/HTTP, Event Hub eller kö). | 
| slutpunktUrl | sträng | Mål-URL:en för händelser i den här händelseprenumerationen. | 

### <a name="filter-object"></a>filtrera objekt

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| inkluderadeEventTypes | matris | Matcha när händelsetypen i händelsemeddelandet är en exakt matchning till ett av dessa händelsetypsnamn. Väcker ett fel när händelsenamnet inte matchar de registrerade händelsetypsnamnen för händelsekällan. Standard matchar alla händelsetyper. |
| ämneBeginsWith | sträng | Ett prefixmatchningsfilter till ämnesfältet i händelsemeddelandet. Standardsträngen eller den tomma strängen matchar alla. | 
| subjectEndsWith | sträng | Ett suffixmatchningsfilter till ämnesfältet i händelsemeddelandet. Standardsträngen eller den tomma strängen matchar alla. |
| ärSbjectCaseSensitive | sträng | Styr skiftlägeskänslig matchning för filter. |


## <a name="example-subscription-schema"></a>Exempel på prenumerationsschema

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

* En introduktion till Händelserutnätet finns i [Vad är händelserutnät?](overview.md)
