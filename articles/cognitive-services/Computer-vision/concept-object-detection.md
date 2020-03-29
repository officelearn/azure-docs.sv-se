---
title: Objektidentifiering - Datorseende
titleSuffix: Azure Cognitive Services
description: Lär dig begrepp som är relaterade till objektidentifieringsfunktionen i API:et för visuellt innehåll – användning och begränsningar.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 3957e15a09bd7e7ecd814d169451af3241108b64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131663"
---
# <a name="detect-common-objects-in-images"></a>Identifiera vanliga objekt i bilder

Objektidentifiering liknar [taggning](concept-tagging-images.md), men API:et returnerar markeringsramens koordinater (i pixlar) för varje objekt som hittas. Om en bild exempelvis innehåller en hund, en katt och en person, kommer identifieringsåtgärden visa en lista över dessa objekt tillsammans med deras koordinater i bilden. Du kan använda den här funktionen för att bearbeta relationerna mellan objekten i en bild. Du kan också avgöra om det finns flera förekomster av samma tagg i en bild.

Identifiera API använder taggar baserat på de objekt eller levande ting som identifieras i bilden. Det finns för närvarande inget formellt samband mellan taggningstaxonomi och objektidentifieringstaxonomi. På en begreppsmässig nivå hittar identifiera API bara objekt och levande ting, medan tagg-API:et också kan innehålla kontextuella termer som "inomhus", som inte kan lokaliseras med markeringsrutor.

## <a name="object-detection-example"></a>Exempel på objektidentifiering

Följande JSON-svar illustrerar vad Datorseende returnerar när objekt identifieras i exempelbilden.

![En kvinna som använder en Microsoft Surface-enhet i ett kök](./Images/windows-kitchen.jpg)

```json
{
   "objects":[
      {
         "rectangle":{
            "x":730,
            "y":66,
            "w":135,
            "h":85
         },
         "object":"kitchen appliance",
         "confidence":0.501
      },
      {
         "rectangle":{
            "x":523,
            "y":377,
            "w":185,
            "h":46
         },
         "object":"computer keyboard",
         "confidence":0.51
      },
      {
         "rectangle":{
            "x":471,
            "y":218,
            "w":289,
            "h":226
         },
         "object":"Laptop",
         "confidence":0.85,
         "parent":{
            "object":"computer",
            "confidence":0.851
         }
      },
      {
         "rectangle":{
            "x":654,
            "y":0,
            "w":584,
            "h":473
         },
         "object":"person",
         "confidence":0.855
      }
   ],
   "requestId":"a7fde8fd-cc18-4f5f-99d3-897dcd07b308",
   "metadata":{
      "width":1260,
      "height":473,
      "format":"Jpeg"
   }
}
```

## <a name="limitations"></a>Begränsningar

Det är viktigt att notera begränsningarna för objektidentifiering så att du kan undvika eller mildra effekterna av falska negativ (missade objekt) och begränsad detaljrikedom.

* Objekt identifieras i allmänhet inte om de är små (mindre än 5 % av bilden).
* Objekt identifieras i allmänhet inte om de är ordnade tätt tillsammans (en stapel med plattor, till exempel).
* Objekt är inte differentierade efter varumärke eller produktnamn (olika typer av läsk på en butikshylla, till exempel). Du kan dock hämta varumärkesinformation från en bild med hjälp av [varumärkesidentifieringsfunktionen.](concept-brand-detection.md)

## <a name="use-the-api"></a>Använda API:et

Objektidentifieringsfunktionen är en del av [Api:et för analyseravbildning.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) Du kan anropa det här API:et via en inbyggd SDK eller via REST-anrop. Inkludera `Objects` i frågeparametern **visualFeatures.** Sedan, när du får hela JSON svar, helt enkelt tolka `"objects"` strängen för innehållet i avsnittet.

* [Snabbstart: Datorseende .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [Snabbstart: Analysera en bild (REST API)](./quickstarts/csharp-analyze.md)