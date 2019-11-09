---
title: Identifiera det talade språket automatiskt med Video Indexer – Azure
titleSuffix: Azure Media Services
description: Den här artikeln beskriver hur den Video Indexer språk identifierings modellen används för att automatiskt identifiera det talade språket i en video.
services: media-services
author: juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/12/2019
ms.author: ellbe
ms.openlocfilehash: ce3e488a6387f9a823d7c1b514b52af24944776b
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838998"
---
# <a name="automatically-identify-the-spoken-language-with-language-identification-model"></a>Identifiera det talade språket automatiskt med språk identifierings modellen

Video Indexer stöder automatisk språk identifiering (lock), vilket är en process för att automatiskt identifiera det talade språk innehållet från ljud och skicka medie filen som ska skrivas in i det dominerande identifierade språket. För närvarande har locket stöd för engelska, spanska, franska, tyska, italienska, kinesiska (förenklad), japanska, ryska och portugisiska (Brasiliansk). 

## <a name="choosing-auto-language-identification-on-indexing"></a>Välja automatisk språk identifiering vid indexering

När du indexerar eller [Omindexerar](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?) en video med hjälp av API: et väljer du alternativet `auto detect` i `sourceLanguage` parameter.

När du använder portalen går du till dina **konto videor** på Start sidan för [video Indexer](https://www.videoindexer.ai/) och hovrar över namnet på videon som du vill indexera om. Klicka på knappen index igen i det högra hörnet. I dialog rutan **Indexera om video** väljer du *Automatisk identifiering* från List rutan för **video källans språk** .

![identifiera automatiskt](./media/language-identification-model/auto-detect.png)

## <a name="model-output"></a>Modellens utdata

Video Indexer beskrivar videon enligt det mest sannolika språket om förtroendet för det språket är `> 0.6`. Om språket inte kan identifieras med förtroende förutsätter det att det talade språket är engelska. 

Modellens dominerande språk är tillgängligt i insikts-JSON som `sourceLanguage`-attribut (under root/video/Insights). Ett motsvarande förtroende poäng är också tillgängligt under `sourceLanguageConfidence`-attributet.

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

* Språk som stöds är engelska, spanska, franska, tyska, italienska, kinesiska (förenklad), japanska, ryska och brasiliansk portugisiska.
* Om ljudet innehåller andra språk än de som stöds ovan, är resultatet oväntat.
* Om Video Indexer inte kan identifiera språket med hög nog tillförlitlighet (`>0.6`) är reserv språket engelska.
* Det finns inget aktuellt stöd för filen med ljud från blandade språk. Om ljudet innehåller blandade språk är resultatet oväntat. 
* Ljud med låg kvalitet kan påverka modell resultatet.
* Modellen kräver minst en minut med tal i ljudet.
* Modellen är utformad för att identifiera ett spontant samtals tal (inte röst kommandon, loggar osv.).

## <a name="next-steps"></a>Nästa steg

* [Översikt](video-indexer-overview.md)
* [Identifiera och automatisk identifiering av innehåll i flera språk](multi-language-identification-transcription.md)
