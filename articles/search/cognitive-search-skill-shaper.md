---
title: Kognitiv formarfärdighet
titleSuffix: Azure Cognitive Search
description: Extrahera metadata och strukturerad information från ostrukturerade data och forma den som en komplex typ i en AI-anrikningspipeline i Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 384b79037bb30656934c5e4b596dac2b776593b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75754117"
---
# <a name="shaper-cognitive-skill"></a>Kognitiv formarfärdighet

**Shaper-färdigheten** konsoliderar flera indata till en [komplex typ](search-howto-complex-data-types.md) som kan refereras senare i anrikningspipelinen. **Med Shaper-färdigheten** kan du i huvudsak skapa en struktur, definiera namnet på medlemmarna i den strukturen och tilldela värden till varje medlem. Exempel på konsoliderade fält som är användbara i sökscenarier är att kombinera ett för- och efternamn i en enda struktur, ort och stat till en enda struktur, eller namn och födelsedatum till en enda struktur för att skapa unik identitet.

Dessutom lägger **Shaper-färdigheten** som illustreras i [scenario 3](#nested-complex-types) till en valfri *sourceContext-egenskap* i indata. *Käll-* och *sourceContext-egenskaperna* utesluter varandra. Om indata är i samband med färdigheten, helt enkelt använda *källan*. Om indata är i en *annan* kontext än färdighetskontexten använder du *sourceContext*. *SourceContext* kräver att du definierar en kapslad indata med det specifika element som adresseras som källa. 

Utdatanamnet är alltid "utdata". Internt kan pipelinen mappa ett annat namn, till exempel "analyzedText" som visas i exemplen nedan, men Shaper-färdigheten själv **returnerar** "utdata" i svaret. Detta kan vara viktigt om du felsöker berikade dokument och märker namnavvikelsen, eller om du bygger en anpassad färdighet och strukturerar svaret själv.

> [!NOTE]
> **Shaper-färdigheten** är inte bunden till ett Cognitive Services API och du debiteras inte för att du använder den. Du bör fortfarande [bifoga en Cognitive Services-resurs](cognitive-search-attach-cognitive-services.md)för att åsidosätta alternativet **Kostnadsfri** resurs som begränsar dig till ett litet antal dagliga berikningar per dag.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ShaperSkill

## <a name="scenario-1-complex-types"></a>Scenario 1: komplexa typer

Tänk dig ett scenario där du vill skapa en struktur som kallas *analyzedText* som har två medlemmar: *text* respektive *sentiment*. I ett index kallas ett sökbart fält i flera delar för en *komplex typ* och det skapas ofta när källdata har en motsvarande komplex struktur som mappar till det.

En annan metod för att skapa **Shaper** komplexa typer är dock genom Shaper-färdigheten. Genom att inkludera den här färdigheten i en kompetens kan minnesåtgärder under kompetensbearbetning mata ut dataformer med kapslade strukturer, som sedan kan mappas till en komplex typ i indexet. 

Följande exempel färdighetsdefinition ger medlemsnamnen som indata. 


```json
{
  "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
  "context": "/document/content/phrases/*",
  "inputs": [
    {
      "name": "text",
      "source": "/document/content/phrases/*"
    },
    {
      "name": "sentiment",
      "source": "/document/content/phrases/*/sentiment"
    }
  ],
  "outputs": [
    {
      "name": "output",
      "targetName": "analyzedText"
    }
  ]
}
```

### <a name="sample-index"></a>Exempelindex

En kompetens anropas av en indexerare och en indexerare kräver ett index. En komplex fältrepresentation i indexet kan se ut som följande exempel. 

```json

    "name": "my-index",
    "fields": [
        {   "name": "myId", "type": "Edm.String", "key": true, "filterable": true   },
        {   "name": "analyzedText", "type": "Edm.ComplexType",
            "fields": [{
                    "name": "text",
                    "type": "Edm.String",
                    "filterable": false,
                    "sortable": false,
                    "facetable": false,
                    "searchable": true  },
          {
                    "name": "sentiment",
                    "type": "Edm.Double",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
```

### <a name="skill-input"></a>Inmatning av färdighet

Ett inkommande JSON-dokument som ger användbara indata för den här **Shaper-färdigheten** kan vara:

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "text": "this movie is awesome",
                "sentiment": 0.9
            }
        }
    ]
}
```


### <a name="skill-output"></a>Utdata för färdighet

**Shaper-färdigheten** genererar ett nytt element som kallas *analyzedText* med de kombinerade elementen *text* och *sentiment*. Den här utdata överensstämmer med indexschemat. Det importeras och indexeras i ett Azure Cognitive Search-index.

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
            "analyzedText": 
              {
                "text": "this movie is awesome" ,
                "sentiment": 0.9
              }
           }
      }
    ]
}
```

