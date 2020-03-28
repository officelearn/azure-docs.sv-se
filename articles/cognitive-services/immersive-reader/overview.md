---
title: Vad är Immersive Reader API?
titleSuffix: Azure Cognitive Services
description: Immersive Reader API är ett verktyg som kan användas för att rymma personer med inlärningsskillnader eller hjälpa nya läsare och språkstuderande.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: overview
ms.date: 01/4/2020
ms.author: metan
ms.openlocfilehash: 2eab7ab62f6a7a105dab33aa889af9f2be3a3fa3
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80052388"
---
# <a name="what-is-immersive-reader"></a>Vad är Avancerad läsare?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

[Den uppslukande Läsaren](https://www.onenote.com/learningtools) är ett inkluderande utformat verktyg som implementerar beprövade tekniker för att förbättra läsförståelsen för nya läsare, språkstuderande och personer med inlärningsskillnader som dyslexi.

Du kan använda Avancerad läsare i ditt webbprogram genom att använda SDK:t för Avancerad läsare.

## <a name="what-does-immersive-reader-do"></a>Vad gör Immersive Reader?

Den uppslukande läsaren är utformad för att göra läsningen mer tillgänglig för alla.

* Visar innehåll i en minimal läsvy

  ![Avancerad läsare](./media/immersive-reader.png)

* Visar bilder på vanliga ord

  ![Bildordlista](./media/picture-dictionary.png)

* Markerar substantiv, verb, adjektiv och adverb

  ![Delar av tal](./media/parts-of-speech.png)

* Läser ditt innehåll högt för dig

  ![Läs högt](./media/read-aloud.png)

* Översätter ditt innehåll till ett annat språk

  ![Översättning](./media/translation.png)

* Dela upp ord i stavelser

  ![Kursplaner](./media/syllabification.png)

## <a name="how-does-immersive-reader-work"></a>Hur fungerar Immersive Reader?

Den uppslukande läsaren är en fristående webbapp som, när den anropas med hjälp av Immersive Reader JavaScript `iframe`SDK, visas ovanpå din befintliga webbapp via en . När du anropar API:et för att starta Immersive Reader anger du det innehåll du vill visa i Immersive Reader. Vår SDK hanterar skapandet och `iframe` stylingen av och kommunikationen med Immersive Reader-backend-tjänsten, som bearbetar innehållet för delar av tal, text till tal, översättning och så vidare.

## <a name="next-steps"></a>Nästa steg

Kom igång med Avancerad läsare:

* Hoppa in i [snabbstarten](./quickstart.md)
* Utforska [Immersive Reader SDK på GitHub](https://github.com/microsoft/immersive-reader-sdk)
* Läs [Immersive Reader SDK-referensen](./reference.md)
