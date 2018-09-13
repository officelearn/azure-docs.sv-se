---
title: Kartan kognitiv sökning berikats inmatningsfält till utdatafält i Azure Search-index | Microsoft Docs
description: Extrahera och berika data källfält och mappar till utdatafält i ett Azure Search-index.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 77e7a1cc725dc56ee20d3c1999cfb7cf0039d67f
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "35965816"
---
# <a name="how-to-map-enriched-fields-to-a-searchable-index"></a>Avancerad och mappning till ett sökbart index

I den här artikeln får du lära dig mappning avancerad och indata till utdatafält i ett sökbart index. När du har [definieras en kompetens](cognitive-search-defining-skillset.md), måste du mappa fält för tabellutdata för alla färdigheter som direkt bidrar värden till ett visst fält i sökindexet. Fältmappningar krävs för att flytta innehåll från avancerad och dokument i indexet.


## <a name="use-outputfieldmappings"></a>Använda outputFieldMappings
Om du vill mappa fält, lägga till `outputFieldMappings` till din indexerardefinitionen enligt nedan:

```http
PUT https://[servicename].search.windows.net/indexers/[indexer name]?api-version=2017-11-11-Preview
api-key: [admin key]
Content-Type: application/json
```

Brödtexten i begäran är strukturerade på följande sätt:

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
Ange namnet på fältet avancerad och (sourceFieldName) och namnet på fältet som refereras till i indexet (targetFieldName) för varje fältmappning för utdata.

Sökvägen i en sourceFieldName kan representera en eller flera element. I exemplet ovan, ```/document/content/sentiment``` representerar ett numeriskt värde, medan ```/document/content/organizations/*/description``` representerar flera organisation beskrivningar. I fall där det finns flera element, de är ”förenklas” till en matris som innehåller varje element. Fler concretely för den ```/document/content/organizations/*/description``` exempel måste data i den *beskrivningar* fältet skulle se ut som en fast mängd beskrivningar innan de indexeras:

```
 ["Microsoft is a company in Seattle","LinkedIn's office is in San Francisco"]
```
## <a name="next-steps"></a>Nästa steg
När du har mappat dina avancerad och fält till sökbara fält kan du ange fältattributen för var och en av de sökbara fälten [som en del av indexdefinitionen](search-what-is-an-index.md).

Läs mer om fältmappning [fältmappningar för Azure Search-indexerare](search-indexer-field-mappings.md).