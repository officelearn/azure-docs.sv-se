---
title: Kognitiv kunskap om entitets igenkänning
titleSuffix: Azure Cognitive Search
description: Extrahera olika typer av entiteter från text i en pipeline för anrikning i Azure Kognitiv sökning.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 08e9656e3b899cbb6d4de733696175e8f31b0e66
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792012"
---
#   <a name="entity-recognition-cognitive-skill"></a>Kognitiv kunskap om entitets igenkänning

Kunskapen om **enhets igenkänning** extraherar entiteter av olika typer från text. Den här kunskapen använder Machine Learning-modeller som tillhandahålls av [textanalys](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) i Cognitive Services.

> [!NOTE]
> När du utökar omfattningen genom att öka frekvensen för bearbetning, lägga till fler dokument eller lägga till fler AI-algoritmer måste du [koppla en fakturerbar Cognitive Services-resurs](cognitive-search-attach-cognitive-services.md). Avgifterna påförs när API: er anropas i Cognitive Services, och för avbildnings extrahering som en del av stadiet för dokument sprickor i Azure Kognitiv sökning. Det finns inga kostnader för text extrahering från dokument.
>
> Körningen av inbyggda kunskaper debiteras enligt den befintliga [Cognitive Services betala per](https://azure.microsoft.com/pricing/details/cognitive-services/)användning-pris. Priser för avbildnings extrahering beskrivs på [sidan med priser för Azure kognitiv sökning](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="odatatype"></a>@odata.type  
Microsoft. färdigheter. text. EntityRecognitionSkill

## <a name="data-limits"></a>Databegränsningar
Den maximala storleken för en post ska vara 50 000 tecken som mäts av [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length). Om du behöver dela upp dina data innan du skickar dem till nyckel frasen Extractor, bör du överväga att använda [text delnings kunskapen](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Kunskaps parametrar

Parametrar är Skift läges känsliga och alla är valfria.

| Parameternamn     | Beskrivning |
|--------------------|-------------|
| kategorier    | Matris med kategorier som ska extraheras.  Möjliga kategori typer: `"Person"`, `"Location"`, `"Organization"`, `"Quantity"`, `"Datetime"`, `"URL"`, `"Email"`. Om ingen kategori anges returneras alla typer.|
|defaultLanguageCode |  Språk koden för inmatad text. Följande språk stöds: `de, en, es, fr, it`|
|minimumPrecision | Outnyttja. Reserverad för framtida användning. |
|includeTypelessEntities | När det är inställt på sant om texten innehåller en välkänd entitet, men inte kan kategoriseras i någon av de kategorier som stöds, returneras den som en del av fältet `"entities"` komplex utmatning. 
Detta är entiteter som är välkända men inte klassificerade som en del av de aktuella "kategorierna" som stöds. Till exempel "Windows 10" är en välkänd entitet (en produkt), men "produkter" finns inte i de kategorier som stöds idag. Standardvärdet är `false` |


## <a name="skill-inputs"></a>Kompetens inmatningar

| Inmatat namn      | Beskrivning                   |
|---------------|-------------------------------|
| languageCode  | Valfri. Standardvärdet är `"en"`.  |
| text          | Den text som ska analyseras.          |

## <a name="skill-outputs"></a>Kunskaps utmatningar

> [!NOTE]
> Alla enhets kategorier stöds inte för alla språk. Endast _en_, _es_ stöder extrahering av `"Quantity"`, `"Datetime"`, `"URL"``"Email"` typer.

| Namn på utdata     | Beskrivning                   |
|---------------|-------------------------------|
| ansvariga      | En sträng mat ris där varje sträng representerar namnet på en person. |
| platser  | En sträng mat ris där varje sträng representerar en plats. |
| organisationer  | En sträng mat ris där varje sträng representerar en organisation. |
| outnyttja  | En sträng mat ris där varje sträng representerar en kvantitet. |
| Slutdatum  | En sträng mat ris där varje sträng representerar ett DateTime-värde (som det visas i texten). |
| er | En sträng mat ris där varje sträng representerar en URL |
| e-postmeddelanden | En sträng mat ris där varje sträng representerar ett e-postmeddelande |
| namedEntities | En matris med komplexa typer som innehåller följande fält: <ul><li>category</li> <li>värde (namnet på den faktiska entiteten)</li><li>offset (platsen där den hittades i texten)</li><li>förtroende (används inte för tillfället. Anges till värdet-1)</li></ul> |
| poster | En matris med komplexa typer som innehåller omfattande information om de entiteter som extraheras från text, med följande fält <ul><li> namn (namnet på den faktiska entiteten. Detta representerar en "normaliserad" form)</li><li> wikipediaId</li><li>wikipediaLanguage</li><li>wikipediaUrl (en länk till Wikipedia-sidan för entiteten)</li><li>bingId</li><li>typ (den enhets kategori som identifieras)</li><li>Undertyp (endast tillgängligt för vissa kategorier, detta ger en mer detaljerad vy av entitetstypen)</li><li> matchningar (en komplex samling som innehåller)<ul><li>text (den råa texten för entiteten)</li><li>förskjutning (platsen där den hittades)</li><li>längd (längden på texten i den obearbetade entiteten)</li></ul></li></ul> |

##  <a name="sample-definition"></a>Exempel definition

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
##  <a name="sample-input"></a>Exempel på inmatade

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


## <a name="error-cases"></a>Fel fall
Om språk koden för dokumentet inte stöds returneras ett fel och inga entiteter extraheras.

## <a name="see-also"></a>Se också

+ [Inbyggda kunskaper](cognitive-search-predefined-skills.md)
+ [Så här definierar du en färdigheter](cognitive-search-defining-skillset.md)
