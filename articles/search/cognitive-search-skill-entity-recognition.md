---
title: Entitet för kognitiv sökning färdigheter – Azure Search
description: Extrahera olika typer av enheter från texten i en Azure Search kognitiv sökning pipeline.
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
ms.openlocfilehash: 9745934891cd7ba99fa821377318e38134b7d2a5
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53311872"
---
#    <a name="entity-recognition-cognitive-skill"></a>Entiteten erkännande kognitiva kunskaper

Den **Entitetsidentifiering** färdighet extraherar entiteter av olika typer i texten. 

> [!NOTE]
> Från och med den 21 December 2018 kan du associera en resurs för Cognitive Services med ett Azure Search-kompetens. Detta gör att vi börjar debitera för körning av kompetens. På det här datumet måste också börjar vi debitera för extrahering av avbildningen som en del av dokumentknäckning fasen. Textextrahering från dokument fortsätter att erbjudas utan extra kostnad.
>
> Körningen av inbyggda kunskaper debiteras med den befintliga [Cognitive Services betala-som-du gå pris](https://azure.microsoft.com/pricing/details/cognitive-services/) . Bild extrahering priser kommer att debiteras enligt priserna för förhandsversionen och beskrivs i den [Azure Search sidan med priser](https://go.microsoft.com/fwlink/?linkid=2042400). Lär dig [mer](cognitive-search-attach-cognitive-services.md).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.EntityRecognitionSkill

## <a name="data-limits"></a>Databegränsningar
Den maximala storleken för en post ska vara 50 000 tecken enligt `String.Length`. Om du vill dela upp dina data innan de skickas till diskussionsämne extraktor kan du använda den [Text dela färdighet](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Färdighet parametrar

Parametrar är skiftlägeskänsliga och är valfria.

| Parameternamn     | Beskrivning |
|--------------------|-------------|
| kategorier    | Matris med kategorier som ska extraheras.  Möjliga kategorityper: `"Person"`, `"Location"`, `"Organization"`, `"Quantity"`, `"Datetime"`, `"URL"`, `"Email"`. Om ingen kategori anges, returneras alla typer.|
|defaultLanguageCode |  Språkkod för den inmatade texten. Följande språk stöds: `de, en, es, fr, it`|
|minimumPrecision | Oanvända. Reserverat för framtida användning. |
|includeTypelessEntites | Värdet true om texten innehåller en välkänd entitet, men kan inte grupperas i en av kategorierna som stöds, kommer det att returneras som en del av den `"entities"` komplexa utdatafältet. Standardvärdet är `false` |


## <a name="skill-inputs"></a>Färdighet indata

| Indatanamnet      | Beskrivning                   |
|---------------|-------------------------------|
| languageCode  | Valfri. Standardvärdet är `"en"`.  |
| text          | Texten att analysera.          |

## <a name="skill-outputs"></a>Färdighet utdata

**OBS**: Inte alla entiteten kategorier har stöd för alla språk.
Endast _en_, _es_ stöd för extrahering av `"Quantity"`, `"Datetime"`, `"URL"`, `"Email"` typer.

| Namn på utdata     | Beskrivning                   |
|---------------|-------------------------------|
| personer      | En matris med strängar där varje sträng representerar namnet på en person. |
| platser  | En matris med strängar där varje sträng representerar en plats. |
| organisationer  | En matris med strängar där varje sträng representerar en organisation. |
| kvantiteter  | En matris med strängar där varje sträng representerar en kvantitet. |
| datum och tid  | En matris med strängar där varje sträng representerar ett datetime-värde (som det visas i texten) värde. |
| URL: er | En matris med strängar där varje sträng representerar en URL |
| e-postmeddelanden | En matris med strängar där varje sträng representerar ett e-postmeddelande |
| namedEntities | En matris med komplexa typer som innehåller följande fält: <ul><li>category</li> <li>värde (faktiska entitetsnamnet)</li><li>förskjutning (plats där det hittades i texten)</li><li>förtroende (används inte för tillfället. Anges till värdet -1)</li></ul> |
| entiteter | En matris med komplexa typer som innehåller omfattande information om de entiteter som extraheras från text, med följande fält <ul><li> (det faktiska entitet namn. Detta motsvarar ett ”normaliserade” formulär)</li><li> wikipediaId</li><li>wikipediaLanguage</li><li>wikipediaUrl (en länk till Wikipedia-sida för entiteten)</li><li>bingId</li><li>typ (kategorin för den entitet som känns igen)</li><li>Undertyp (tillgänglig endast för särskilda kategorier, detta ger en mer detaljerad vy av entitetstypen)</li><li> matchar (en komplex samling som innehåller)<ul><li>text (rå text för entiteten)</li><li>förskjutning (plats där det hittades)</li><li>längden (längden på texten raw entitet)</li></ul></li></ul> |

##  <a name="sample-definition"></a>Exempeldefinition

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
    "categories": [ "Person", "Email"],
    "defaultLanguageCode": "en",
    "includeTypelessEntities": true,
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
      },
      {
        "name": "emails",
        "targetName": "contact"
      },
      {
        "name": "entities"
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
             "text": "Contoso corporation was founded by John Smith. They can be reached at contact@contoso.com",
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
        "persons": [ "John Smith"],
        "emails":["contact@contoso.com"],
        "namedEntities": 
        [
          {
            "category":"Person",
            "value": "John Smith",
            "offset": 35,
            "confidence": -1
          }
        ],
        "entities":  
        [
          {
            "name":"John Smith",
            "wikipediaId": null,
            "wikipediaLanguage": null,
            "wikipediaUrl": null,
            "bingId": null,
            "type": "Person",
            "subType": null,
            "matches": [{
                "text": "John Smith",
                "offset": 35,
                "length": 10
            }]
          },
          {
            "name": "contact@contoso.com",
            "wikipediaId": null,
            "wikipediaLanguage": null,
            "wikipediaUrl": null,
            "bingId": null,
            "type": "Email",
            "subType": null,
            "matches": [
            {
                "text": "contact@contoso.com",
                "offset": 70,
                "length": 19
            }]
          },
          {
            "name": "Contoso",
            "wikipediaId": "Contoso",
            "wikipediaLanguage": "en",
            "wikipediaUrl": "https://en.wikipedia.org/wiki/Contoso",
            "bingId": "349f014e-7a37-e619-0374-787ebb288113",
            "type": null,
            "subType": null,
            "matches": [
            {
                "text": "Contoso",
                "offset": 0,
                "length": 7
            }]
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