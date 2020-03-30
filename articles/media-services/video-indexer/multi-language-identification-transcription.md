---
title: Identifiera och transkribera innehåll med flera språk automatiskt med Video Indexer
titleSuffix: Azure Media Services
description: Det här avsnittet visar hur du automatiskt identifierar och transkriberar flerspråkigt innehåll med Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/01/2019
ms.author: juliako
ms.openlocfilehash: f0dede42891069bb5d01ddc33f3797c20c5493d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72968746"
---
# <a name="automatically-identify-and-transcribe-multi-language-content-preview"></a>Identifiera och transkribera innehåll med flera språk automatiskt (förhandsgranskning)

Video Indexer stöder automatisk språkidentifiering och transkription i flerspråkigt innehåll. Denna process innebär att automatiskt identifiera det talade språket i olika segment från ljud, skicka varje segment av mediefilen som ska transkriberas och kombinera transkriptionen tillbaka till en enhetlig transkription. 

## <a name="choosing-multilingual-identification-on-indexing-with-portal"></a>Välja flerspråkig identifiering vid indexering med portal

Du kan välja **flerspråksidentifiering** när du laddar upp och indexerar videon. Du kan också välja **flerspråkig identifiering** när du indexerar om videon. I följande steg beskrivs hur du indexerar om:

1. Gå till [Video Indexer](https://vi.microsoft.com/)-webbplatsen och logga in.
1. Gå till sidan **Bibliotek** och hovra över namnet på videon som du vill indexera om. 
1. Klicka på **knappen Indexera om videon** längst till höger. 
1. I dialogrutan **Indexera om video** väljer du **flerspråksidentifiering** i listrutan **Videokällspråk.**

    * När en video indexeras som flera språk, kommer insiktssidan att innehålla det alternativet, och en ytterligare insiktstyp visas, vilket gör det möjligt för användaren att visa vilket segment som transkriberas på vilket språk "Talat språk".
    * Översättning till alla språk är fullt tillgänglig från multispråksavskriften.
    * Alla andra insikter visas på huvudspråket som upptäckts – det är det språk som visades mest i ljudet.
    * Dold textning på spelaren finns även på flera språk.

![Portalmiljö](./media/multi-language-identification-transcription/portal-experience.png)

## <a name="choosing-multilingual-identification-on-indexing-with-api"></a>Välja flerspråkig identifiering vid indexering med API

När du indexerar eller [indexerar](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?) om `multi-language detection` en `sourceLanguage` video med API:et väljer du alternativet i parametern.

### <a name="model-output"></a>Modell-utdata

Modellen hämtar alla språk som upptäcks i videon i en lista

```json
"sourceLanguage": null,
"sourceLanguages": [
    "es-ES",
    "en-US"
],
```

Dessutom kommer varje instans i transkriptionsavsnittet att innehålla det språk på vilket det transkriberades

```json
{
  "id": 136,
  "text": "I remember well when my youth Minister took me to hear Doctor King I was a teenager.",
  "confidence": 0.9343,
  "speakerId": 1,
  "language": "en-US",
  "instances": [
    {
       "adjustedStart": "0:21:10.42",
       "adjustedEnd": "0:21:17.48",
       "start": "0:21:10.42",
       "end": "0:21:17.48"
    }
  ]
},
```

## <a name="guidelines-and-limitations"></a>Riktlinjer och begränsningar

* Uppsättning språk som stöds: engelska, franska, tyska, spanska.
* Stöd för flerspråkigt innehåll med upp till tre språk som stöds.
* Om ljudet innehåller andra språk än listan ovan som stöds är resultatet oväntat.
* Minimal segmentlängd att upptäcka för varje språk – 15 sekunder.
* Förskjutningen av språkidentifiering är i genomsnitt 3 sekunder.
* Talet förväntas vara kontinuerligt. Frekventa växlingar mellan språk kan påverka modellernas prestanda.
* Tal av icke-infödda talare kan påverka modellens prestanda (till exempel när talare använder sitt modersmål och de byter till ett annat språk).
* Modellen är utformad för att känna igen ett spontant samtalstal med rimlig ljudakustik (inte röstkommandon, sång, etc.).
* Projektskapande och redigering är för närvarande inte tillgängligt för videor med flera språk.
* Anpassade språkmodeller är inte tillgängliga när du använder flerspråkig identifiering.
* Det går inte att lägga till nyckelord.
* När du exporterar filer med dold textning visas inte språkindikeringen.
* Api:et för uppdateringsavskrift stöder inte flera språkfiler.

## <a name="next-steps"></a>Nästa steg

[Översikt över Video Indexer](video-indexer-overview.md)
