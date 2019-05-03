---
title: Formaren kognitiv sökning färdigheter – Azure Search
description: Extrahera metadata och strukturerad information från Ostrukturerade data och forma dem som en komplex typ i en Azure Search berikande pipeline.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 5267f81c9886e2d1d8d62c134156aedb3b2b8763
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65023692"
---
#   <a name="shaper-cognitive-skill"></a>Formaren kognitiva kunskaper

Den **formaren** färdighet konsoliderar flera indata i en [komplex typ](search-howto-complex-data-types.md) som kan referera till senare i berikande pipelinen. Den **formaren** färdighet kan du skapa en struktur, ange namnet på medlemmarna i denna struktur och tilldela värden till varje medlem i stort sett. Exempel på konsoliderade fält som är användbart i sökscenarier är att kombinera ett första och sista namn till en enda struktur, stad och tillstånd i en enda struktur, eller ett namn och födelsedatum i en enda struktur att upprätta en unik identitet.

API-versionen avgör djup vi att du kan uppnå. 

| API-version | Forma beteenden | 
|-------------|-------------------|
| 2019-05-06-preview REST-API-versionen (.NET SDK stöds inte) | Komplexa objekt flera nivåer djup, i en **formaren** färdighet definition. |
| 2019-05-06 ** (allmänt tillgänglig), 2017-11-11-förhandsversion| Komplexa objekt, en nivå. Formen med flera nivåer kräver länkning av flera formaren steg.|

Förhandsversionen av **formaren** färdigheter som illustreras i [scenario 3](#nested-complex-types), lägger till en ny valfri *sourceContext* som indata. Den *källa* och *sourceContext* egenskaper kan inte anges samtidigt. Om indata är i samband med kunskaper kan bara använda *källa*. Om indata är på en *olika* kontext än färdighet kontext, Använd den *sourceContext*. Den *sourceContext* måste du definiera en kapslad indata med specifika elementet håller på att åtgärdas som källa. 

I svaret, för alla API-versioner, är utdatanamnet alltid ”utdata”. Internt pipelinen kan mappa ett annat namn, till exempel ”analyzedText” som du ser i exemplen nedan, men **formaren** färdighet själva returnerar ”utdata” i svaret. Det kan vara viktigt om du felsöker avancerad och dokument och notera avvikelsen namngivning, eller om du bygger en anpassad kunskap och strukturerar svaret själv.

> [!NOTE]
> Den **formaren** färdighet är inte bunden till en Cognitive Services-API och du debiteras inte för att använda den. Du bör fortfarande [bifoga en resurs för Cognitive Services](cognitive-search-attach-cognitive-services.md), men att åsidosätta den **kostnadsfri** resurs-alternativ som begränsar du till ett litet antal dagliga enrichments per dag.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ShaperSkill

## <a name="scenario-1-complex-types"></a>Scenario 1: komplexa typer

Tänk dig ett scenario där du vill skapa en struktur som kallas *analyzedText* som har två medlemmar: *text* och *sentiment*respektive. I ett Azure Search-index, en flerdelad sökbart fält kallas en *komplex typ* och skapas ofta när källdatan har en motsvarande komplex struktur som mappar till den.

En annan metod för att skapa komplexa typer är dock via den **formaren** färdigheter. Genom att inkludera kompetensen i en kompetens kan i minnet åtgärder under kompetens bearbetning skickas datastrukturer med kapslade strukturer, vilket kan sedan mappas till en komplex typ i ditt index. 

Följande exempel färdighet definition innehåller medlemmen namn som indata. 


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

En kompetens anropas av en indexerare och en indexerare kräver ett index. En representation för komplexa fält i indexet kan se ut som i följande exempel. 

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

### <a name="skill-input"></a>Färdighet indata

Ett inkommande JSON-dokument som tillhandahåller användbar indata för den här **formaren** färdighet kan vara:

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


### <a name="skill-output"></a>Färdighet utdata

Den **formaren** färdighet genererar ett nytt element som kallas *analyzedText* med de kombinerade delarna av *text* och *sentiment*. Dessa utdata överensstämmer med indexschemat. Det ska importeras och indexeras i ett Azure Search-index.

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

## <a name="scenario-2-input-consolidation"></a>Scenario 2: inkommande konsolidering

Tänk dig att i olika faser av pipeline-bearbetning, du har extraherat rubriken för en bok och kapitelrubriker på olika sidor i en bok i ett annat exempel. Du kan nu skapa en enkel struktur som består av dessa olika indata.

Den **formaren** färdighet definitionen för det här scenariot kan se ut som i följande exempel:

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

### <a name="skill-output"></a>Färdighet utdata
I det här fallet den **formaren** plattar ut alla kapitelrubriker för att skapa en enskild matris. 

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

## <a name="scenario-3-input-consolidation-from-nested-contexts"></a>Scenario 3: inkommande konsolidering från kapslade kontexter

> [!NOTE]
> Kapslade strukturer som stöds i api-version = 2019-05-06-Preview kan användas i en [knowledge store](knowledge-store-concept-intro.md) eller i ett Azure Search-index.

Anta att du har den rubrik, kapitel och innehållet i en bok och har kört entitet erkännande och nyckeln fraser på innehållet och nu måste du aggregera resultat från olika färdigheter till en enda form med KAPITELNAMN, entiteter och viktiga fraser.

Den **formaren** färdighet definitionen för det här scenariot kan se ut som i följande exempel:

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

### <a name="skill-output"></a>Färdighet utdata
I det här fallet den **formaren** skapar en komplex typ. Den här strukturen finns i minnet. Om du vill spara den till en knowledge store bör du skapa en projektion i din kompetens som definierar lagringsegenskaper.

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

## <a name="see-also"></a>Se också

+ [Fördefinierade kunskaper](cognitive-search-predefined-skills.md)
+ [Hur du definierar en kompetens](cognitive-search-defining-skillset.md)
+ [Hur du använder komplexa typer](search-howto-complex-data-types.md)
+ [Översikt över Knowledge](knowledge-store-concept-intro.md)
+ [Hur du kommer igång med Knowledge Store](knowledge-store-howto.md)