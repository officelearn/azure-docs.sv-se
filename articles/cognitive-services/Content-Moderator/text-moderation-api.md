---
title: Text moderator – Content Moderator
titleSuffix: Azure Cognitive Services
description: Använd text redigering för möjlig oönskad text, personliga data och anpassade listor med villkor.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 5a07f0749b59efc96b67df3ad5ed2fbf353be614
ms.sourcegitcommit: 36eb583994af0f25a04df29573ee44fbe13bd06e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2019
ms.locfileid: "74538834"
---
# <a name="learn-text-moderation-concepts"></a>Lär dig om text moderator koncept

Använd Content Moderator datorns stöd för text redigering och [mänsklig granskning](Review-Tool-User-Guide/human-in-the-loop.md) för att förstå text innehåll.

Du kan antingen blockera, godkänna eller granska innehållet baserat på dina principer och tröskelvärden. Använd den för att förstärka mänsklig kontroll av miljöer där partner, anställda och konsumenter genererar text innehåll. Dessa omfattar chattrum, diskussions tavlor, chattrobotar, e-Commerce-kataloger och dokument. 

Tjänste svaret innehåller följande information:

- Svordomar: term-baserad matchning med inbyggd lista över flera svordomar på olika språk
- Klassificering: maskinvarustödd klassificering i tre kategorier
- Person uppgifter
- Automatiskt korrigerad text
- Ursprunglig text
- Språk

## <a name="profanity"></a>Svordomar

Om API: n identifierar eventuella svordomar i något av de [språk som stöds](Text-Moderation-API-Languages.md), inkluderas dessa villkor i svaret. Svaret innehåller också deras plats (`Index`) i den ursprungliga texten. `ListId` i följande exempel-JSON refererar till termer som finns i [anpassade term listor](try-terms-list-api.md) om de är tillgängliga.

    "Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 0,
        "Term": "crap"
    }

> [!NOTE]
> För parametern **språk** tilldelar du `eng` eller låter den vara tom för att se den datorbaserade **klassificerings** svaret (för hands version). **Den här funktionen stöder enbart engelska**.
>
> För identifiering av **svordomar** kan du använda [ISO 639-3-koden](http://www-01.sil.org/iso639-3/codes.asp) för de språk som stöds i den här artikeln eller lämna det tomt.

## <a name="classification"></a>Klassificering

Content Moderator funktionen för maskin stöd för **text klassificering** stöder **endast engelska**och hjälper till att identifiera potentiellt oönskat innehåll. Det flaggade innehållet kan utvärderas som olämpligt beroende på kontext. Den förmedlar sannolikheten för varje kategori och kan rekommendera en mänsklig granskning. Funktionen använder en tränad modell för att identifiera eventuellt stötande, avvikande eller diskriminerande språk. Detta omfattar slang, förkortade ord, stötande och avsiktligt felstavade ord för granskning. 

Följande utdrag i JSON-extraheringen visar ett exempel på utdata:

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

- `Category1` avser potentiell närvaro av ett språk som kan anses vara sexuellt explicit eller vuxna i vissa situationer.
- `Category2` avser potentiell närvaro av ett språk som kan anses vara sexuellt eller mogna i vissa situationer.
- `Category3` avser potentiell närvaro av ett språk som kan betraktas som stötande i vissa situationer.
- `Score` är mellan 0 och 1. Ju högre poäng, desto högre blir modellen för att förutsäga att kategorin kan vara tillämplig. Den här funktionen förlitar sig på en statistisk modell i stället för att manuellt koda resultat. Vi rekommenderar att du testar med ditt eget innehåll för att avgöra hur varje kategori anpassar sig efter dina behov.
- `ReviewRecommended` är antingen sant eller falskt beroende på de interna Poäng tröskelvärdena. Kunderna bör bedöma om de ska använda det här värdet eller bestämma anpassade tröskelvärden baserat på deras innehålls principer.

## <a name="personal-data"></a>Person uppgifter

Funktionen personliga data identifierar potentiell närvaro av den här informationen:

- E-postadress
- E-postadress till oss
- IP-adress
- Telefonnummer till USA
- BRITTISKt telefonnummer
- Person nummer (SSN)

I följande exempel visas ett exempel svar:

```json
"PII":{ 
  "Email":[ 
    { 
      "Detected":"abcdef@abcd.com",
      "SubType":"Regular",
      "Text":"abcdef@abcd.com",
      "Index":32
    }
  ],
  "IPA":[ 
    { 
      "SubType":"IPV4",
      "Text":"255.255.255.255",
      "Index":72
    }
  ],
  "Phone":[ 
    { 
      "CountryCode":"US",
      "Text":"4255550111",
      "Index":56
    },
    { 
      "CountryCode":"US",
      "Text":"425 555 0111",
      "Index":212
    },
    { 
      "CountryCode":"UK",
      "Text":"+123 456 7890",
      "Index":208
    },
    { 
      "CountryCode":"UK",
      "Text":"0234 567 8901",
      "Index":228
    },
    { 
      "CountryCode":"UK",
      "Text":"0456 789 0123",
      "Index":245
    }
  ],
  "Address":[ 
    { 
      "Text":"1234 Main Boulevard, Panapolis WA 96555",
      "Index":89
    }
  ],
  "SSN":[ 
    { 
      "Text":"999999999",
      "Index":56
    },
    { 
      "Text":"999-99-9999",
      "Index":267
    }
  ]
}
```

## <a name="auto-correction"></a>Automatisk korrigering

Anta att indatamängden är ("lzay" och "f0x" är avsiktliga):

    The qu!ck brown f0x jumps over the lzay dog.

Om du ber om automatisk korrigering innehåller svaret den korrigerade versionen av texten:

    The quick brown fox jumps over the lazy dog.

## <a name="creating-and-managing-your-custom-lists-of-terms"></a>Skapa och hantera dina anpassade listor med villkor

Standardinställningen är att den globala listan över termer fungerar bra i de flesta fall, men du kanske vill skärmen mot de villkor som är specifika för dina affärs behov. Du kanske till exempel vill filtrera bort eventuella konkurrens märkes namn från inlägg av användare.

> [!NOTE]
> Det finns en maxgräns på **5 termlistor** där varje lista kan innehålla **högst 10 000 termer**.
>

I följande exempel visas matchnings List-ID:

    "Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 231.
        "Term": "crap"
    }

Content Moderator innehåller ett [API för term listor](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f) med åtgärder för att hantera anpassade term listor. Börja med [villkoret listar API-konsolen](try-terms-list-api.md) och Använd REST API kod exempel. Ta också en titt på [termen listar .net snabb start](term-lists-quickstart-dotnet.md) om du är bekant med Visual C#Studio och.

## <a name="next-steps"></a>Nästa steg

Testa API- [konsolen för text redigering](try-text-api.md) och Använd REST API kod exempel. Se även avsnittet text redigering i snabb starten för [.NET SDK](dotnet-sdk-quickstart.md) om du är bekant med Visual Studio och C#.
