---
title: Mappa indata till utdatafält
titleSuffix: Azure Cognitive Search
description: Extrahera och berika källdatafält och mappa till utdatafält i ett Azure Cognitive Search-index.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f0537af684632a08a39e3e681900d62238365073
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74280973"
---
# <a name="how-to-map-ai-enriched-fields-to-a-searchable-index"></a>Så här mappar du AI-berikade fält till ett sökbart index

I den här artikeln får du lära dig hur du mappar utökade indatafält till utdatafält i ett sökbart index. När du har [definierat en kompetens](cognitive-search-defining-skillset.md)måste du mappa utdatafälten för alla färdigheter som direkt bidrar med värden till ett visst fält i sökindexet. 

Utdatafältmappningar krävs för att flytta innehåll från berikade dokument till indexet.  Det berikade dokumentet är verkligen ett informationsträd, och även om det finns stöd för komplexa typer i indexet, kanske du ibland vill omvandla informationen från det berikade trädet till en enklare typ (till exempel en matris med strängar). Med datafältsmappningar kan du utföra omvandlingar av dataform genom att förenkla information.

## <a name="use-outputfieldmappings"></a>Använda outputFieldMappings
Om du vill `outputFieldMappings` mappa fält lägger du till indexeringsdefinitionen enligt nedan:

```http
PUT https://[servicename].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
api-key: [admin key]
Content-Type: application/json
```

Begärans brödtext är strukturerad på följande sätt:

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

För varje utdatafältmappning anger du platsen för data i det berikade dokumentträdet (sourceFieldName) och namnet på fältet som refererat i indexet (targetFieldName).

## <a name="flattening-information-from-complex-types"></a>Förenkla information från komplexa typer 

Sökvägen i en sourceFieldName kan representera ett element eller flera element. I exemplet ovan ```/document/content/sentiment``` representerar ett enda ```/document/content/organizations/*/description``` numeriskt värde, medan det representerar flera organisationsbeskrivningar. 

I de fall där det finns flera element, är de "förenklade" i en matris som innehåller vart och ett av elementen. 

Mer konkret, till ```/document/content/organizations/*/description``` exempel, data i *beskrivningsfältet* skulle se ut som en platt matris med beskrivningar innan det blir indexerade:

```
 ["Microsoft is a company in Seattle","LinkedIn's office is in San Francisco"]
```

Detta är en viktig princip, så vi kommer att ge ytterligare ett exempel. Föreställ dig att du har en rad komplexa typer som en del av anrikningsträdet. Anta att det finns en medlem som heter anpassade entiteter som har en matris med komplexa typer som den som beskrivs nedan.

```json
"document/customEntities": 
[
    {
        "name": "heart failure",
        "matches": [
            {
                "text": "heart failure",
                "offset": 10,
                "length": 12,
                "matchDistance": 0.0
            }
        ]
    },
    {
        "name": "morquio",
        "matches": [
            {
                "text": "morquio",
                "offset": 25,
                "length": 7,
                "matchDistance": 0.0
            }
        ]
    }
    //...
]
```

Anta att indexet har ett fält som heter "sjukdomar" av typen Samling (Edm.String), där du vill lagra var och en av namnen på entiteterna. 

Detta kan göras enkelt genom\*att använda " " symbolen, enligt följande:

```json
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/customEntities/*/name",
            "targetFieldName": "diseases"
        }
    ]
```

Den här åtgärden kommer helt enkelt att "platta" vart och ett av namnen på de anpassade entitetselementen till en enda matris med strängar som denna:

```json
  "diseases" : ["heart failure","morquio"]
```

## <a name="next-steps"></a>Nästa steg
När du har mappat dina utökade fält till sökbara fält kan du ange fältattribut för vart och ett av de sökbara fälten [som en del av indexdefinitionen](search-what-is-an-index.md).

Mer information om fältmappning finns [i Fältmappningar i Azure Cognitive Search-indexerare](search-indexer-field-mappings.md).