## <a name="scenario-2-input-consolidation"></a>Scenario 2: konsolidering av indata

I ett annat exempel kan du tänka dig att du i olika skeden av bearbetningen av rörledningar har extraherat titeln på en bok och kapitelrubriker på olika sidor i boken. Du kan nu skapa en enda struktur som består av dessa olika ingångar.

**Shaper-färdighetsdefinitionen** för det här scenariot kan se ut som följande exempel:

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "context": "/document",
    "inputs": [
        {
            "name": "title",
            "source": "/document/content/title"
        },
        {
            "name": "chapterTitles",
            "source": "/document/content/pages/*/chapterTitles/*/title"
        }
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "titlesAndChapters"
        }
    ]
}
```

### <a name="skill-output"></a>Utdata för färdighet
I det här fallet **plattar Shaper** till alla kapitelrubriker för att skapa en enda matris. 

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "titlesAndChapters": {
                    "title": "How to be happy",
                    "chapterTitles": [
                        "Start young",
                        "Laugh often",
                        "Eat, sleep and exercise"
                    ]
                }
            }
        }
    ]
}
```

<a name="nested-complex-types"></a>

## <a name="scenario-3-input-consolidation-from-nested-contexts"></a>Scenario 3: indatakonsolidering från kapslade kontexter

Anta att du har en boks titel, kapitel och innehåll och har kört entitetsigenkänning och nyckelfraser på innehållet och nu behöver aggregera resultat från de olika färdigheterna till en enda form med kapitelnamn, entiteter och nyckelfraser.

**Shaper-färdighetsdefinitionen** för det här scenariot kan se ut som följande exempel:

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "context": "/document",
    "inputs": [
        {
            "name": "title",
            "source": "/document/content/title"
        },
        {
            "name": "chapterTitles",
            "sourceContext": "/document/content/pages/*/chapterTitles/*",
            "inputs": [
              {
                  "name": "title",
                  "source": "/document/content/pages/*/chapterTitles/*/title"
              },
              {
                  "name": "number",
                  "source": "/document/content/pages/*/chapterTitles/*/number"
              }
            ]
        }

    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "titlesAndChapters"
        }
    ]
}
```

### <a name="skill-output"></a>Utdata för färdighet
I det här fallet skapar **Shaper** en komplex typ. Den här strukturen finns i minnet. Om du vill spara den i ett [kunskapslager](knowledge-store-concept-intro.md)bör du skapa en projektion i din kompetensuppsättning som definierar lagringsegenskaper.

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "titlesAndChapters": {
                    "title": "How to be happy",
                    "chapterTitles": [
                      { "title": "Start young", "number": 1},
                      { "title": "Laugh often", "number": 2},
                      { "title": "Eat, sleep and exercise", "number: 3}
                    ]
                }
            }
        }
    ]
}
```

## <a name="see-also"></a>Se även

+ [Inbyggda färdigheter](cognitive-search-predefined-skills.md)
+ [Hur man definierar en kompetens](cognitive-search-defining-skillset.md)
+ [Så här använder du komplexa typer](search-howto-complex-data-types.md)
+ [Knowledge Store (förhandsgranskning)](knowledge-store-concept-intro.md)
+ [Skapa ett kunskapslager i REST](knowledge-store-create-rest.md)