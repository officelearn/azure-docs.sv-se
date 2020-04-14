---
title: Textmoderation - Innehållsmoderator
titleSuffix: Azure Cognitive Services
description: Använd textmoderering för oönskad text, personuppgifter och anpassade listor med termer.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 41e88dd5a08de485f770559959843ba3b54e590f
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81274018"
---
# <a name="learn-text-moderation-concepts"></a>Lär dig begrepp för textmoderering

Använd Content Moderators datorstödda textmodering och [funktioner för mänsklig granskning](Review-Tool-User-Guide/human-in-the-loop.md) för att moderera textinnehållet.

Du blockerar, godkänner eller granskar innehållet utifrån dina principer och tröskelvärden. Använd den för att öka den mänskliga måttfullheten i miljöer där partners, anställda och konsumenter genererar textinnehåll. Det kan vara chattrum, diskussionstavlor, chattrobotar, e-handelskataloger dokument. 

Tjänstsvaret innehåller följande information:

- Svordomar: termbaserad matchning med inbyggd lista över profana termer på olika språk
- Klassificering: maskinassisterad klassificering i tre kategorier
- Personuppgifter
- Automatiskt korrigerad text
- Originaltext
- Språk

## <a name="profanity"></a>Olämpligt språk

Om API:et upptäcker några profana termer på något av de [språk som stöds](Text-Moderation-API-Languages.md)inkluderas dessa termer i svaret. Svaret innehåller också deras`Index`placering ( ) i den ursprungliga texten. I `ListId` följande prov JSON refererar till termer som finns i [anpassade termlistor](try-terms-list-api.md) om sådana finns tillgängliga.

    "Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 0,
        "Term": "crap"
    }

> [!NOTE]
> För **language** språkparametern `eng` tilldelar eller lämnar den tom för att se det maskinstödda **klassificeringssvaret** (förhandsgranskningsfunktionen). **Den här funktionen stöder endast engelska**.
>
> För identifiering av **svordomar termer,** använd [ISO 639-3-koden](http://www-01.sil.org/iso639-3/codes.asp) för de språk som stöds i den här artikeln, eller lämna den tom.

## <a name="classification"></a>Klassificering

Innehållsmoderatorns maskinstödda **textklassificeringsfunktion** stöder **endast engelska**och hjälper till att identifiera potentiellt oönskat innehåll. Det flaggade innehållet kan bedömas som olämpligt beroende på sammanhanget. Det förmedlar sannolikheten för varje kategori och kan rekommendera en mänsklig översyn. Funktionen använder en utbildad modell för att identifiera ett eventuellt kränkande, nedsättande eller diskriminerande språk. Detta inkluderar slang, förkortade ord, stötande och avsiktligt felstavade ord för granskning. 

Följande utdrag i JSON-extraktet visar ett exempel på utdata:

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

- `Category1`avser potentiell förekomst av språk som kan betraktas som sexuellt explicit eller vuxen i vissa situationer.
- `Category2`avser potentiell närvaro av språk som kan anses vara sexuellt suggestivt eller moget i vissa situationer.
- `Category3`avser potentiell närvaro av språk som kan anses stötande i vissa situationer.
- `Score`är mellan 0 och 1. Ju högre poäng, desto högre modell förutspår att kategorin kan vara tillämplig. Den här funktionen är beroende av en statistisk modell i stället för manuellt kodade resultat. Vi rekommenderar att du testar med ditt eget innehåll för att avgöra hur varje kategori anpassar sig till dina krav.
- `ReviewRecommended`är antingen sant eller falskt beroende på de interna poängtrösklarna. Kunder bör bedöma om de ska använda det här värdet eller besluta om anpassade tröskelvärden baserat på deras innehållsprinciper.

## <a name="personal-data"></a>Personuppgifter

Personuppgiftsfunktionen identifierar den potentiella förekomsten av denna information:

- E-postadress
- Amerikansk postadress
- IP-adress
- Amerikanskt telefonnummer

I följande exempel visas ett exempelsvar:

```json
"pii":{
  "email":[
      {
        "detected":"abcdef@abcd.com",
        "sub_type":"Regular",
        "text":"abcdef@abcd.com",
        "index":32
      }
  ],
  "ssn":[

  ],
  "ipa":[
      {
        "sub_type":"IPV4",
        "text":"255.255.255.255",
        "index":72
      }
  ],
  "phone":[
      {
        "country_code":"US",
        "text":"6657789887",
        "index":56
      }
  ],
  "address":[
      {
        "text":"1 Microsoft Way, Redmond, WA 98052",
        "index":89
      }
  ]
}
```

## <a name="auto-correction"></a>Automatisk korrigering

Anta att indatatexten är ("lzay" och "f0x" är avsiktliga):

    The qu!ck brown f0x jumps over the lzay dog.

Om du ber om automatisk korrigering innehåller svaret den korrigerade versionen av texten:

    The quick brown fox jumps over the lazy dog.

## <a name="creating-and-managing-your-custom-lists-of-terms"></a>Skapa och hantera dina anpassade listor med termer

Även om standardlistan över termer fungerar bra för de flesta fall kanske du vill söka mot termer som är specifika för dina affärsbehov. Du kanske till exempel vill filtrera bort alla konkurrenskraftiga varumärken från inlägg från användare.

> [!NOTE]
> Det finns en maxgräns på **5 termlistor** där varje lista kan innehålla **högst 10 000 termer**.
>

I följande exempel visas matchande list-ID:

    "Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 231.
        "Term": "crap"
    }

Innehållsmoderatorn tillhandahåller [ett termlist-API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f) med åtgärder för att hantera anpassade termlistor. Börja med [API-konsolen Term Lists](try-terms-list-api.md) och använd rest-API-kodexemplen. Kolla också in [snabbstarten Term List .NET](term-lists-quickstart-dotnet.md) om du är bekant med Visual Studio och C#.

## <a name="next-steps"></a>Nästa steg

Testa [api-konsolen textmodering](try-text-api.md) och använd rest-API-kodexemplen. Kolla också in avsnittet Textmodering i [snabbstarten för .NET SDK](dotnet-sdk-quickstart.md) om du är bekant med Visual Studio och C#.
