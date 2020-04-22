---
title: Använda Video Indexer för att automatiskt identifiera talade språk - Azure
titleSuffix: Azure Media Services
description: I den här artikeln beskrivs hur språkidentifieringsmodellen för videoindexeraren används för att automatiskt identifiera det talade språket i en video.
services: media-services
author: juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 04/12/2020
ms.author: ellbe
ms.openlocfilehash: 3a71a29fdf4af10162e2f7961fb457d0e99b18e8
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687128"
---
# <a name="automatically-identify-the-spoken-language-with-language-identification-model"></a>Identifiera automatiskt det talade språket med språkidentifieringsmodell

Video Indexer stöder automatisk språkidentifiering (LID), vilket är processen att automatiskt identifiera det talade språkets innehåll från ljud och skicka mediefilen som ska transkriberas på det dominerande identifierade språket. 

För närvarande LID stöder: engelska, spanska, franska, tyska, italienska, mandarin kinesiska, japanska, ryska och portugisiska (brasilianska). 

Se till att gå igenom avsnittet [Riktlinjer och begränsningar](#guidelines-and-limitations) nedan.

## <a name="choosing-auto-language-identification-on-indexing"></a>Välja automatisk språkidentifiering vid indexering

När du indexerar eller [indexerar om](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?) `auto detect` en video `sourceLanguage` med API:et väljer du alternativet i parametern.

När du använder portalen går du till dina **kontovideor** på startsidan [för Video indexeraren](https://www.videoindexer.ai/) och hovrar över namnet på videon som du vill indexera om. Klicka på knappen indexera om till höger. I dialogrutan **Indexera om video** väljer du *Automatisk identifiering* i listrutan **Videokällans språk.**

![automatisk identifiering](./media/language-identification-model/auto-detect.png)

## <a name="model-output"></a>Modell-utdata

Video Indexer transkriberar videon enligt det mest sannolika `> 0.6`språket om förtroendet för det språket är . Om språket inte kan identifieras med tillförsikt förutsätter det att det talade språket är engelska. 

Modell dominerande språk är tillgänglig i insikter `sourceLanguage` JSON som attribut (under root / videor / insikter). En motsvarande konfidenspoäng `sourceLanguageConfidence` är också tillgänglig under attributet.

```json
"insights": {
        "version": "1.0.0.0",
        "duration": "0:05:30.902",
        "sourceLanguage": "fr-FR",
        "language": "fr-FR",
        "transcript": [...],
        . . .
        "sourceLanguageConfidence": 0.8563
      },
```

## <a name="guidelines-and-limitations"></a>Riktlinjer och begränsningar

* Automatisk språkidentifiering (LID) stöder följande språk: 

    Engelska, spanska, franska, tyska, italienska, mandarin chines, japanska, ryska och portugisiska (brasiliansk).
* Även om Video Indexer stöder arabiska (Modern Standard och Levantine), Hindi och koreanska, stöds inte dessa språk i LID.
* Om ljudet innehåller andra språk än listan ovan som stöds är resultatet oväntat.
* Om Video Indexer inte kan identifiera språket`>0.6`med tillräckligt hög konfidens ( ) är reservspråket engelska.
* Det finns inget aktuellt stöd för fil med blandade språk ljud. Om ljudet innehåller blandade språk blir resultatet oväntat. 
* Ljud av låg kvalitet kan påverka modellresultaten.
* Modellen kräver minst en minuts tal i ljudet.
* Modellen är utformad för att känna igen ett spontant samtalstal (inte röstkommandon, sång, etc.).

## <a name="next-steps"></a>Nästa steg

* [Översikt](video-indexer-overview.md)
* [Identifiera och transkribera innehåll med flera språk automatiskt](multi-language-identification-transcription.md)
