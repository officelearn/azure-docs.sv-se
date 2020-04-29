---
title: Azure Event Grid prenumerations schema
description: I den här artikeln beskrivs egenskaperna för att prenumerera på en händelse med Azure Event Grid. Event Grid prenumerations schema.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: reference
ms.date: 01/23/2020
ms.author: babanisa
ms.openlocfilehash: 4bb04d22b762f31a02515549b698030a5267e4cd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "76720766"
---
# <a name="event-grid-subscription-schema"></a>Event Grid prenumerations schema

Om du vill skapa en Event Grid-prenumeration skickar du en begäran till prenumerations åtgärden Skapa händelse. Använd följande format:

```HTTP
PUT /subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/{resource-provider}/{resource-type}/{resource-name}/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

Om du till exempel vill skapa en händelse prenumeration för ett lagrings `examplestorage` konto som heter i en `examplegroup`resurs grupp med namnet, använder du följande format:

```HTTP
PUT /subscriptions/{subscription-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageaccounts/examplestorage/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

Händelse prenumerationens namn måste vara 3-64 tecken långt och får bara innehålla a-z, A-Z, 0-9 och "-". I artikeln beskrivs egenskaperna och schemat för bröd texten i begäran.
 
## <a name="event-subscription-properties"></a>Egenskaper för händelse prenumeration

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| mål | objekt | Objektet som definierar slut punkten. |
| filter | objekt | Ett valfritt fält för filtrering av händelse typer. |

### <a name="destination-object"></a>mål objekt

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| endpointType | sträng | Typ av slut punkt för prenumerationen (webhook/HTTP, Händelsehubben eller kö). | 
| endpointUrl | sträng | Mål-URL: en för händelser i den här händelse prenumerationen. | 

### <a name="filter-object"></a>Filtrera objekt

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| includedEventTypes | matris | Matcha när händelse typen i händelse meddelandet är en exakt matchning till något av de här händelse typ namnen. Genererar ett fel när händelse namnet inte matchar registrerade händelse typ namn för händelse källan. Standard matchar alla händelse typer. |
| subjectBeginsWith | sträng | Ett prefix-match-filter till ämnes fältet i händelse meddelandet. Standardvärdet eller den tomma strängen matchar alla. | 
| subjectEndsWith | sträng | Ett suffix matchnings filter till ämnes fältet i händelse meddelandet. Standardvärdet eller den tomma strängen matchar alla. |
| isSubjectCaseSensitive | sträng | Kontrollerar Skift läges känslig matchning för filter. |


## <a name="example-subscription-schema"></a>Exempel på prenumerations schema

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
