---
title: Karta kognitiva Sök utökat inmatningsfält utdatafält i Azure Search index | Microsoft Docs
description: Extrahera och utöka källa datafält och mappar till utdatafält i ett Azure Search-index.
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 67e4798070a73eebb8f61b0b260e3104e9ae6237
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
ms.locfileid: "33790953"
---
# <a name="how-to-map-enriched-fields-to-a-searchable-index"></a>Utökade mappning till ett sökbart index

I den här artikeln får lära du att mappa utökade indatafält till utdatafält i ett sökbart index. När du har [definieras en kunskaper](cognitive-search-defining-skillset.md), måste du mappa fälten utdata för alla kunskaper som bidrar med värden till ett visst fält i din sökindex direkt. Mappningar av fältet krävs för att flytta innehåll från utökade dokument i indexet.


## <a name="use-outputfieldmappings"></a>Använd outputFieldMappings
Mappa fält, Lägg till `outputFieldMappings` till indexeraren-definitionen enligt nedan:

```http
PUT https://[servicename].search.windows.net/indexers/[indexer name]?api-version=2017-11-11-Preview
api-key: [admin key]
Content-Type: application/json
```

Brödtexten i begäran är strukturerad på följande sätt:

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
Ange namnet på fältet utökade (sourceFieldName) och namnet på fältet som refereras till i indexet (targetFieldName) för varje fältmappning utdata.

Sökvägen i en sourceFieldName kan representera en eller flera element. I exemplet ovan, ```/document/content/sentiment``` representerar ett numeriskt värde, medan ```/document/content/organizations/*/description``` representerar flera organisation beskrivningar. I fall där det finns flera element, de ”förenklas” till en matris som innehåller varje element. Fler concretely för den ```/document/content/organizations/*/description``` exempel, data i den *beskrivningar* fält som ser ut som en platt uppsättning beskrivningar innan det hämtar indexeras:

```
 ["Microsoft is a company in Seattle","LinkedIn's office is in San Francisco"]
```
## <a name="next-steps"></a>Nästa steg
När du har mappat utökade fälten till sökbara fält, du kan ange i fältattribut för varje sökbara fält [som en del av indexdefinitionen](search-what-is-an-index.md).

Mer information om avbildning finns [fältet avbildningar i Azure Search indexerare](search-indexer-field-mappings.md).