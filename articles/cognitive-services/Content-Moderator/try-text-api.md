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
ms.openlocfilehash: ad365c2d4c171105d8dec89d818ef481361d1ff8
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81272601"
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
Is this a grabage or crap email abcdef@abcd.com, phone: 4255550111, IP: 
255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
```

## <a name="analyze-the-response"></a>Analysera svaret

Följande svar visar de olika insikterna från API:et. Den innehåller potentiella svordomar, personuppgifter, klassificering (förhandsgranskning) och den automatiskt korrigerade versionen.

> [!NOTE]
> Den maskinassisterade "Klassificering"-funktionen är endast i förhandsversion och stöder endast engelska.

```json
{
   "original_text":"Is this a grabage or crap email abcdef@abcd.com, phone: 
   6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.",
   "normalized_text":"   grabage  crap email abcdef@abcd.com, phone: 
   6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.",
   "auto_corrected_text":"Is this a garbage or crap email abcdef@abcd.com, phone: 
   6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.",
   "status":{
      "code":3000,
      "description":"OK"
   },
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
   },
   "language":"eng",
   "terms":[
      {
         "index":12,
         "original_index":21,
         "list_id":0,
         "term":"crap"
      }
   ],
   "tracking_id":"WU_ibiza_65a1016d-0f67-45d2-b838-b8f373d6d52e_ContentModerator.
   F0_fe000d38-8ecd-47b5-a8b0-4764df00e3b5"
}
```

En detaljerad förklaring av alla avsnitt i JSON-svaret finns i begreppsguiden för [textmodering.](text-moderation-api.md)

## <a name="next-steps"></a>Nästa steg

Använd REST API i koden eller följ [snabbstarten .NET SDK](dotnet-sdk-quickstart.md) för att integrera med ditt program.
