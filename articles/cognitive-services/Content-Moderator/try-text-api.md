---
title: Måttlig text med hjälp av API-Content Moderator för text redigering
titleSuffix: Azure Cognitive Services
description: 'Kontroll av textkontrollant med hjälp av API: et för text redigering i online-konsolen.'
services: cognitive-services
author: PatrickFarley
ms.author: pafarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 05/29/2019
ms.openlocfilehash: 7a7085eccf05fb65bf7b9036c738a1d17e91a433
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2019
ms.locfileid: "72935898"
---
# <a name="moderate-text-from-the-api-console"></a>Måttlig text från API-konsolen

Använd [API: et för text redigering](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f) i Azure Content moderator för att skanna ditt text innehåll för svordomar och jämföra det med anpassade och delade listor.

## <a name="get-your-api-key"></a>Hämta din API-nyckel

Innan du kan testa API: et i online-konsolen behöver du din prenumerations nyckel. Detta finns på fliken **Inställningar** i rutan **OCP-APIM-Subscription-Key** . Mer information finns i [Översikt](overview.md).

## <a name="navigate-to-the-api-reference"></a>Gå till API-referensen

Gå till [API-referensen för text redigering](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f). 

  Sidan med **text skärmen** öppnas.

## <a name="open-the-api-console"></a>Öppna API-konsolen

För **öppna API test-konsolen**väljer du den region som bäst beskriver din plats. 

  ![Val av sid region för text på skärmen](images/test-drive-region.png)

  API **-konsolen för text skärmen** öppnas.

## <a name="select-the-inputs"></a>Välj indata

### <a name="parameters"></a>Parametrar

Välj de frågeparametrar som du vill använda på text skärmen. I det här exemplet använder du standardvärdet för **språk**. Du kan också lämna det tomt eftersom åtgärden automatiskt identifierar det sannolika språket som en del av körningen.

> [!NOTE]
> För parametern **språk** tilldelar du `eng` eller låter den vara tom för att se den datorbaserade **klassificerings** svaret (för hands version). **Den här funktionen stöder enbart engelska**.
>
> För identifiering av **svordomar** kan du använda [ISO 639-3-koden](http://www-01.sil.org/iso639-3/codes.asp) för de språk som stöds i den här artikeln eller lämna det tomt.

För **Autokorrigering**, **PII**och **klassificering (för hands version)** väljer du **Sant**. Lämna fältet **listid** tomt.

  ![Frågeparametrar för text skärms konsolen](images/text-api-console-inputs.PNG)

### <a name="content-type"></a>Innehålls typ

För **innehålls typ**väljer du den typ av innehåll som du vill skärma. I det här exemplet använder du standard typen **text/oformaterad** innehålls typ. I rutan **OCP-APIM-Subscription-Key** anger du din prenumerations nyckel.

### <a name="sample-text-to-scan"></a>Exempel text att skanna

Skriv lite text i rutan **begär** text. I följande exempel visas ett avsiktligt skrivfel i texten.

```
Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.
These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300.
Also, 999-99-9999 looks like a social security number (SSN).
```

## <a name="analyze-the-response"></a>Analysera svaret

Följande svar visar de olika insikterna från API: et. Den innehåller potentiell svordom, personliga data, klassificering (för hands version) och den automatiskt korrigerade versionen.

> [!NOTE]
> Den datorbaserade klassificerings funktionen är i för hands version och har endast stöd för engelska.

```json
{"OriginalText":"Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.\r\nThese are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300.\r\nAlso, 544-56-7788 looks like a social security number (SSN).",
"NormalizedText":"Is this a grabage or crap email abcdef@ abcd. com, phone: 6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, Redmond, WA 98052. \r\nThese are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300. \r\nAlso, 544- 56- 7788 looks like a social security number ( SSN) .",
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
      "Text":"6657789887",
      "Index":56
    },
    {  
      "CountryCode":"US",
      "Text":"870 608 4000",
      "Index":211
    },
    {  
      "CountryCode":"UK",
      "Text":"+44 870 608 4000",
      "Index":207
    },
    {  
      "CountryCode":"UK",
      "Text":"0344 800 2400",
      "Index":227
    },
    {  
      "CountryCode":"UK",
      "Text":"0800 820 3300",
      "Index":244
    }
  ],
  "Address":[  
    {  
      "Text":"1 Microsoft Way, Redmond, WA 98052",
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

En detaljerad förklaring av alla avsnitt i JSON-svaret finns i vägledning för [text redigering](text-moderation-api.md) .

## <a name="next-steps"></a>Nästa steg

Använd REST API i koden eller börja med [text redigerings programmet .net-snabb start](text-moderation-quickstart-dotnet.md) för att integrera med ditt program.
