---
title: Granska data kvaliteten för Custom Speech tal-tjänsten
titleSuffix: Azure Cognitive Services
description: Custom Speech innehåller verktyg som gör det möjligt att visuellt inspektera igenkännings kvaliteten för en modell genom att jämföra ljuddata med motsvarande igenkännings resultat. Du kan spela upp ljud som laddats upp och avgöra om det angivna igenkännings resultatet är korrekt.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: erhopf
ms.openlocfilehash: 7a8fec876556d943d29756a38ffc27ae8095e3c4
ms.sourcegitcommit: 813f7126ed140a0dff7658553a80b266249d302f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/06/2020
ms.locfileid: "84466246"
---
# <a name="inspect-custom-speech-data"></a>Inspektera Custom Speech-data

> [!NOTE]
> På den här sidan förutsätter vi att du har läst [förbereda test data för Custom Speech](how-to-custom-speech-test-data.md) och har laddat upp en data uppsättning för inspektion.

Custom Speech innehåller verktyg som gör det möjligt att visuellt inspektera igenkännings kvaliteten för en modell genom att jämföra ljuddata med motsvarande igenkännings resultat. Från [Custom Speech-portalen](https://speech.microsoft.com/customspeech)kan du spela upp ljud som laddats upp och avgöra om det angivna igenkännings resultatet är korrekt. Med det här verktyget kan du kontrol lera kvaliteten på Microsofts grundläggande tal-till-text-modell, inspektera en utbildad anpassad modell eller jämföra avskrifter med två modeller.

I det här dokumentet får du lära dig att visuellt kontrol lera kvaliteten på en modell med hjälp av de tränings data som du tidigare har laddat upp.

På den här sidan får du lära dig att visuellt kontrol lera kvaliteten på Microsofts bas linje tal till text-modell och/eller en anpassad modell som du har tränat. Du använder de data som du överförde till fliken **data** för att testa.

## <a name="create-a-test"></a>Skapa ett test

Följ de här anvisningarna för att skapa ett test:

1. Logga in på [Custom Speech Portal](https://speech.microsoft.com/customspeech).
2. Navigera till **> för tal till text Custom Speech > testning**.
3. Klicka på **Lägg till test**.
4. Välj **inspektera kvalitet (endast ljuddata)**. Ge testet ett namn, en beskrivning och välj din ljud data uppsättning.
5. Välj upp till två modeller som du vill testa.
6. Klicka på **Skapa**.

När ett test har skapats kan du se hur en modell anger den ljud data uppsättning du angav eller jämför resultat från två modeller sida vid sida.

[!INCLUDE [service-pricing-advisory](includes/service-pricing-advisory.md)]

## <a name="side-by-side-model-comparisons"></a>Jämförelser av modeller sida vid sida

När test status är _klar_klickar du på test objekt namnet för att se information om testet. Den här informations sidan visar alla yttranden i din data uppsättning och visar igenkännings resultatet för de två modeller som du jämför.

För att kunna kontrol lera jämförelsen sida vid sida kan du växla mellan olika typer av fel, inklusive infogning, borttagning och ersättning. Genom att lyssna på ljudet och jämföra igenkännings resultaten i varje kolumn (med mänsklig avskrift och resultatet av två tal till text-modeller) kan du bestämma vilken modell som uppfyller dina behov och var förbättringar behövs.

Modell testning sida vid sida är användbar för att validera vilken tal igenkännings modell som passar bäst för ett program. Om du vill ha ett objektivt mått på precision, som kräver uppmätta ljud, följer du anvisningarna i [utvärdera noggrannhet](how-to-custom-speech-evaluate-data.md).

## <a name="next-steps"></a>Nästa steg

- [Utvärdera dina data](how-to-custom-speech-evaluate-data.md)
- [Träna din modell](how-to-custom-speech-train-model.md)
- [Förbättra din modell](how-to-custom-speech-improve-accuracy.md)
- [Distribuera din modell](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>Ytterligare resurser

- [Förbered test data för Custom Speech](how-to-custom-speech-test-data.md)
