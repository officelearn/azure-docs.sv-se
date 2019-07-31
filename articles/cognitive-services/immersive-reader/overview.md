---
title: Vad är API för avancerad läsare?
titleSuffix: Azure Cognitive Services
description: 'Läs mer om API: t för avancerad läsare.'
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: overview
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: f84804fbad07357706e786fc449fce9e42a5ec4a
ms.sourcegitcommit: d585cdda2afcf729ed943cfd170b0b361e615fae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2019
ms.locfileid: "68688338"
---
# <a name="what-is-immersive-reader"></a>Vad är Avancerad läsare?

Den [fördjupade läsaren](https://www.onenote.com/learningtools) är ett särskilt utformat verktyg som implementerar beprövade tekniker för att förbättra Läs förståelse för nya läsare, språkutbildningar och personer med inlärnings skillnader som Dyslexia.

Du kan använda Avancerad läsare i ditt webbprogram genom att använda SDK:t för Avancerad läsare.

## <a name="what-does-immersive-reader-do"></a>Vad gör avancerad läsare?

Den fördjupade läsaren är utformad för att läsa mer tillgänglig för alla.

* Visar innehåll i minimal Läsvy

  ![Avancerad läsare](./media/immersive-reader.png)

* Visar bilder av vanliga ord

  ![Bild ord lista](./media/picture-dictionary.png)

* Markerar substantiv, verb, adjektiv och adverb

  ![Delar av tal](./media/parts-of-speech.png)

* Läser upp ditt innehåll till dig

  ![Läs upp](./media/read-aloud.png)

* Översätter ditt innehåll till ett annat språk

  ![Översättning](./media/translation.png)

* Delar upp ord i stavelser

  ![Syllabification](./media/syllabification.png)

## <a name="how-does-immersive-reader-work"></a>Hur fungerar den fördjupade läsaren?

Den fördjupade läsaren är en fristående webbapp som, när den anropas med avancerad läsares JavaScript SDK, visas ovanpå din befintliga webbapp via en `iframe`. När du anropar API: et för att starta den fördjupade läsaren anger du det innehåll som du vill visa i den fördjupade läsaren. Vår SDK hanterar skapandet och utformningen av `iframe` och kommunikationen med Server dels tjänsten för avancerad läsare, som bearbetar innehållet för delar av tal, text till tal, översättning och så vidare.

## <a name="next-steps"></a>Nästa steg

Kom igång med Avancerad läsare:

* Hoppa till [snabb](./quickstart.md) starten
* Utforska [SDK för avancerad läsare på GitHub](https://github.com/microsoft/immersive-reader-sdk)
* Läs SDK-referensen för [Avancerad läsare](./reference.md)