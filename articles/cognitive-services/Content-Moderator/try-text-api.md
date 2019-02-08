---
title: Moderera text med hjälp av Text Moderering API - Content Moderator
titlesuffix: Azure Cognitive Services
description: Testa textmoderering med hjälp av API för Moderering av Text i online-konsolen.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 08/05/2017
ms.author: sajagtap
ms.openlocfilehash: 794638496931f72a12fcb3bd8819b04c7e2e7c97
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55877056"
---
# <a name="moderate-text-from-the-api-console"></a>Moderera text från API-konsol

Använd den [Text Moderering API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f) i Azure Content Moderator söker igenom din textinnehåll. Åtgärden söker igenom innehållet med avseende på svordomar och jämför innehåll mot anpassade och delade svartlistor.


## <a name="get-your-api-key"></a>Få din API-nyckel
Innan du kan testa API: et i online-konsolen, måste din prenumerationsnyckel. Den finns på den **inställningar** fliken den **Ocp-Apim-Subscription-Key** box. Mer information finns i [Översikt](overview.md).

## <a name="navigate-to-the-api-reference"></a>Gå till API-referens
Gå till den [Text Moderering API-referens](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f). 

  Den **Text - skärmen** öppnas.

## <a name="open-the-api-console"></a>Öppna konsolen API
För **Open API testkonsolen**, väljer du den region som bäst beskriver din plats. 

  ![Text: sidan val av skärmen](images/test-drive-region.png)

  Den **Text - skärmen** API-konsolen öppnas.

## <a name="select-the-inputs"></a>Välj indata

### <a name="parameters"></a>Parametrar
Välj frågeparametrar som du vill använda i textskärmen. I det här exemplet använder du standardvärdet för **språk**. Du kan även lämna det tomt eftersom åtgärden identifierar automatiskt det sannolika språket som en del av den kan körningen.

> [!NOTE]
> För den **språk** parameter, tilldela `eng` eller lämna det tomt om du vill se den maskinassisterad **klassificering** svar (förhandsgranskningsfunktion). **Den här funktionen stöder endast engelska**.
>
> För **svordomar villkoren** identifiering, Använd den [ISO 639-3-kod](http://www-01.sil.org/iso639-3/codes.asp) språk som stöds visas i den här artikeln eller lämna det tomt.

För **Autokorrigering**, **PII**, och **klassificera (förhandsversion)** väljer **SANT**. Lämna den **ListId** fält tomma.

  ![Text: skärmen konsolen frågeparametrar](images/text-api-console-inputs.PNG)

### <a name="content-type"></a>Innehållstyp
För **Content-Type**, Välj typ av innehåll som du vill skärmen. Det här exemplet använder standard **text/plain** innehållstyp. I den **Ocp-Apim-Subscription-Key** anger din prenumerationsnyckel.

### <a name="sample-text-to-scan"></a>Exempeltext söker igenom
I den **Begärandetext** anger lite text. I följande exempel visas en avsiktlig skrivfel i texten.

> [!NOTE]
> Ogiltig e-postadress i följande exempel är avsiktligt. Syftet är att förmedla Exempelindata och utdata format.

```
    Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.
    These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300.
    Also, 999-99-9999 looks like a social security number (SSN).
```

### <a name="text-classification-feature"></a>Funktionen för klassificering av text

I följande exempel ser du Content Moderator datorstödd text klassificering svar. Det hjälper dig att identifiera potentiellt oönskade innehåll. Flaggade innehållet kan betraktas som olämplig beroende på sammanhang. Förutom att förmedla sannolikheten för varje kategori, kan det rekommenderar en mänsklig granskning av innehållet. Funktionen använder en tränad modell för att identifiera möjliga missbruk, uttrycker sig nedsättande eller diskriminerande språk. Detta inkluderar slang, förkortat ord och stötande och avsiktligt felstavade ord för granskning. 

#### <a name="explanation"></a>Förklaring

- `Category1` representerar potentiella förekomsten av språk som kan uppfattas som sexuellt explicit eller är olämpligt för barn i vissa situationer.
- `Category2` representerar potentiella förekomsten av språk som kan uppfattas som sexuellt något eller mogen i vissa situationer.
- `Category3` representerar potentiella förekomsten av språk som kan uppfattas som stötande i vissa situationer.
- `Score` är mellan 0 och 1. Ju högre poäng desto högre modellen är att förutsäga att kategorin kan användas. Den här förhandsversionen är beroende av en statistisk modell i stället för manuellt kodade resultat. Vi rekommenderar att du testar med ditt eget innehåll att avgöra hur varje kategori stämmer överens med dina behov.
- `ReviewRecommended` är true eller false beroende på den interna poängen tröskelvärden. Kunder bör utvärdera om du vill använda det här värdet eller välja anpassade tröskelvärden baserat på deras innehåll principer.

### <a name="analyze-the-response"></a>Analysera svaret
Följande svar visar olika insikter från API: et. Den innehåller potentiellt olämpligt språk, personligt identifierbar information, klassificering (förhandsversion) och korrigeras automatiskt-version.

> [!NOTE]
> Funktionen datorstödd 'Klassificering' är en förhandsversion och endast stöd för engelska.

```
{
    "OriginalText": "Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.\r\nThese are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300.\r\nAlso, 544-56-7788 looks like a social security number (SSN).",
    "NormalizedText": "Is this a grabage or crap email abcdef@ abcd. com, phone: 6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, Redmond, WA 98052. \r\nThese are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300. \r\nAlso, 544- 56- 7788 looks like a social security number ( SSN) .",
"Misrepresentation": null,
    "PII": {
            "Email": [{
                "Detected": "abcdef@abcd.com",
                "SubType": "Regular",
                "Text": "abcdef@abcd.com",
                "Index": 32
                }],
            "IPA": [{
                "SubType": "IPV4",
                "Text": "255.255.255.255",
                "Index": 72
                }],
            "Phone": [{
                "CountryCode": "US",
                "Text": "6657789887",
                "Index": 56
                }, {
                "CountryCode": "US",
                "Text": "870 608 4000",
                "Index": 211
                }, {
                "CountryCode": "UK",
                "Text": "+44 870 608 4000",
                "Index": 207
                }, {
                "CountryCode": "UK",
                "Text": "0344 800 2400",
                "Index": 227
                }, {
                "CountryCode": "UK",
                "Text": "0800 820 3300",
                "Index": 244
            }],
         "Address": [{
                 "Text": "1 Microsoft Way, Redmond, WA 98052",
                "Index": 89
            }],
            "SSN": [{
                "Text": "999999999",
                "Index": 56
            }, {
                "Text": "999-99-9999",
                "Index": 266
            }]
        },
    "Classification": {
        "ReviewRecommended": true,
        "Category1": {
            "Score": 1.5113095059859916E-06
        },
        "Category2": {
            "Score": 0.12747249007225037
        },
        "Category3": {
            "Score": 0.98799997568130493
        }
        },
    "Language": "eng",
    "Terms": [{
            "Index": 21,
            "OriginalIndex": 21,
            "ListId": 0,
         "Term": "crap"
        }],
    "Status": {
            "Code": 3000,
            "Description": "OK",
            "Exception": null
        },
     "TrackingId": "2eaa012f-1604-4e36-a8d7-cc34b14ebcb4"
}
```

En detaljerad förklaring av alla avsnitt i JSON-svar finns i den [text moderering API-översikt](text-moderation-api.md).

## <a name="next-steps"></a>Nästa steg

Använda REST-API i koden eller börja med den [text moderering .NET Snabbstart](text-moderation-quickstart-dotnet.md) att integrera med ditt program.
