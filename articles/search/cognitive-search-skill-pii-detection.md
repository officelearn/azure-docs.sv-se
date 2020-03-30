---
title: PII-identifiering av kognitiv färdighet (förhandsgranskning)
titleSuffix: Azure Cognitive Search
description: Extrahera och maskera personligt identifierbar information från text i en anrikningspipeline i Azure Cognitive Search. Den här färdigheten är för närvarande i offentlig förhandsversion.
manager: nitinme
author: careyjmac
ms.author: chalton
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: f21200bc6f5b25f3330f5bb87c0843caa5a84e56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298884"
---
#    <a name="pii-detection-cognitive-skill"></a>PII-identifiering kognitiv skicklighet

> [!IMPORTANT] 
> Den här färdigheten är för närvarande i offentlig förhandsversion. Förhandsversionsfunktionen tillhandahålls utan ett servicenivåavtal och rekommenderas inte för produktionsarbetsbelastningar. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Det finns för närvarande inget stöd för portal eller .NET SDK.

**Pii-identifieringsfärdigheten** extraherar personligt identifierbar information från en indatatext och ger dig möjlighet att maskera den från den texten på olika sätt. Den här färdigheten använder de maskininlärningsmodeller som tillhandahålls av [Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) i Cognitive Services.

> [!NOTE]
> När du utökar omfattningen genom att öka bearbetningsfrekvensen, lägga till fler dokument eller lägga till fler AI-algoritmer måste du [bifoga en fakturerbar Cognitive Services-resurs](cognitive-search-attach-cognitive-services.md). Avgifter ackumuleras när du anropar API:er i Cognitive Services och för bildutvinning som en del av dokumentsprickningsfasen i Azure Cognitive Search. Det finns inga avgifter för textextrahering från dokument.
>
> Utförande av inbyggda färdigheter debiteras på den befintliga [Cognitive Services pay-as-you go pris](https://azure.microsoft.com/pricing/details/cognitive-services/). Prissättning för avbildningsextrahering beskrivs på [prissidan](https://go.microsoft.com/fwlink/?linkid=2042400)för Azure Cognitive Search .


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.PIIDetectionSkill

## <a name="data-limits"></a>Databegränsningar
Den maximala storleken på en post bör vara 50 [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)000 tecken mätt med . Om du behöver dela upp dina data innan du skickar dem till färdigheten kan du överväga att använda [textdelningsfärdigheten](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Färdighetsparametrar

Parametrar är skiftlägeskänsliga och alla är valfria.

| Parameternamn     | Beskrivning |
|--------------------|-------------|
| standardSpråkkod |    Ingångstextens språkkod. För tillfället `en` stöds bara. |
| minimumPrecision | Ett värde mellan 0,0 och 1,0. Om konfidenspoängen `piiEntities` (i utdata) är lägre än det inställda `minimumPrecision` värdet returneras eller maskeras inte entiteten. Standardvärdet är 0,0. |
| maskeringMode | En parameter som ger olika sätt att maskera den identifierade PII i indatatexten. Följande alternativ stöds: <ul><li>`none`(standard): Detta innebär att ingen maskering `maskedText` kommer att utföras och utdata kommer inte att returneras. </li><li> `redact`: Det här alternativet tar bort de identifierade entiteterna från indatatexten och ersätter dem inte med någonting. Observera att i det här `piiEntities` fallet kommer förskjutningen i utdata att vara i förhållande till den ursprungliga texten och inte den maskerade texten. </li><li> `replace`: Det här alternativet ersätter de identifierade `maskingCharacter` entiteterna med det tecken som anges i parametern.  Tecknet upprepas till längden på den identifierade entiteten så att förskjutningarna korrekt motsvarar `maskedText`både indatatexten och utdata .</li></ul> |
| maskeringTecken | Tecknet som ska användas för att `maskingMode` maskera texten `replace`om parametern är inställd på . Följande alternativ stöds: `*` `#`(standard), `X`, . Den här parametern `maskingMode` kan bara `replace`vara `null` om den inte är inställd på . |


## <a name="skill-inputs"></a>Indata för färdighet

| Indatanamn      | Beskrivning                   |
|---------------|-------------------------------|
| languageCode    | Valfri. Standardvärdet är `en`.  |
| text          | Texten att analysera.          |

## <a name="skill-outputs"></a>Utdata för färdighet

| Utdatanamn      | Beskrivning                   |
|---------------|-------------------------------|
| pii Enheter | En matris med komplexa typer som innehåller följande fält: <ul><li>text (Den faktiska PII som extraheras)</li> <li>typ</li><li>Subtyp</li><li>poäng (Högre värde innebär att det är mer sannolikt att vara en verklig enhet)</li><li>(i indatatexten)</li><li>length</li></ul> </br> [Möjliga typer och undertyper finns här.](https://docs.microsoft.com/azure/cognitive-services/text-analytics/named-entity-types?tabs=personal) |
| maskeradText | Om `maskingMode` är inställt på `none`ett annat värde än kommer det här utdata att vara strängresultatet för maskeringen som utförs på indatatexten enligt beskrivningen av den valda `maskingMode`.  Om `maskingMode` är `none`inställt på kommer den här utdata inte att finnas. |

##    <a name="sample-definition"></a>Exempeldefinition

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.PIIDetectionSkill",
    "defaultLanguageCode": "en",
    "minimumPrecision": 0.5,
    "maskingMode": "replace",
    "maskingCharacter": "*",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "piiEntities"
      },
      {
        "name": "maskedText"
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
             "text": "Microsoft employee with ssn 859-98-0987 is using our awesome API's."
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
        "piiEntities":[ 
           { 
              "text":"859-98-0987",
              "type":"U.S. Social Security Number (SSN)",
              "subtype":"",
              "offset":28,
              "length":11,
              "score":0.65
           }
        ],
        "maskedText": "Microsoft employee with ssn *********** is using our awesome API's."
      }
    }
  ]
}
```

Observera att förskjutningar som returneras för entiteter i utdata för den här färdigheten returneras direkt från [API:et](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview)för textanalys , vilket innebär att om du använder dem för att indexera till den ursprungliga strängen bör du använda klassen [StringInfo](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo?view=netframework-4.8) i .NET för att extrahera rätt innehåll.  [Mer information hittar du här.](https://docs.microsoft.com/azure/cognitive-services/text-analytics/concepts/text-offsets)

## <a name="error-and-warning-cases"></a>Fel- och varningsfall
Om språkkoden för dokumentet inte stöds returneras en varning och inga entiteter extraheras.
Om texten är tom visas en varning.
Om texten är större än 50 000 tecken analyseras endast de första 50 000 tecknen och en varning utfärdas.

Om färdigheten returnerar en `maskedText` varning kan utdata vara tomma.  Detta innebär att om du förväntar dig att produktionen ska finnas för indata i senare färdigheter, kommer det inte att fungera som avsett. Tänk på detta när du skriver din skillset definition.

## <a name="see-also"></a>Se även

+ [Inbyggda färdigheter](cognitive-search-predefined-skills.md)
+ [Hur man definierar en kompetens](cognitive-search-defining-skillset.md)
