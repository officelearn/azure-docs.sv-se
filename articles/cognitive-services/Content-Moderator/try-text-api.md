---
title: Måttlig text med Text Avbrottsmoderering API i Azure Content kontrollant | Microsoft Docs
description: Testkör text avbrottsmoderering med Text Avbrottsmoderering API i online-konsolen.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 08/05/2017
ms.author: sajagtap
ms.openlocfilehash: ed696c31a886626819414c45eb7995edaf161fff
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35352722"
---
# <a name="moderate-text-from-the-api-console"></a>Måttlig text från konsolen API

Använd den [Text Avbrottsmoderering API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f) Azure innehåll kontrollant söker igenom din textinnehåll. Åtgärden söker igenom innehållet bort olämpligt material och jämför innehållet mot anpassade och delade blacklists.


## <a name="get-your-api-key"></a>Hämta din API-nyckel
Innan du kan testa API i konsolen online måste din nyckel för prenumerationen. Den finns på den **inställningar** fliken den **Ocp-Apim-prenumeration-nyckeln** rutan. Mer information finns i [översikt](overview.md).

## <a name="navigate-to-the-api-reference"></a>Navigera till API-referens
Gå till den [Text Avbrottsmoderering API-referens för](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f). 

  Den **Text - skärmen** öppnas.

## <a name="open-the-api-console"></a>Öppna konsolen API
För **öppna API-testet konsolen**, väljer du den region som bäst beskriver din plats. 

  ![Text: skärmen val av region](images/test-drive-region.png)

  Den **Text - skärmen** API-konsolen öppnas.

## <a name="select-the-inputs"></a>Välj indata

### <a name="parameters"></a>Parametrar
Välj frågeparametrar som du vill använda i textskärmen. Det här exemplet använder standardvärdet för **språk**. Du kan också lämna det tomt eftersom åtgärden identifierar automatiskt språket som sannolikt som en del av körningen.

> [!NOTE]
> För den **språk** parameter, tilldela `eng` eller lämna det tomt om du vill se dator-stödd **klassificering** svar (förhandsgranskningsfunktion). **Den här funktionen engelska har endast stöd för**.
>
> För **svordomar villkoren** identifiering, Använd den [ISO 639-3-kod](http://www-01.sil.org/iso639-3/codes.asp) språk som stöds anges i den här artikeln eller lämna det tomt.

För **Autokorrigering**, **PII**, och **klassificera (förhandsgranskning)** väljer **SANT**. Lämna den **ListId** fältet tomt.

  ![Text: skärmen konsolen frågeparametrar](images/text-api-console-inputs.PNG)

### <a name="content-type"></a>Innehållstyp
För **Content-Type**, Välj typ av innehåll som du vill skärmen. I det här exemplet använder du standard **text/plain** innehållstyp. I den **Ocp-Apim-prenumeration-nyckeln** ange din prenumeration nyckel.

### <a name="sample-text-to-scan"></a>Exempeltext skanna
I den **Begärandetext** ange lite text. I följande exempel visas en avsiktlig skrivfel i texten.

> [!NOTE]
> Ogiltig personnummer i följande exempel är avsiktlig. Syftet är att förmedla Exempelindata och utdata format.

```
    Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.
    These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300.
    Also, 999-99-9999 looks like a social security number (SSN).
```

### <a name="text-classification-feature"></a>Funktionen för klassificering av text

I följande exempel finns innehåll kontrollant datorn stödd text klassificering svar. Det hjälper dig att identifiera potentiellt oönskade innehåll. Flaggade innehållet kan betraktas som olämpligt beroende på kontext. Förutom att förmedla sannolikheten för varje kategori kan det rekommenderar en mänsklig granskning av innehållet. Funktionen använder en tränad modell för att identifiera möjliga missbruk, nedsättning eller diskriminerande språk. Detta inkluderar slang, förkortat ord och stötande och avsiktligt felstavade ord för granskning. 

#### <a name="explanation"></a>Förklaring

- `Category1` representerar potentiella förekomst av språk som kan uppfattas som webbplatser explicit eller vuxna i vissa situationer.
- `Category2` representerar potentiella förekomst av språk som kan uppfattas som webbplatser något eller mogen i vissa situationer.
- `Category3` representerar potentiella förekomst av språk som kan uppfattas som stötande i vissa situationer.
- `Score` är mellan 0 och 1. Ju högre poäng, desto högre modellen är att förutsäga att kategorin kan användas. Den här förhandsgranskningen är beroende av en statistisk modell i stället för manuellt kodade resultat. Vi rekommenderar att du testar med ditt eget innehåll för att avgöra hur varje kategori passar dina behov.
- `ReviewRecommended` är antingen SANT eller FALSKT beroende på den interna poängen tröskelvärden. Kunder bör bedöma om du vill använda det här värdet eller besluta om anpassade tröskelvärden baserat på deras innehåll principer.

### <a name="analyze-the-response"></a>Analysera svaret
Följande meddelande visar de olika insikter från API: et. Den innehåller potentiella svordomar, personligt identifierbar information, klassificering (förhandsversion) och version korrigeras automatiskt.

> [!NOTE]
> Funktionen datorn stödd 'Klassificering' är en förhandsversion och har endast stöd för engelska.

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

En detaljerad förklaring av alla avsnitt i JSON-svar finns i den [text avbrottsmoderering API översikt](text-moderation-api.md).

## <a name="next-steps"></a>Nästa steg

Använda REST-API i koden eller starta med den [text avbrottsmoderering .NET quickstart](text-moderation-quickstart-dotnet.md) att integrera med ditt program.
