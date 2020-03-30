---
title: Kognitiv färdighet för enhetsigenkänning
titleSuffix: Azure Cognitive Search
description: Extrahera olika typer av entiteter från text i en anrikningspipeline i Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 6ef5952b6413563b2c2e16ff2218f709b414fb84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297817"
---
#    <a name="entity-recognition-cognitive-skill"></a>Kognitiv färdighet för enhetsigenkänning

Skilliteten **Entitetsigenkänning** extraherar entiteter av olika typer från text. Den här färdigheten använder de maskininlärningsmodeller som tillhandahålls av [Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) i Cognitive Services.

> [!NOTE]
> När du utökar omfattningen genom att öka bearbetningsfrekvensen, lägga till fler dokument eller lägga till fler AI-algoritmer måste du [bifoga en fakturerbar Cognitive Services-resurs](cognitive-search-attach-cognitive-services.md). Avgifter ackumuleras när du anropar API:er i Cognitive Services och för bildutvinning som en del av dokumentsprickningsfasen i Azure Cognitive Search. Det finns inga avgifter för textextrahering från dokument.
>
> Utförande av inbyggda färdigheter debiteras på den befintliga [Cognitive Services pay-as-you go pris](https://azure.microsoft.com/pricing/details/cognitive-services/). Prissättning för avbildningsextrahering beskrivs på [prissidan](https://go.microsoft.com/fwlink/?linkid=2042400)för Azure Cognitive Search .


## <a name="odatatype"></a>@odata.type  
Microsoft.skills.text.entityRecognitionskill

## <a name="data-limits"></a>Databegränsningar
Den maximala storleken på en post bör vara 50 [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)000 tecken mätt med . Om du behöver dela upp dina data innan du skickar dem till nyckelfrasutsugaren kan du använda [textdelningsfärdigheten](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Färdighetsparametrar

Parametrar är skiftlägeskänsliga och är alla valfria.

| Parameternamn     | Beskrivning |
|--------------------|-------------|
| kategorier    | Matris med kategorier som ska extraheras.  Möjliga `"Person"`kategorityper: `"Location"` `"Organization"`, `"Quantity"` `"Datetime"`, `"URL"` `"Email"`, , , , . Om ingen kategori anges returneras alla typer.|
|standardSpråkkod |    Ingångstextens språkkod. Följande språk stöds: `ar, cs, da, de, en, es, fi, fr, hu, it, ja, ko, nl, no, pl, pt-BR, pt-PT, ru, sv, tr, zh-hans`. Alla entitetskategorier stöds inte för alla språk. se anmärkning nedan.|
|minimumPrecision | Ett värde mellan 0 och 1. Om konfidenspoängen `namedEntities` (i utdata) är lägre än det här värdet returneras inte entiteten. Standardvärdet är 0. |
|includeTypelessEntities includeTypelessEntities includeTypelessEntities includeType | Ange `true` om du vill känna igen välkända entiteter som inte passar de aktuella kategorierna. Erkända entiteter returneras i det `entities` komplexa utdatafältet. Till exempel är "Windows 10" en välkänd entitet (en produkt), men eftersom "Produkter" inte är en kategori som stöds, skulle den här entiteten inkluderas i entiteternas utdatafält. Standard är`false` |


## <a name="skill-inputs"></a>Indata för färdighet

| Indatanamn      | Beskrivning                   |
|---------------|-------------------------------|
| languageCode    | Valfri. Standardvärdet är `"en"`.  |
| text          | Texten att analysera.          |

## <a name="skill-outputs"></a>Utdata för färdighet

> [!NOTE]
> Alla entitetskategorier stöds inte för alla språk. `"Person"`Kategorityperna `"Location"`, `"Organization"` och entitetskategori stöds för den fullständiga listan över språk ovan. Endast _de_, _en_, _es_, _fr_och `"Quantity"` `"Datetime"` _zh-hans_ stöd utvinning av , , `"URL"`och `"Email"` typer. Mer information finns i [Språk- och regionstöd för API:et för textanalys](https://docs.microsoft.com/azure/cognitive-services/text-analytics/language-support).  

| Utdatanamn      | Beskrivning                   |
|---------------|-------------------------------|
| Personer       | En matris med strängar där varje sträng representerar namnet på en person. |
| platser  | En matris med strängar där varje sträng representerar en plats. |
| organisationer  | En matris med strängar där varje sträng representerar en organisation. |
| Kvantiteter  | En matris med strängar där varje sträng representerar en kvantitet. |
| dateTimes  | En matris med strängar där varje sträng representerar ett DateTime-värde (som det visas i texten). |
| Webbadresser | En matris med strängar där varje sträng representerar en URL |
| Email | En matris med strängar där varje sträng representerar ett e-postmeddelande |
| namngivna entiteter | En matris med komplexa typer som innehåller följande fält: <ul><li>category</li> <li>(Det faktiska enhetsnamnet)</li><li>(Den plats där den hittades i texten)</li><li>förtroende (Högre värde innebär att det är mer att vara en verklig enhet)</li></ul> |
| Enheter | En matris med komplexa typer som innehåller omfattande information om de entiteter som extraherats från text, med följande fält <ul><li> (det faktiska entitetsnamnet. Detta representerar ett "normaliserat" formulär)</li><li> wikipediaId (på)</li><li>wikipediaSpråk</li><li>wikipediaUrl (en länk till Wikipedia sida för enheten)</li><li>bingId (på)</li><li>typ (kategorin för den enhet som redovisas)</li><li>subType (endast tillgängligt för vissa kategorier ger detta en mer detaljerad vy av entitetstypen)</li><li> matchar (en komplex samling som innehåller)<ul><li>text (den råa texten för entiteten)</li><li>(den plats där den hittades)</li><li>längd (längden på den råa entitetstexten)</li></ul></li></ul> |

##    <a name="sample-definition"></a>Exempeldefinition

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
    "categories": [ "Person", "Email"],
    "defaultLanguageCode": "en",
    "includeTypelessEntities": true,
    "minimumPrecision": 0.5,
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
##    <a name="sample-input"></a>Exempelinmatning

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

##    <a name="sample-output"></a>Exempel på utdata

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
            "confidence": 0.98
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

Observera att förskjutningar som returneras för entiteter i utdata för den här färdigheten returneras direkt från [API:et](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview)för textanalys , vilket innebär att om du använder dem för att indexera till den ursprungliga strängen bör du använda klassen [StringInfo](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo?view=netframework-4.8) i .NET för att extrahera rätt innehåll.  [Mer information hittar du här.](https://docs.microsoft.com/azure/cognitive-services/text-analytics/concepts/text-offsets)

## <a name="error-cases"></a>Felfall
Om dokumentets språkkod inte stöds returneras ett fel och inga entiteter extraheras.

## <a name="see-also"></a>Se även

+ [Inbyggda färdigheter](cognitive-search-predefined-skills.md)
+ [Hur man definierar en kompetens](cognitive-search-defining-skillset.md)
