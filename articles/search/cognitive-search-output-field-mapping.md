---
title: Mappa indata till utdatakolumner
titleSuffix: Azure Cognitive Search
description: Extrahera och utöka käll data fält och mappa till utmatnings fält i ett Azure Kognitiv sökning-index.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: d2d5e717154d16cc5579c1495aff9c1eebf54b17
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132375"
---
# <a name="how-to-map-ai-enriched-fields-to-a-searchable-index"></a>Så här mappar du AI-berikade fält till ett sökbart index

I den här artikeln får du lära dig hur du mappar informativa inmatnings fält till utdatakolumner i ett sökbart index. När du har [definierat en färdigheter](cognitive-search-defining-skillset.md)måste du mappa utmatnings fälten för alla färdigheter som direkt bidrar med värden till ett angivet fält i ditt sökindex. Fält mappningar krävs för att flytta innehåll från berikade dokument till indexet.


## <a name="use-outputfieldmappings"></a>Använd outputFieldMappings
Om du vill mappa fält lägger du till `outputFieldMappings` i din index definition enligt nedan:

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

Sökvägen i en sourceFieldName kan representera ett eller flera element. I exemplet ovan representerar ```/document/content/sentiment``` ett enskilt numeriskt värde medan ```/document/content/organizations/*/description``` representerar flera organisations beskrivningar. I de fall där det finns flera element blir de "förenklade" i en matris som innehåller varje element. Mer konkret, för ```/document/content/organizations/*/description``` exemplet, skulle data i fältet *beskrivningar* se ut som en fast uppsättning beskrivningar innan de indexeras:

```
 ["Microsoft is a company in Seattle","LinkedIn's office is in San Francisco"]
```
## <a name="next-steps"></a>Nästa steg
När du har mappat dina berikade fält till sökbara fält kan du ange fältattribut för varje sökbart fält [som en del av index definitionen](search-what-is-an-index.md).

Mer information om fält mappning finns i [fält mappningar i Azure kognitiv sökning indexerare](search-indexer-field-mappings.md).
