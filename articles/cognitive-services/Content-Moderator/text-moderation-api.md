---
title: Textmoderering - Content Moderator
description: Använd textmoderering för möjligt oönskad text, personligt identifierbar information, och anpassade listor med villkor.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 5a1007f2408b48c96f5eeaf585b94c8caa7ceb45
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60607130"
---
# <a name="learn-text-moderation-concepts"></a>Lär dig principerna för moderering av text

Använd Content Moderator datorstödd textmoderering och [mänsklig granskning](Review-Tool-User-Guide/human-in-the-loop.md) funktioner för begränsad textinnehåll.

Du antingen blockera, godkänna eller granska innehållet baserat på principer och tröskelvärden. Använda den för att förbättra människors moderering av miljöer där partners, anställda och konsumenter genererar textinnehåll. Dessa inkluderar chattrum, diskussionstavlor, chattrobotar, e-handel kataloger och dokument. 

Tjänstsvaret innehåller följande information:

- Svordomar: termen-baserade matchar med inbyggda termlista olämpliga på olika språk
- Klassificering: datorstödd klassificering i tre kategorier
- Personliga data
- Korrigeras automatiskt text
- Originaltexten
- Språk

## <a name="profanity"></a>Olämpligt språk

Om API: et identifierar eventuella olämpliga villkor i någon av de [språk som stöds](Text-Moderation-API-Languages.md), dessa villkor som ingår i svaret. Svaret innehåller även deras plats (`Index`) i den ursprungliga texten. Den `ListId` i följande exempel JSON som refererar till villkor finns i [anpassade termlistor](try-terms-list-api.md) om det är tillgängligt.

    "Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 0,
        "Term": "crap"
    }

> [!NOTE]
> För den **språk** parameter, tilldela `eng` eller lämna det tomt om du vill se den maskinassisterad **klassificering** svar (förhandsgranskningsfunktion). **Den här funktionen stöder endast engelska**.
>
> För **svordomar villkoren** identifiering, Använd den [ISO 639-3-kod](http://www-01.sil.org/iso639-3/codes.asp) språk som stöds visas i den här artikeln eller lämna det tomt.

## <a name="classification"></a>Klassificering

Content Moderator är maskinassisterad **text klassificering funktionen** stöder **endast engelska**, och hjälper dig identifiera potentiellt oönskade innehåll. Flaggade innehållet kan utvärderas som olämplig beroende på sammanhang. Den ger sannolikheten för varje kategori och rekommendera en mänsklig granskning. Funktionen använder en tränad modell för att identifiera möjliga missbruk, uttrycker sig nedsättande eller diskriminerande språk. Detta inkluderar slang, förkortat ord och stötande och avsiktligt felstavade ord för granskning. 

Följande extrahera i JSON-extrahera visar ett exempel på utdata:

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
    }

### <a name="explanation"></a>Förklaring

- `Category1` refererar till potentiella förekomst av språk som kan uppfattas som sexuellt explicit eller är olämpligt för barn i vissa situationer.
- `Category2` refererar till potentiella förekomst av språk som kan uppfattas som sexuellt något eller mogen i vissa situationer.
- `Category3` refererar till potentiella förekomst av språk som kan uppfattas som stötande i vissa situationer.
- `Score` är mellan 0 och 1. Ju högre poäng desto högre modellen är att förutsäga att kategorin kan användas. Den här funktionen är beroende av en statistisk modell i stället för manuellt kodade resultat. Vi rekommenderar att du testar med ditt eget innehåll att avgöra hur varje kategori stämmer överens med dina behov.
- `ReviewRecommended` är true eller false beroende på den interna poängen tröskelvärden. Kunder bör utvärdera om du vill använda det här värdet eller välja anpassade tröskelvärden baserat på deras innehåll principer.

## <a name="personal-data"></a>Personliga data

Funktionen PII identifierar potentiella förekomst av den här informationen:

- E-postadress
- USA-postadress
- IP-adress
- Amerikanskt telefonnummer
- Storbritannien telefonnummer
- Social Security Number (SSN)

I följande exempel visas en exempelsvaret:

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
            "Index": 212
            }, {
            "CountryCode": "UK",
            "Text": "+44 870 608 4000",
            "Index": 208
            }, {
            "CountryCode": "UK",
            "Text": "0344 800 2400",
            "Index": 228
            }, {
            "CountryCode": "UK",
            "Text": "0800 820 3300",
            "Index": 245
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
            "Index": 267
            }]
        }

## <a name="auto-correction"></a>Autokorrigering

Anta att indatatexten är ('lzay' och 'f0x ”är avsiktlig):

    The qu!ck brown f0x jumps over the lzay dog.

Om du ber om Autokorrigering innehåller svaret korrigerad version av texten:

    The quick brown fox jumps over the lazy dog.

## <a name="creating-and-managing-your-custom-lists-of-terms"></a>Skapa och hantera dina anpassade listor av villkoren

Medan standardvärdet, global termlista fungerar bra för de flesta fall kan du skärmen mot termer som är specifika för dina affärsbehov. Du kanske vill filtrera bort alla konkurrenskraftiga homofoner från inlägg av användare.

> [!NOTE]
> Det finns en maxgräns på **5 termlistor** där varje lista kan innehålla **högst 10 000 termer**.
>

I följande exempel visas den matchande List-ID:

    "Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 231.
        "Term": "crap"
    }

Content Moderator tillhandahåller en [termen lista API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f) med åtgärder för att hantera anpassade villkor visas. Börja med den [termen visar en lista över API-konsol](try-terms-list-api.md) och använda REST API-kodexempel. Se även de [Snabbstart för termen visar .NET](term-lists-quickstart-dotnet.md) om du är bekant med Visual Studio och C#.

## <a name="next-steps"></a>Nästa steg

Testkör den [Text moderering API konsolen](try-text-api.md) och använda REST API-kodexempel. Se även de [Text moderering .NET Snabbstart](text-moderation-quickstart-dotnet.md) om du är bekant med Visual Studio och C#.
