---
title: Namngiven kognitiv färdighet för entitetsigenkänning
titleSuffix: Azure Cognitive Search
description: Extrahera namngivna entiteter för person, plats och organisation från text i en AI-anrikningspipeline i Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 127155e492b556ce1ce02b67cf0b0846b99ebcd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72791934"
---
#    <a name="named-entity-recognition-cognitive-skill"></a>Namngiven kognitiv färdighet för entitetsigenkänning

Den **namngivna entitetsigenkänning** färdighet extraherar namngivna entiteter från text. Tillgängliga entiteter `person` `location` inkluderar `organization`typerna och .

> [!IMPORTANT]
> Namngiven entitetsigenkänningsfärdighet har nu upphört ersatt av [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md). Supporten upphörde den 15 februari 2019 och API:et togs bort från produkten den 2 maj 2019. Följ rekommendationerna i [Inaktuella kognitiva sökfärdigheter](cognitive-search-skill-deprecated.md) för att migrera till en färdighet som stöds.

> [!NOTE]
> När du utökar omfattningen genom att öka bearbetningsfrekvensen, lägga till fler dokument eller lägga till fler AI-algoritmer måste du [bifoga en fakturerbar Cognitive Services-resurs](cognitive-search-attach-cognitive-services.md). Avgifter ackumuleras när du anropar API:er i Cognitive Services och för bildutvinning som en del av dokumentsprickningsfasen i Azure Cognitive Search. Det finns inga avgifter för textextrahering från dokument.
>
> Utförande av inbyggda färdigheter debiteras på den befintliga [Cognitive Services pay-as-you go pris](https://azure.microsoft.com/pricing/details/cognitive-services/). Prissättning för avbildningsextrahering beskrivs på [prissidan](https://go.microsoft.com/fwlink/?linkid=2042400)för Azure Cognitive Search .


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.NamedEntityRecognitionSkill

## <a name="data-limits"></a>Databegränsningar
Den maximala storleken på en post bör vara 50 [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)000 tecken mätt med . Om du behöver dela upp dina data innan du skickar dem till nyckelfrasutsugaren kan du använda [textdelningsfärdigheten](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Färdighetsparametrar

Parametrar är skiftlägeskänsliga.

| Parameternamn     | Beskrivning |
|--------------------|-------------|
| kategorier    | Matris med kategorier som ska extraheras.  Möjliga `"Person"`kategorityper: `"Location"` `"Organization"`, , . Om ingen kategori anges returneras alla typer.|
|standardSpråkkod |  Ingångstextens språkkod. Följande språk stöds:`de, en, es, fr, it`|
| minimumPrecision  | Ett tal mellan 0 och 1. Om precisionen är lägre än det här värdet returneras inte entiteten. Standardvärdet är 0.|

## <a name="skill-inputs"></a>Indata för färdighet

| Indatanamn      | Beskrivning                   |
|---------------|-------------------------------|
| languageCode  | Valfri. Standardvärdet är `"en"`.  |
| text          | Texten att analysera.          |

## <a name="skill-outputs"></a>Utdata för färdighet

| Utdatanamn     | Beskrivning                   |
|---------------|-------------------------------|
| Personer      | En matris med strängar där varje sträng representerar namnet på en person. |
| platser  | En matris med strängar där varje sträng representerar en plats. |
| organisationer  | En matris med strängar där varje sträng representerar en organisation. |
| Enheter | En rad komplexa typer. Varje komplex typ innehåller följande fält: <ul><li>kategori`"person"`( `"organization"`, `"location"`, eller )</li> <li>värde (det faktiska enhetsnamnet)</li><li>(Den plats där den hittades i texten)</li><li>konfidens (A-värde mellan 0 och 1 som representerar det förtroende för att värdet är en faktisk enhet)</li></ul> |

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
##  <a name="sample-input"></a>Exempelinmatning

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "This is the loan application for Joe Romero, a Microsoft employee who was born in Chile and who then moved to Australia… Ana Smith is provided as a reference.",
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


## <a name="error-cases"></a>Felfall
Om dokumentets språkkod inte stöds returneras ett fel och inga entiteter extraheras.

## <a name="see-also"></a>Se även

+ [Inbyggda färdigheter](cognitive-search-predefined-skills.md)
+ [Hur man definierar en kompetens](cognitive-search-defining-skillset.md)
+ [Färdighet för entitetsigenkän](cognitive-search-skill-entity-recognition.md)
