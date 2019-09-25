---
title: Enhets igenkänning kognitiv Sök kompetens – Azure Search
description: Extrahera olika typer av entiteter från text i en Azure Search kognitiv Sök pipeline.
services: search
manager: nitinme
author: luiscabrer
ms.service: search
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.openlocfilehash: ad2fef96491c2d1a15ad9ff5f57d2911dfecaa36
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2019
ms.locfileid: "71265793"
---
#    <a name="entity-recognition-cognitive-skill"></a>Kognitiv kunskap om entitets igenkänning

Kunskapen om **enhets igenkänning** extraherar entiteter av olika typer från text. Den här kunskapen använder Machine Learning-modeller som tillhandahålls av [textanalys](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) i Cognitive Services.

> [!NOTE]
> När du utökar omfattningen genom att öka frekvensen för bearbetning, lägga till fler dokument eller lägga till fler AI-algoritmer måste du [koppla en fakturerbar Cognitive Services-resurs](cognitive-search-attach-cognitive-services.md). Avgifterna påförs när API: er anropas i Cognitive Services, och för avbildnings extrahering som en del av dokument-cracking-fasen i Azure Search. Det finns inga kostnader för text extrahering från dokument.
>
> Körningen av inbyggda kunskaper debiteras enligt den befintliga [Cognitive Services betala per](https://azure.microsoft.com/pricing/details/cognitive-services/)användning-pris. Priser för avbildnings extrahering beskrivs på [sidan Azure Search priser](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="odatatype"></a>@odata.type  
Microsoft. färdigheter. text. EntityRecognitionSkill

## <a name="data-limits"></a>Databegränsningar
Den maximala storleken för en post ska vara 50 000 tecken som mäts av [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length). Om du behöver dela upp dina data innan du skickar dem till nyckel frasen Extractor, bör du överväga att använda [text delnings kunskapen](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Kunskaps parametrar

Parametrar är Skift läges känsliga och alla är valfria.

| Parameternamn     | Beskrivning |
|--------------------|-------------|
| categories    | Matris med kategorier som ska extraheras.  Möjliga kategori typer: `"Person"` `"Location"` `"Organization"`, `"Quantity"` ,,`"Email"`,, ,.`"URL"` `"Datetime"` Om ingen kategori anges returneras alla typer.|
|defaultLanguageCode |  Språk koden för inmatad text. Följande språk stöds:`de, en, es, fr, it`|
|minimumPrecision | Outnyttja. Reserverad för framtida användning. |
|includeTypelessEntities | När det är inställt på sant om texten innehåller en välkänd entitet, men inte kan kategoriseras i någon av de kategorier som stöds, returneras den som en `"entities"` del av det komplexa utmatnings fältet. 
Detta är entiteter som är välkända men inte klassificerade som en del av de aktuella "kategorierna" som stöds. Till exempel "Windows 10" är en välkänd entitet (en produkt), men "produkter" finns inte i de kategorier som stöds idag. Standard är`false` |


## <a name="skill-inputs"></a>Kompetens inmatningar

| Indatanamn      | Beskrivning                   |
|---------------|-------------------------------|
| languageCode  | Valfritt. Standardvärdet är `"en"`.  |
| text          | Den text som ska analyseras.          |

## <a name="skill-outputs"></a>Kunskaps utmatningar

> [!NOTE]
> Alla enhets kategorier stöds inte för alla språk. Endast _en_, _es_ stöder extrahering av `"Quantity"`, `"Datetime"`, `"URL"`, `"Email"` typer.

| Namn på utdata     | Beskrivning                   |
|---------------|-------------------------------|
| ansvariga      | En sträng mat ris där varje sträng representerar namnet på en person. |
| locations  | En sträng mat ris där varje sträng representerar en plats. |
| organisationer  | En sträng mat ris där varje sträng representerar en organisation. |
| outnyttja  | En sträng mat ris där varje sträng representerar en kvantitet. |
| dateTimes  | En sträng mat ris där varje sträng representerar ett DateTime-värde (som det visas i texten). |
| er | En sträng mat ris där varje sträng representerar en URL |
| e-postmeddelanden | En sträng mat ris där varje sträng representerar ett e-postmeddelande |
| namedEntities | En matris med komplexa typer som innehåller följande fält: <ul><li>category</li> <li>värde (namnet på den faktiska entiteten)</li><li>offset (platsen där den hittades i texten)</li><li>förtroende (används inte för tillfället. Anges till värdet-1)</li></ul> |
| Poster | En matris med komplexa typer som innehåller omfattande information om de entiteter som extraheras från text, med följande fält <ul><li> namn (namnet på den faktiska entiteten. Detta representerar en "normaliserad" form)</li><li> wikipediaId</li><li>wikipediaLanguage</li><li>wikipediaUrl (en länk till Wikipedia-sidan för entiteten)</li><li>bingId</li><li>typ (den enhets kategori som identifieras)</li><li>Undertyp (endast tillgängligt för vissa kategorier, detta ger en mer detaljerad vy av entitetstypen)</li><li> matchningar (en komplex samling som innehåller)<ul><li>text (den råa texten för entiteten)</li><li>förskjutning (platsen där den hittades)</li><li>längd (längden på texten i den obearbetade entiteten)</li></ul></li></ul> |

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

+ [Fördefinierade kunskaper](cognitive-search-predefined-skills.md)
+ [Så här definierar du en färdigheter](cognitive-search-defining-skillset.md)
