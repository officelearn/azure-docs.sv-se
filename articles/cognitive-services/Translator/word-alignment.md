---
title: Word-justering - API för översättare
titleSuffix: Azure Cognitive Services
description: Om du vill ta emot justeringsinformation använder du metoden Översätt och inkluderar parametern includeAlignment.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.custom: seodec18
ms.openlocfilehash: dd4ff1e39c062910f4627973c801dc3c51f345e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "73837231"
---
# <a name="how-to-receive-word-alignment-information"></a>Så här får du information om ordjustering

## <a name="receiving-word-alignment-information"></a>Ta emot information om ordjustering
Om du vill ta emot justeringsinformation använder du metoden Översätt och inkluderar parametern includeAlignment.

## <a name="alignment-information-format"></a>Format för justeringsinformation
Justeringen returneras som ett strängvärde för följande format för varje ord i källan. Informationen för varje ord avgränsas med ett blanksteg, inklusive för icke-utrymmesavgränsade språk (skript) som kinesiska:

[[[SourceTextStartIndex]:[SourceTextEndIndex]–[TgtTextStartIndex]:[TgtTextEndIndex]] *

Exempel justering sträng: "0:0-7:10 1:2-11:20 3:4-0:3 3:4-4:6 5:5-21:21".

Med andra ord separerar kolon start- och slutindex, strecket separerar språken och utrymmet separerar orden. Ett ord kan justeras med noll, en eller flera ord på det andra språket, och de justerade orden kan vara icke-sammanhängande. När det inte finns någon justeringsinformation är justeringselementet tomt. Metoden returnerar inget fel i det fallet.

## <a name="restrictions"></a>Begränsningar
Justering returneras endast för en delmängd av språkparen just nu:
* från engelska till något annat språk;
* från något annat språk till engelska med undantag för förenklad kinesiska, traditionell kinesiska och lettiska
* från japanska till koreanska eller från koreanska till japanska Du kommer inte att få justeringsinformation om meningen är en konserverad översättning. Exempel på en konserverad översättning är "Detta är ett test", "Jag älskar dig", och andra högfrekventa meningar.

## <a name="example"></a>Exempel

Exempel på JSON

```json
[
  {
    "translations": [
      {
        "text": "Kann ich morgen Ihr Auto fahren?",
        "to": "de",
        "alignment": {
          "proj": "0:2-0:3 4:4-5:7 6:10-25:30 12:15-16:18 17:19-20:23 21:28-9:14 29:29-31:31"
        }
      }
    ]
  }
]
```
