---
title: Händelsescheman – Azure Event Grid IoT Edge | Microsoft-dokument
description: Händelsescheman i händelserutnät på IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: ba261aeedf6574f69d3c05f8fd005c912dcc59d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73242460"
---
# <a name="event-schemas"></a>Händelsescheman

Event Grid-modulen accepterar och levererar händelser i JSON-format. Det finns för närvarande tre scheman som stöds av Event Grid: -

* **EventGridSchema**
* **Anpassatschema**
* **CloudEventSchema**

Du kan konfigurera schemat som en utgivare måste följa när ämnet skapas. Om det inte anges, standard är **EventGridSchema**. Händelser som inte överensstämmer med det förväntade schemat kommer att avvisas.

Prenumeranter kan också konfigurera schemat där de vill att händelserna ska levereras. Om det inte anges är standardschemat för ämnet.
För närvarande måste prenumerantleveransschema matcha ämnets indataschema. 

## <a name="eventgrid-schema"></a>EventGrid-schema

EventGrid-schemat består av en uppsättning obligatoriska egenskaper som en publiceringsenhet måste följa. Varje utgivare måste fylla i fälten på den översta nivån.

```json
[
  {
    "topic": string,
    "subject": string,
    "id": string,
    "eventType": string,
    "eventTime": string,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string,
    "metadataVersion": string
  }
]
```

### <a name="eventgrid-schema-properties"></a>Egenskaper för EventGrid-schema

Alla händelser har följande data på den högsta nivån:

| Egenskap | Typ | Krävs | Beskrivning |
| -------- | ---- | ----------- |-----------
| ämne | sträng | Inga | Ska matcha det ämne som det publiceras på. Event Grid fyller den med namnet på det ämne som det publiceras om det är ospecificerat. |
| Ämne | sträng | Ja | Utgivardefinierad sökväg till händelseobjektet. |
| Händelsetyp | sträng | Ja | Händelsetyp för den här händelsekällan, till exempel BlobCreated. |
| Händelsetid | sträng | Ja | Den tid som händelsen genereras baserat på leverantörens UTC-tid. |
| ID | sträng | Inga | Unik identifierare för händelsen. |
| data | objekt | Inga | Används för att samla in händelsedata som är specifika för publiceringsentiteten. |
| Dataversion | sträng | Ja | Dataobjektets schemaversion. Utgivaren definierar schemaversion. |
| Metadataversion | sträng | Inga | Schemaversionen av händelsens metadata. Event Grid definierar schemat för de översta egenskaperna. Event Grid ger det här värdet. |

### <a name="example--eventgrid-schema-event"></a>Exempel — Händelse-schemahändelse

```json
[
  {
    "id": "1807",
    "eventType": "recordInserted",
    "subject": "myapp/vehicles/motorcycles",
    "eventTime": "2017-08-10T21:03:07+00:00",
    "data": {
      "make": "Ducati",
      "model": "Monster"
    },
    "dataVersion": "1.0"
  }
]
```

## <a name="customevent-schema"></a>CustomEvent-schema

I anpassat schema finns det inga obligatoriska egenskaper som tillämpas som EventGrid-schemat. Publiceringsentiteten kan styra händelseschemat helt och hållet. Det ger maximal flexibilitet och möjliggör scenarier där du redan har ett händelsebaserat system och vill återanvända befintliga händelser och/eller inte vill vara bunden till ett visst schema.

### <a name="custom-schema-properties"></a>Anpassade schemaegenskaper

Inga obligatoriska egenskaper. Det är upp till publiceringsentiteten att bestämma nyttolasten.

### <a name="example--custom-schema-event"></a>Exempel – Anpassad schemahändelse

```json
[
  {
    "eventdata": {
      "make": "Ducati",
      "model": "Monster"
    }
  }
]
```

## <a name="cloudevent-schema"></a>CloudEvent-schema

Utöver ovanstående scheman stöder Event Grid inbyggt händelser i [CloudEvents JSON-schemat](https://github.com/cloudevents/spec/blob/master/json-format.md). CloudEvents är en öppen specifikation för att beskriva händelsedata. Det förenklar interoperabilitet genom att tillhandahålla ett gemensamt händelseschema för publicering och tidskrävande händelser. Det är en del av [CNCF](https://www.cncf.io/) och för närvarande tillgänglig version är 1,0-rc1.

### <a name="cloudevent-schema-properties"></a>CloudEvent-schemaegenskaper

Se [CloudEvents-specifikationen](https://github.com/cloudevents/spec/blob/master/json-format.md#3-envelope) på de obligatoriska kuvertegenskaperna.

### <a name="example--cloud-event"></a>Exempel – molnhändelse
```json
[{
       "id": "1807",
       "type": "recordInserted",
       "source": "myapp/vehicles/motorcycles",
       "time": "2017-08-10T21:03:07+00:00",
       "datacontenttype": "application/json",
       "data": {
            "make": "Ducati",
            "model": "Monster"
        },
        "dataVersion": "1.0",
        "specVersion": "1.0-rc1"
}]
```
