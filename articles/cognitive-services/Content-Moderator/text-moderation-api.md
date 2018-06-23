---
title: Azure innehåll kontrollant - Text Avbrottsmoderering | Microsoft Docs
description: Använda text avbrottsmoderering för möjliga oönskad text, personligt identifierbar information, och anpassade listor av villkoren.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/30/2018
ms.author: sajagtap
ms.openlocfilehash: 5783a7a06d75a409969abad011de3bbd31dec292
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35352725"
---
# <a name="text-moderation"></a>Textmoderering

Använda innehåll kontrollant datorn stödd text avbrottsmoderering och [hr-i-the-loop](Review-Tool-User-Guide/human-in-the-loop.md) funktioner för måttlig textinnehåll.

Företag använda text avbrottsmoderering tjänsten för att blockera, godkänna eller granska innehållet baserat på deras principer och tröskelvärden. Tjänsten avbrottsmoderering text kan användas för att utöka mänsklig avbrottsmoderering i miljöer som kräver partners, anställda och konsumenter att generera textinnehåll. Dessa inkluderar chatt-rum, diskussion anslagstavlor, chatbots, e-handel kataloger, dokument och mycket mer. 

API: et söker igenom inkommande text (högst 1024 tecken) bort olämpligt material, klassificerar för möjliga oönskad text (förhandsgranskning) autocorrects text och identifierar potentiella personligt identifierbar Information (PII). Också matchas mot anpassade listor av villkoren. Funktionen Autokorrigering hjälper fånga avsiktligt stavningen. När innehållet har bearbetats returnerar tjänsten en detaljerad svar. Du kan använda svaret för att skapa en mänsklig granskning i verktyget granskning eller dra nedåt, osv.

Service-svaret innehåller följande information:

- Svordomar: termen-baserade matchar med inbyggda lista över vulgärt termer på flera språk
- Klassificering: datorn stödd klassificering i tre kategorier
- Personligt identifierbar Information (PII)
- Automatisk åtgärdat text
- Ursprungliga texten
- Språk

## <a name="profanity"></a>Svordomar

Om API: et upptäcker eventuella vulgärt villkor i någon av de [språk som stöds](Text-Moderation-API-Languages.md), dessa villkor inkluderas i svaret. Svaret innehåller också deras plats (`Index`) i den ursprungliga texten. Den `ListId` i följande exempel JSON refererar till villkor hittades i [anpassade termen listor](try-terms-list-api.md) om de är tillgängliga.

    "Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 0,
        "Term": "crap"
    }

> [!NOTE]
> För den **språk** parameter, tilldela `eng` eller lämna det tomt om du vill se dator-stödd **klassificering** svar (förhandsgranskningsfunktion). **Den här funktionen engelska har endast stöd för**.
>
> För **svordomar villkoren** identifiering, Använd den [ISO 639-3-kod](http://www-01.sil.org/iso639-3/codes.asp) språk som stöds anges i den här artikeln eller lämna det tomt.

## <a name="classification"></a>Klassificering

Innehåll kontrollant är datorn stödd **text klassificering funktionen** stöder **endast engelska**, och hjälper dig identifiera potentiellt oönskade innehåll. Flaggade innehållet kan betraktas som olämpligt beroende på kontext. Förutom att förmedla sannolikheten för varje kategori kan det rekommenderar en mänsklig granskning av innehållet. Funktionen använder en tränad modell för att identifiera möjliga missbruk, nedsättning eller diskriminerande språk. Detta inkluderar slang, förkortat ord och stötande och avsiktligt felstavade ord för granskning. 

Följande utdrag i JSON-extrahera visar ett exempel på utdata:

> [!NOTE]
> Dator-stödd 'Klassificering'-funktionen är i förhandsgranskning.

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

- `Category1` representerar potentiella förekomst av språk som kan uppfattas som webbplatser explicit eller vuxna i vissa situationer.
- `Category2` representerar potentiella förekomst av språk som kan uppfattas som webbplatser något eller mogen i vissa situationer.
- `Category3` representerar potentiella förekomst av språk som kan uppfattas som stötande i vissa situationer.
- `Score` är mellan 0 och 1. Ju högre poäng, desto högre modellen är att förutsäga att kategorin kan användas. Den här förhandsgranskningen är beroende av en statistisk modell i stället för manuellt kodade resultat. Vi rekommenderar att du testar med ditt eget innehåll för att avgöra hur varje kategori passar dina behov.
- `ReviewRecommended` är antingen SANT eller FALSKT beroende på den interna poängen tröskelvärden. Kunder bör bedöma om du vill använda det här värdet eller besluta om anpassade tröskelvärden baserat på deras innehåll principer.

## <a name="personally-identifiable-information-pii"></a>Personligt identifierbar Information (PII)

Funktionen PII identifierar potentiella förekomst av den här informationen:

- E-postadress
- USA postadress
- IP-adress
- Telefonnummer i USA
- Storbritannien telefonnummer
- Personnummer (SSN)

I följande exempel visas en exempelsvar:

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

## <a name="auto-correction"></a>Automatisk korrigering

Anta att indatatexten är ('lzay' och 'f0x' är avsiktlig):

    The qu!ck brown f0x jumps over the lzay dog.

Om du ber om automatisk korrigering innehåller svaret korrigerade version av texten:

    The quick brown fox jumps over the lazy dog.

## <a name="creating-and-managing-your-custom-lists-of-terms"></a>Skapa och hantera din egen lista över villkor

Medan standard global lista över villkor fungerar bra för de flesta fall måste du skärmen mot termer som är specifika för dina affärsbehov. Du kanske vill filtrerar ut eventuella konkurrerande varumärken från inlägg av användare. Din tröskelvärdet tillåtna textinnehåll kan skilja sig från listan som standard.

> [!NOTE]
> Det finns en övre gräns för **5 termen visar** med varje lista till **inte överstiga 10 000 villkoren**.
>

I följande exempel visas den matchande List-ID:

    "Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 231.
        "Term": "crap"
    }

Innehåll kontrollanten ger en [termen listan API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f) med åtgärder för att hantera anpassade villkor visas. Börja med den [termen innehåller API-konsolen](try-terms-list-api.md) och använda REST API-kodexempel. Se även den [termen visar .NET quickstart](term-lists-quickstart-dotnet.md) om du är bekant med Visual Studio och C#.

## <a name="next-steps"></a>Nästa steg

Testkör den [Text avbrottsmoderering API konsolen](try-text-api.md) och använda REST API-kodexempel. Se även den [Text avbrottsmoderering .NET quickstart](text-moderation-quickstart-dotnet.md) om du är bekant med Visual Studio och C#.
