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
ms.date: 01/17/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: a1f5a698ee76ebd0561bd19ff1a23d0f04be0771
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2019
ms.locfileid: "54410123"
---
#   <a name="shaper-cognitive-skill"></a>Formaren kognitiva kunskaper

Den **formaren** färdighet skapar en komplex typ för att stödja sammansatta fält (även kallat multipart fält). En komplex typ-fältet har flera delar men behandlas som ett enskilt objekt i ett Azure Search-index. Exempel på konsoliderade fält som är användbart i sökscenarier är att kombinera ett första och sista namn till ett fält, stad och tillstånd till ett enda fält eller namn och födelsedatum till ett fält att upprätta en unik identitet.

Den **formaren** färdighet kan du skapa en struktur, ange namnet på medlemmarna i denna struktur och tilldela värden till varje medlem i stort sett.

Som standard stöder den här tekniken objekt som är en nivå. För mer komplexa objekt, kan du länka flera **formaren** steg.

I svaret, är utdatanamnet alltid ”utdata”. Internt pipelinen kan mappa ett annat namn, till exempel ”analyzedText” i exemplen nedan till ”utdata”, men **formaren** färdighet själva returnerar ”utdata” i svaret. Det kan vara viktigt om du felsöker avancerad och dokument och notera avvikelsen namngivning, eller om du bygger en anpassad kunskap och strukturerar svaret själv.

> [!NOTE]
> Kompetensen är inte bunden till en Cognitive Services-API och du debiteras inte för att använda den. Du bör fortfarande [bifoga en resurs för Cognitive Services](cognitive-search-attach-cognitive-services.md), men att åsidosätta den **kostnadsfri** resurs-alternativ som begränsar du till ett litet antal dagliga enrichments per dag.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ShaperSkill

## <a name="sample-1-complex-types"></a>Exempel 1: komplexa typer

Tänk dig ett scenario där du vill skapa en struktur som kallas *analyzedText* som har två medlemmar: *text* och *sentiment*respektive. I Azure Search, en flerdelad sökbart fält kallas en *komplex typ*, och stöds ännu inte direkt. I den här förhandsversionen kan en **formaren** färdighet kan användas för att generera fält i en komplex typ i ditt index. 

I följande exempel tillhandahåller medlemmen namn som indata. Utdata-struktur (dina komplexa fält i Azure Search) har angetts via *targetName*. 


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
      "targetName": analyzedText"
    }
  ]
}
```

### <a name="sample-input"></a>Exempelindata
Ett JSON-dokument som tillhandahåller användbar indata för den här **formaren** färdighet kan vara:

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


### <a name="sample-output"></a>Exempel på utdata
Den **formaren** färdighet genererar ett nytt element som kallas *analyzedText* med de kombinerade delarna av *text* och *sentiment*. 

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

## <a name="sample-2-input-consolidation"></a>Exempel 2: inkommande konsolidering

Tänk dig att i olika faser av pipeline-bearbetning, du har extraherat rubriken för en bok och kapitelrubriker på olika sidor i en bok i ett annat exempel. Du kan nu skapa en enkel struktur som består av dessa olika indata.

Formaren färdighet definitionen för det här scenariot kan se ut som i följande exempel:

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
            "source": "/document/content/pages/*/chapterTitles/*"
        }
    ],
    "outputs": [
        {
            "output": "titlesAndChapters",
            "targetName": "analyzedText"
        }
    ]
}
```

### <a name="sample-output"></a>Exempel på utdata
I det här fallet formaren plattar ut alla kapitelrubriker för att skapa en enskild matris. 

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

## <a name="see-also"></a>Se också

+ [Fördefinierade kunskaper](cognitive-search-predefined-skills.md)
+ [Hur du definierar en kompetens](cognitive-search-defining-skillset.md)

