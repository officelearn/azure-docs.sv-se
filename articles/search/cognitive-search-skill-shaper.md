---
title: Formaren kognitiva Sök kunskaper (Azure Search) | Microsoft Docs
description: Extrahera metadata och strukturerad information från Ostrukturerade data och form som en komplex typ i en Azure Search berikande pipeline.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 311f4bd67081de567763783a9d86540eda36d9f8
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
#   <a name="shaper-cognitive-skill"></a>Formaren kognitiva kunskaper

Den **formaren** kunskaper skapar en komplex typ för att stödja sammansatta fält (även kallat multipart fält). En komplex typ av fält har flera delar men behandlas som ett enskilt objekt i ett Azure Search-index. Exempel på konsoliderade fält som är användbar i situationer när sökningen är att kombinera ett första och sista namn till ett enda fält, ort och tillstånd till ett enda fält eller namn och födelsedatum i ett enda fält att upprätta en unik identitet.

Formaren kompetensen kan du skapa en struktur, definiera namnet på medlemmarna i denna struktur och tilldelar värden till varje medlem i princip.

Som standard stöder den här tekniken objekt som är en nivå. För mer komplexa objekt kedja du flera formaren steg.

I svaret, är utdatanamnet alltid ”utdata”. Internt pipeline kan mappa ett annat namn, till exempel ”analyzedText” i exemplen nedan till ”utdata”, men formaren kunskaper själva returnerar ”utdata” i svaret. Det kan vara viktigt om du felsöker utökade dokument och Observera namngivning avvikelse, eller om du skapar en anpassad kunskap och strukturerar svaret själv.


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ShaperSkill

## <a name="sample-1-complex-types"></a>Exempel 1: komplexa typer

Överväg ett scenario där du vill skapa en struktur som kallas *analyzedText* som har två medlemmar: *text* och *sentiment*respektive. I Azure Search flerdelade sökbara fält kallas en *komplex typ*, och stöds ännu inte direkt. I den här förhandsgranskningen kan formaren kunskaper användas för att generera fält i en komplex typ i ditt index. 

I följande exempel tillhandahåller medlemmen namn som indata. Utdata-struktur (din komplexa fält i Azure Search) har angetts via *targetName*. 


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
En JSON-dokumentet som tillhandahåller användbar indata för formaren kompetensen kan vara:

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
Formaren kompetensen genererar ett nytt element som kallas *analyzedText* med kombinerade elementen i *text* och *sentiment*. 

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

Anta exempelvis att vid olika faser i pipeline-bearbetning du har extraherat rubrik i en bok och kapitelrubriker på olika sidor i boken. Du kan nu skapa en enkel struktur som består av dessa olika indata.

Formaren kunskaper definitionen för det här scenariot kan se ut som i följande exempel:

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
I det här fallet förenklar formaren titlar kapitlet om du vill skapa en matris. 

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
+ [Hur du definierar en kunskaper](cognitive-search-defining-skillset.md)

