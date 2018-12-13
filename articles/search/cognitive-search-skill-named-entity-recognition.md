---
title: Med namnet Entitetsidentifiering kognitiv sökning färdigheter – Azure Search
description: Extrahera namngivna enheter för person, plats och organisation från texten i en Azure Search kognitiv sökning pipeline.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: e63354152f8821c5ce975563639c8b87fb332bd4
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53313997"
---
#    <a name="named-entity-recognition-cognitive-skill"></a>Namngivna Entitetsidentifiering kognitiva kunskaper

Den **med namnet Entitetsidentifiering** färdighet extraherar namngivna enheter från text. Tillgängliga entiteter omfattar typer `person`, `location` och `organization`.

> [!NOTE]
> <ul>
> <li>
> Från och med den 21 December 2018 kan du associera en resurs för Cognitive Services med ett Azure Search-kompetens. Detta gör att vi börjar debitera för körning av kompetens. På det här datumet måste också börjar vi debitera för extrahering av avbildningen som en del av dokumentknäckning fasen. Textextrahering från dokument fortsätter att erbjudas utan extra kostnad.>
> Körningen av inbyggda kunskaper debiteras med den befintliga [Cognitive Services betala-som-du gå pris](https://azure.microsoft.com/pricing/details/cognitive-services/) . Bild extrahering priser kommer att debiteras enligt priserna för förhandsversionen och beskrivs i den [Azure Search sidan med priser](https://go.microsoft.com/fwlink/?linkid=2042400). Lär dig [mer](cognitive-search-attach-cognitive-services.md).</li>
> <li> Namngiven entitet erkännande färdighet anses ”föråldrade” och officiellt stöds inte startar Feburary 15 2019. Följ rekommendationerna som anges i <a href="cognitive-search-skill-deprecated.md">inaktuell kognitiva Search funktioner</a> sidan för att migrera till en stöds färdighet</li>

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.NamedEntityRecognitionSkill

## <a name="data-limits"></a>Databegränsningar
Den maximala storleken för en post ska vara 50 000 tecken enligt `String.Length`. Om du vill dela upp dina data innan de skickas till diskussionsämne extraktor kan du använda den [Text dela färdighet](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Färdighet parametrar

Parametrar är skiftlägeskänsliga.

| Parameternamn     | Beskrivning |
|--------------------|-------------|
| kategorier    | Matris med kategorier som ska extraheras.  Möjliga kategorityper: `"Person"`, `"Location"`, `"Organization"`. Om ingen kategori anges, returneras alla typer.|
|defaultLanguageCode |  Språkkod för den inmatade texten. Följande språk stöds: `de, en, es, fr, it`|
| minimumPrecision  | Ett tal mellan 0 och 1. Om precisionen är lägre än det här värdet, returneras inte entiteten. Standardvärdet är 0.|

## <a name="skill-inputs"></a>Färdighet indata

| Indatanamnet      | Beskrivning                   |
|---------------|-------------------------------|
| languageCode  | Valfri. Standardvärdet är `"en"`.  |
| text          | Texten att analysera.          |

## <a name="skill-outputs"></a>Färdighet utdata

| Namn på utdata     | Beskrivning                   |
|---------------|-------------------------------|
| personer      | En matris med strängar där varje sträng representerar namnet på en person. |
| platser  | En matris med strängar där varje sträng representerar en plats. |
| organisationer  | En matris med strängar där varje sträng representerar en organisation. |
| entiteter | En matris med komplexa typer. Varje typ av komplexa innehåller följande fält: <ul><li>kategori (`"person"`, `"organization"`, eller `"location"`)</li> <li>värde (faktiska entitetsnamnet)</li><li>förskjutning (plats där det hittades i texten)</li><li>förtroende (ett värde mellan 0 och 1 som representerar den säker på att värdet är en faktisk enhet)</li></ul> |

##  <a name="sample-definition"></a>Exempeldefinition

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
    "categories": [ "Person", "Location", "Organization"],
    "defaultLanguageCode": "en",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "persons",
        "targetName": "people"
      }
    ]
  }
```
##  <a name="sample-input"></a>Exempelindata

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "This is the loan application for Joe Romero, he is a Microsoft employee who was born in Chile and then moved to Australia… Ana Smith is provided as a reference.",
             "languageCode": "en"
           }
      }
    ]
}
```

##  <a name="sample-output"></a>Exempel på utdata

```json
{
  "values": [
    {
      "recordId": "1",
      "data" : 
      {
        "persons": [ "Joe Romero", "Ana Smith"],
        "locations": ["Chile", "Australia"],
        "organizations":["Microsoft"],
        "entities":  
        [
          {
            "category":"person",
            "value": "Joe Romero",
            "offset": 33,
            "confidence": 0.87
          },
          {
            "category":"person",
            "value": "Ana Smith",
            "offset": 124,
            "confidence": 0.87
          },
          {
            "category":"location",
            "value": "Chile",
            "offset": 88,
            "confidence": 0.99
          },
          {
            "category":"location",
            "value": "Australia",
            "offset": 112,
            "confidence": 0.99
          },
          {
            "category":"organization",
            "value": "Microsoft",
            "offset": 54,
            "confidence": 0.99
          }
        ]
      }
    }
  ]
}
```


## <a name="error-cases"></a>Felhändelser
Om språkkod för dokumentet inte stöds, returneras ett fel och inga entiteter extraheras.

## <a name="see-also"></a>Se också

+ [Fördefinierade kunskaper](cognitive-search-predefined-skills.md)
+ [Hur du definierar en kompetens](cognitive-search-defining-skillset.md)
+ [Entiteten erkännande färdighet](cognitive-search-skill-entity-recognition.md)
