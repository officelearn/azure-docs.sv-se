---
title: 'Vad är uppslukande läsare API: et?'
titleSuffix: Azure Cognitive Services
description: Läs mer om uppslukande läsaren API.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: overview
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: 4500b6213c549ab9977fe8f2d849ffa8089d04b9
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2019
ms.locfileid: "67718430"
---
# <a name="what-is-immersive-reader"></a>Vad är Avancerad läsare?

Den [uppslukande läsare](https://www.onenote.com/learningtools) är ett portintervallet utformade verktyg som implementerar beprövade metoder för att förbättra läsbarheten för kommande läsare, språk-inlärning och personer med learning skillnader, till exempel dyslexi.

Du kan använda Avancerad läsare i ditt webbprogram genom att använda SDK:t för Avancerad läsare.

## <a name="what-does-immersive-reader-do"></a>Vad gör uppslukande läsare?

Uppslukande läsaren är utformad för att göra läser mer tillgängligt för alla.

* Visar innehållet i en minimal läsvyn

  ![Avancerad läsare](./media/immersive-reader.png)

* Visar bilder av vanliga ord

  ![Bild-ordlista](./media/picture-dictionary.png)

* Höjdpunkter substantiv, verb, adjektiv och adverb

  ![Delar av tal](./media/parts-of-speech.png)

* Läser upp ditt innehåll till dig

  ![Läsa upp](./media/read-aloud.png)

* Översätter ditt innehåll till ett annat språk

  ![Översättning](./media/translation.png)

* Delar upp ord i stavelser

  ![Syllabification](./media/syllabification.png)

## <a name="how-does-immersive-reader-work"></a>Hur fungerar den uppslukande läsare?

Uppslukande läsaren är en fristående webbapp som, när anropas med hjälp av uppslukande läsare JavaScript SDK, visas ovanpå ditt befintliga webbprogram via en `iframe`. När du anropar API för att starta uppslukande läsaren, anger du det innehåll som du vill visa i uppslukande läsaren. Våra SDK: N hanterar skapandet och formatering av den `iframe` och kommunikation med serverdelstjänsten uppslukande läsare som bearbetar innehållet för delar av tal, text till tal, översättning och så vidare.

## <a name="next-steps"></a>Nästa steg

Kom igång med Avancerad läsare:

* Gå vidare till den [Snabbstart](./quickstart.md)
* Utforska den [uppslukande läsare SDK på GitHub](https://github.com/Microsoft/immersive-reader-sdk)
* Läs den [uppslukande läsare SDK-referens](./reference.md)