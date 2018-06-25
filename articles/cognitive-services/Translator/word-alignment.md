---
title: Word justering information med Microsoft översättare Text API | Microsoft Docs
description: Ta emot word justering information från Microsoft översättare Text API.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-text
ms.topic: article
ms.date: 12/14/2017
ms.author: v-jansko
ms.openlocfilehash: 9d8284db61235284ec0d5a1594c34687c89560e9
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35352608"
---
# <a name="how-to-receive-word-alignment-information"></a>Ta emot word justering information

## <a name="receiving-word-alignment-information"></a>Ta emot word justering information
Om du vill ta emot information om justering använder metoden Översätt och den valfria includeAlignment-parametern.

## <a name="alignment-information-format"></a>Justering informationsformat
Justering returneras som ett strängvärde i följande format för varje ord i källan. Information för varje ord är avgränsat med ett blanksteg, inklusive för icke-blankstegsavgränsad språk (skript) som kinesiska:

[[SourceTextStartIndex]: [SourceTextEndIndex]-[TgtTextStartIndex]: [TgtTextEndIndex]] *

Exempel justering sträng ”: 0:0-7:10 1:2-11:20 3:4-0:3 3:4-4:6 5:5-21:21”.

Med andra ord avgränsas start och end index, ett streck separerar språk och blanksteg mellan ord. Ett ord som kan justera med noll, en eller flera ord på andra språk och justerade orden kan vara icke sammanhängande. När ingen justering information är tillgänglig, vara elementet justering tomt. Metoden returnerar inget fel i så fall.

## <a name="restrictions"></a>Begränsningar
Justering returneras endast för en delmängd av språkpar nu:
* från engelska till andra språk.
* från ett annat språk till engelska utom kinesiska, förenklad kinesiska, traditionell kinesiska och lettiska till engelska
* från japanska till koreanska eller koreanska till japanska du får inte justering information om meningen är en kapslade översättning. Exempel på en kapslade översättning är ”det här är ett test”, ”jag gillar du” och andra hög frekvens meningar.

## <a name="example"></a>Exempel

Exempel JSON

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
