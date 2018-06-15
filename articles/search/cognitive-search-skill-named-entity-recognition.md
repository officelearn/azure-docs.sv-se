---
title: Med namnet entitet Recognition kognitiva Sök kunskaper (Azure Search) | Microsoft Docs
description: Extrahera namngivna entiteter för person, plats och organisation från texten i en pipeline för Azure Search kognitiva sökning.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 73ffcf5e2ced63fddaf0f5ef2ca7e72a7d94b966
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
ms.locfileid: "33791044"
---
#    <a name="named-entity-recognition-cognitive-skill"></a>Namngiven entitet Recognition kognitiva kunskaper

Den **med namnet entitet Recognition** kunskaper extraherar namngivna enheter från text. Tillgängliga entiteter omfattar typer `person`, `location`, och `organization`.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.NamedEntityRecognitionSkill

## <a name="skill-parameters"></a>Kunskaper parametrar

Parametrar är skiftlägeskänsliga.

| Parameternamn     | Beskrivning |
|--------------------|-------------|
| kategorier    | Matris med kategorier som ska extraheras.  Möjliga kategorityper: `"Person"`, `"Location"`, `"Organization"`. Om ingen kategori anges returneras alla typer.|
|defaultLanguageCode |  Språkkod av indatatexten. Stöd för följande språk: `ar, cs, da, de, en, es, fi, fr, he, hu, it, ko, pt-br, pt`|
| minimumPrecision  | En siffra mellan 0 och 1. Om precisionen är lägre än detta värde returneras inte entiteten. Standardvärdet är 0.|

## <a name="skill-inputs"></a>Kunskaper indata

| Indatanamnet      | Beskrivning                   |
|---------------|-------------------------------|
| languageCode  | Valfri. Standardvärdet är `"en"`.  |
| Text          | Texten som används för att analysera.          |

## <a name="skill-outputs"></a>Kunskaper utdata

| Namnet på målet     | Beskrivning                   |
|---------------|-------------------------------|
| personer      | En matris med strängar som där varje sträng representerar namnet på en person. |
| Platser  | En matris med strängar som där varje sträng representerar en plats. |
| Organisationer  | En matris med strängar som där varje sträng representerar en organisation. |
| entiteter | En matris med komplexa typer. Varje typ av komplexa innehåller följande fält: <ul><li>kategori (`"person"`, `"organization"`, eller `"location"`)</li> <li>värdet (faktiska entitetsnamnet)</li><li>förskjutning (plats där det hittades i texten)</li><li>förtroende (ett värde mellan 0 och 1 som motsvarar den förtroende att värdet är en faktisk enhet)</li></ul> |

##  <a name="sample-definition"></a>Exempel definition

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
    "categories": [ "Person"],
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
             "text": "This is a the loan application for Joe Romero, he is a Microsoft employee who was born in Chile and then moved to Australia… Ana Smith is provided as a reference.",
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
        "locations": ["Seattle"],
        "organizations":["Microsoft Corporation"],
        "entities":  
        [
          {
            "category":"person",
            "value": "Joe Romero",
            "offset": 45,
            "confidence": 0.87
          },
          {
            "category":"person",
            "value": "Ana Smith",
            "offset": 59,
            "confidence": 0.87
          },
          {
            "category":"location",
            "value": "Seattle",
            "offset": 5,
            "confidence": 0.99
          },
          {
            "category":"organization",
            "value": "Microsoft Corporation",
            "offset": 120,
            "confidence": 0.99
          }
        ]
      }
    }
  ]
}
```


## <a name="error-cases"></a>Fel
Om du anger en kod med språket stöds inte, eller om innehållet inte matchar det språk som anges, är returnerade ett fel och inga entiteter extraheras.

## <a name="see-also"></a>Se också

+ [Fördefinierade kunskaper](cognitive-search-predefined-skills.md)
+ [Hur du definierar en kunskaper](cognitive-search-defining-skillset.md)