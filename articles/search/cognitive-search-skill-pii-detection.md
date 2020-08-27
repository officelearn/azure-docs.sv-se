---
title: Kognitiva kunskaper om PII-identifiering (förhands granskning)
titleSuffix: Azure Cognitive Search
description: Extrahera och maskera personligt identifierbar information från text i en pipeline i Azure Kognitiv sökning. Den här kunskapen är för närvarande en offentlig för hands version.
manager: nitinme
author: careyjmac
ms.author: chalton
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: b2e35ba083e376f519ccbc32c71c1ac9b1e03a41
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/26/2020
ms.locfileid: "88935304"
---
#    <a name="pii-detection-cognitive-skill"></a>Kognitiva kunskaper om PII-identifiering

> [!IMPORTANT] 
> Den här kunskapen är för närvarande en offentlig för hands version. För hands versions funktionerna tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Det finns för närvarande inget stöd för Portal eller .NET SDK.

**Identifieringen av PII** hämtar personligt identifierbar information från en indata-text och ger dig möjlighet att maskera den från den texten på olika sätt. Den här kunskapen använder Machine Learning-modeller som tillhandahålls av [textanalys](../cognitive-services/text-analytics/overview.md) i Cognitive Services.

> [!NOTE]
> När du utökar omfattningen genom att öka frekvensen för bearbetning, lägga till fler dokument eller lägga till fler AI-algoritmer måste du [koppla en fakturerbar Cognitive Services-resurs](cognitive-search-attach-cognitive-services.md). Avgifterna påförs när API: er anropas i Cognitive Services, och för avbildnings extrahering som en del av stadiet för dokument sprickor i Azure Kognitiv sökning. Det finns inga kostnader för text extrahering från dokument.
>
> Körningen av inbyggda kunskaper debiteras enligt den befintliga [Cognitive Services betala per](https://azure.microsoft.com/pricing/details/cognitive-services/)användning-pris. Priser för avbildnings extrahering beskrivs på [sidan med priser för Azure kognitiv sökning](https://azure.microsoft.com/pricing/details/search/).


## <a name="odatatype"></a>@odata.type  
Microsoft. färdigheter. text. PIIDetectionSkill

## <a name="data-limits"></a>Databegränsningar
Den maximala storleken för en post ska vara 50 000 tecken som mäts av [`String.Length`](/dotnet/api/system.string.length) . Om du behöver dela upp dina data innan du skickar dem till kunskapen bör du överväga att använda [text delnings kunskaper](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Kunskaps parametrar

Parametrar är Skift läges känsliga och alla är valfria.

| Parameternamn     | Beskrivning |
|--------------------|-------------|
| `defaultLanguageCode` |    Språk koden för inmatad text. För närvarande stöds endast `en` . |
| `minimumPrecision` | Ett värde mellan 0,0 och 1,0. Om förtroende poängen (i `piiEntities` utdata) är lägre än set- `minimumPrecision` värdet, returneras eller maskeras inte entiteten. Standardvärdet är 0,0. |
| `maskingMode` | En parameter som ger olika sätt att maskera identifierad personligt identifierbar information i indatamängden. Följande alternativ stöds: <ul><li>`none` (standard): det innebär att ingen maskning kommer att utföras och `maskedText` utdata kommer inte att returneras. </li><li> `redact`: Det här alternativet tar bort identifierade entiteter från inmatad text och ersätter dem inte med något. Observera i så fall att förskjutningen i `piiEntities` utdata är i förhållande till den ursprungliga texten och inte den maskerade texten. </li><li> `replace`: Det här alternativet ersätter de identifierade entiteterna med det som angavs i `maskingCharacter` parametern.  Specialtecknet upprepas till den identifierade entitetens längd så att förskjutningarna motsvarar både inmatnings text och utdata `maskedText` .</li></ul> |
| `maskingCharacter` | Det tecken som ska användas för att maskera texten om `maskingMode` parametern är inställd på `replace` . Följande alternativ stöds: `*` (standard), `#` , `X` . Den här parametern kan bara vara `null` om `maskingMode` inte är inställt på `replace` . |


## <a name="skill-inputs"></a>Kompetens inmatningar

| Inmatat namn      | Beskrivning                   |
|---------------|-------------------------------|
| `languageCode`    | Valfritt. Standardvärdet är `en`.  |
| `text`          | Den text som ska analyseras.          |

## <a name="skill-outputs"></a>Kunskaps utmatningar

| Namn på utdata      | Beskrivning                   |
|---------------|-------------------------------|
| `piiEntities` | En matris med komplexa typer som innehåller följande fält: <ul><li>text (den faktiska PII som extraheras)</li> <li>typ</li><li>Undertyp</li><li>Score (högre värde innebär att det är mer troligt att det är en riktig entitet)</li><li>förskjutning (i inmatad text)</li><li>length</li></ul> </br> [Möjliga typer och under typer hittar du här.](../cognitive-services/text-analytics/named-entity-types.md?tabs=personal) |
| `maskedText` | Om `maskingMode` är inställt på ett annat värde än `none` , blir det här resultatet sträng resultatet av maskeringen som utförts på inmatnings texten, enligt beskrivningen av den valda `maskingMode` .  Om `maskingMode` är inställt på är `none` inte det här resultatet tillgängligt. |

##    <a name="sample-definition"></a>Exempel definition

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
##    <a name="sample-input"></a>Exempelindata

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

##    <a name="sample-output"></a>Exempelutdata

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

Observera att förskjutningarna som returneras för entiteter i resultatet av den här kunskapen returneras direkt från [API för textanalys](../cognitive-services/text-analytics/overview.md), vilket innebär att om du använder dem för att indexera till den ursprungliga strängen, bör du använda [StringInfo](/dotnet/api/system.globalization.stringinfo?view=netframework-4.8) -klassen i .net för att extrahera rätt innehåll.  [Mer information hittar du här.](../cognitive-services/text-analytics/concepts/text-offsets.md)

## <a name="error-and-warning-cases"></a>Fel-och varnings fall
Om språk koden för dokumentet inte stöds returneras en varning och inga entiteter extraheras.
Om texten är tom skapas en varning.
Om texten är större än 50 000 tecken kommer endast de första 50 000 tecknen att analyseras och en varning utfärdas.

Om kunskapen returnerar en varning `maskedText` kan resultatet vara tomt.  Det innebär att om du förväntar dig att utdata ska finnas för indata till senare kunskaper fungerar den inte som avsett. Tänk på detta när du skriver färdigheter-definitionen.

## <a name="see-also"></a>Se även

+ [Inbyggda färdigheter](cognitive-search-predefined-skills.md)
+ [Så här definierar du en färdigheter](cognitive-search-defining-skillset.md)