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
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 7c462971f7ac02e35ab8df5b651da7d0a3f39e1f
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65022265"
---
#    <a name="named-entity-recognition-cognitive-skill"></a>Namngivna Entitetsidentifiering kognitiva kunskaper

Den **med namnet Entitetsidentifiering** färdighet extraherar namngivna enheter från text. Tillgängliga entiteter omfattar typer `person`, `location` och `organization`.

> [!IMPORTANT]
> Igenkänning av namngivna entiteter färdighet har nu upphört ersättas med [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md). Support stoppas den 15 februari 2019 och API: et togs bort från produkten i den 2 maj 2019. Följ rekommendationerna i [inaktuell kognitiva Search funktioner](cognitive-search-skill-deprecated.md) att migrera till en färdighet som stöds.

> [!NOTE]
> När du utökar omfattningen genom att öka frekvensen för bearbetning, att lägga till fler dokument eller att lägga till fler AI-algoritmer, måste du [bifoga en fakturerbar resurs för Cognitive Services](cognitive-search-attach-cognitive-services.md). Avgifter tillkommer när du anropar API: er i Cognitive Services och extrahering av avbildningen som en del av det dokumentknäckning steget i Azure Search. Det finns inga avgifter för textextrahering från dokument.
>
> Körningen av inbyggda färdigheter som ingår debiteras enligt den befintliga [Cognitive Services betala-som-du gå pris](https://azure.microsoft.com/pricing/details/cognitive-services/). Bild extrahering priser beskrivs i den [Azure Search sidan med priser](https://go.microsoft.com/fwlink/?linkid=2042400).


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

| Indatanamn      | Beskrivning                   |
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
