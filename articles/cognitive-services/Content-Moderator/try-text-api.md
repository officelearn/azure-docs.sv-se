---
title: Moderera text med hjälp av API för textmoderering - Innehållsmoderator
titleSuffix: Azure Cognitive Services
description: Provkörningstextmoderering med hjälp av API:et för textmoderering i onlinekonsolen.
services: cognitive-services
author: PatrickFarley
ms.author: pafarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 05/29/2019
ms.openlocfilehash: e0930558f31b27a77fa2cd6b44fcea2fe9091086
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "74538828"
---
# <a name="moderate-text-from-the-api-console"></a>Moderera text från API-konsolen

Använd [API:et för textmoderering](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f) i Azure Content Moderator för att söka igenom textinnehållet efter svordomar och jämföra det med anpassade och delade listor.

## <a name="get-your-api-key"></a>Hämta din API-nyckel

Innan du kan provköra API:et i onlinekonsolen behöver du din prenumerationsnyckel. Detta finns på fliken **Inställningar** i rutan **Ocp-Apim-Subscription-Key.** Mer information finns i [Översikt](overview.md).

## <a name="navigate-to-the-api-reference"></a>Navigera till API-referensen

Gå till [API-referensen för textmoderering](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f). 

  Sidan **Text - Skärm** öppnas.

## <a name="open-the-api-console"></a>Öppna API-konsolen

För **Open API-testkonsol**väljer du den region som bäst beskriver din plats. 

  ![Text - Val av skärmsida region](images/test-drive-region.png)

  **API-konsolen Text - Skärm** öppnas.

## <a name="select-the-inputs"></a>Välj indata

### <a name="parameters"></a>Parametrar

Markera de frågeparametrar som du vill använda på textskärmen. I det här exemplet använder du standardvärdet för **språk**. Du kan också lämna det tomt eftersom åtgärden automatiskt identifierar det troliga språket som en del av körningen.

> [!NOTE]
> För **language** språkparametern `eng` tilldelar eller lämnar den tom för att se det maskinstödda **klassificeringssvaret** (förhandsgranskningsfunktionen). **Den här funktionen stöder endast engelska**.
>
> För identifiering av **svordomar termer,** använd [ISO 639-3-koden](http://www-01.sil.org/iso639-3/codes.asp) för de språk som stöds i den här artikeln, eller lämna den tom.

För **autokorrigering**, **PII**och **klassificera (förhandsgranska)** väljer du **sant**. Lämna **listid-fältet** tomt.

  ![Text - Frågeparametrar för skärmkonsol](images/text-api-console-inputs.PNG)

### <a name="content-type"></a>Innehållstyp

För **Content-Type**väljer du den typ av innehåll som du vill visa. I det här exemplet använder du standardtypen **text/oformaterat** innehåll. Ange din prenumerationsnyckel i rutan **Ocp-Apim-Subscription-Key.**

### <a name="sample-text-to-scan"></a>Exempel på text att skanna

Ange text i rutan **Begäran.** I följande exempel visas ett avsiktligt stavfel i texten.

```
Is this a grabage or crap email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555. These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300. Also, 999-99-9999 looks like a social security number (SSN).
```

## <a name="analyze-the-response"></a>Analysera svaret

Följande svar visar de olika insikterna från API:et. Den innehåller potentiella svordomar, personuppgifter, klassificering (förhandsgranskning) och den automatiskt korrigerade versionen.

> [!NOTE]
> Den maskinassisterade "Klassificering"-funktionen är endast i förhandsversion och stöder endast engelska.

```json
{"OriginalText":"Is this a grabage or crap email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.\r\nThese are all UK phone numbers: +44 123 456 7890 or 0234 567 8901 or 0456 789 0123.\r\nAlso, 999-99-9999 looks like a social security number (SSN).",
"NormalizedText":"Is this a grabage or crap email abcdef@ abcd. com, phone: 4255550111, IP: 255. 255. 255. 255, 1234 Main Boulevard, Panapolis WA 96555. \r\nThese are all UK phone numbers: +44 123 456 7890 or 0234 567 8901 or 0456 789 0123. \r\nAlso, 999- 99- 9999 looks like a social security number ( SSN) .",
"Misrepresentation":null,
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
      "Index":211
    },
    {  
      "CountryCode":"UK",
      "Text":"+44 123 456 7890",
      "Index":207
    },
    {  
      "CountryCode":"UK",
      "Text":"0234 567 8901",
      "Index":227
    },
    {  
      "CountryCode":"UK",
      "Text":"0456 789 0123",
      "Index":244
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
      "Index":266
    }
  ]
},
"Classification":{  
  "ReviewRecommended":true,
  "Category1":{  
    "Score":1.5113095059859916E-06
  },
  "Category2":{  
    "Score":0.12747249007225037
  },
  "Category3":{  
    "Score":0.98799997568130493
  }
},
"Language":"eng",
"Terms":[  
  {  
    "Index":21,
    "OriginalIndex":21,
    "ListId":0,
    "Term":"crap"
  }
],
"Status":{  
  "Code":3000,
  "Description":"OK",
  "Exception":null
},
"TrackingId":"2eaa012f-1604-4e36-a8d7-cc34b14ebcb4"
}
```

En detaljerad förklaring av alla avsnitt i JSON-svaret finns i begreppsguiden för [textmodering.](text-moderation-api.md)

## <a name="next-steps"></a>Nästa steg

Använd REST API i koden eller följ [snabbstarten .NET SDK](dotnet-sdk-quickstart.md) för att integrera med ditt program.
