---
title: Mappa kognitiva sökfält för att mata in fält – Azure Search
description: Extrahera och utöka käll data fält och mappa till utdatakolumner i ett Azure Search index.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.subservice: cognitive-search
ms.openlocfilehash: 1d1ef7be83601a1ccf09ac52f87d0fcc953c605c
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2019
ms.locfileid: "69639194"
---
# <a name="how-to-map-enriched-fields-to-a-searchable-index"></a>Så här mappar du omfattande fält till ett sökbart index

I den här artikeln får du lära dig hur du mappar informativa inmatnings fält till utdatakolumner i ett sökbart index. När du har [definierat en färdigheter](cognitive-search-defining-skillset.md)måste du mappa utmatnings fälten för alla färdigheter som direkt bidrar med värden till ett angivet fält i ditt sökindex. Fält mappningar krävs för att flytta innehåll från berikade dokument till indexet.


## <a name="use-outputfieldmappings"></a>Använd outputFieldMappings
Om du vill mappa fält `outputFieldMappings` lägger du till den i index definitions definitionen enligt nedan:

```http
PUT https://[servicename].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
api-key: [admin key]
Content-Type: application/json
```

Bröd texten i begäran är strukturerad enligt följande:

```json
{
    "name": "myIndexer",
    "dataSourceName": "myDataSource",
    "targetIndexName": "myIndex",
    "skillsetName": "myFirstSkillSet",
    "fieldMappings": [
        {
            "sourceFieldName": "metadata_storage_path",
            "targetFieldName": "id",
            "mappingFunction": {
                "name": "base64Encode"
            }
        }
    ],
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/content/organizations/*/description",
            "targetFieldName": "descriptions"
        },
        {
            "sourceFieldName": "/document/content/organizations",
            "targetFieldName": "orgNames"
        },
        {
            "sourceFieldName": "/document/content/sentiment",
            "targetFieldName": "sentiment"
        }
    ]
}
```
För varje fält mappning för utdata anger du namnet på det berikade fältet (sourceFieldName) och namnet på fältet som det refereras till i indexet (targetFieldName).

Sökvägen i en sourceFieldName kan representera ett eller flera element. I exemplet ovan ```/document/content/sentiment``` representerar ett enkelt numeriskt värde, medan ```/document/content/organizations/*/description``` representerar flera organisations beskrivningar. I de fall där det finns flera element blir de "förenklade" i en matris som innehåller varje element. Mer konkret, ```/document/content/organizations/*/description``` i exemplet skulle data i fältet *beskrivningar* se ut som en plan mat ris med beskrivningar innan de indexeras:

```
 ["Microsoft is a company in Seattle","LinkedIn's office is in San Francisco"]
```
## <a name="next-steps"></a>Nästa steg
När du har mappat dina berikade fält till sökbara fält kan du ange fältattribut för varje sökbart fält [som en del av index definitionen](search-what-is-an-index.md).

Mer information om fält mappning finns [i fält mappningar i Azure Search indexerare](search-indexer-field-mappings.md).
