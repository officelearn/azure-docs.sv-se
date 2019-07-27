---
title: Granska data kvaliteten för Custom Speech tal-tjänsten
titleSuffix: Azure Cognitive Services
description: Custom Speech innehåller verktyg som gör det möjligt att visuellt inspektera igenkännings kvaliteten för en modell genom att jämföra ljuddata med motsvarande igenkännings resultat. Från Custom Speech-portalen kan du spela upp ljud som laddats upp och avgöra om det angivna igenkännings resultatet är korrekt.  Med det här verktyget kan du snabbt kontrol lera kvaliteten på vår bas linje för tal-till-text-modellen eller en utbildad anpassad modell utan att behöva skriva om ljud data.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: b58f9c17995128091b5c4badd228356dbacc6ae9
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562845"
---
# <a name="inspect-custom-speech-data"></a>Granska Custom Speech data

> [!NOTE]
> På den här sidan förutsätter vi att du har läst [förbereda test data för Custom Speech](how-to-custom-speech-test-data.md) och har laddat upp en data uppsättning för inspektion.

Custom Speech innehåller verktyg som gör det möjligt att visuellt inspektera igenkännings kvaliteten för en modell genom att jämföra ljuddata med motsvarande igenkännings resultat. Från Custom Speech-portalen kan du spela upp ljud som laddats upp och avgöra om det angivna igenkännings resultatet är korrekt. Med det här verktyget kan du snabbt kontrol lera kvaliteten på Microsofts bas linje för tal-till-text-modellen eller en utbildad anpassad modell utan att behöva skriva om ljud data.

I det här dokumentet får du lära dig att visuellt kontrol lera kvaliteten på en modell med hjälp av de tränings data som du tidigare har laddat upp.

På den här sidan får du lära dig att visuellt kontrol lera kvaliteten på Microsofts bas linje tal till text-modell och/eller en anpassad modell som du har tränat. Du använder de data som du överförde till fliken **data** för att testa.

## <a name="create-a-test"></a>Skapa ett test

Följ de här anvisningarna för att skapa ett test:

1. Navigera till **> för tal till text Custom Speech > testning**.
2. Klicka på **Lägg till test**.
3. Välj **inspektera kvalitet (endast ljuddata)** . Ge testet ett namn, en beskrivning och välj din ljud data uppsättning.
4. Välj upp till två modeller som du vill testa.
5. Klicka på **Skapa**.

När du har skapat ett test kan du jämföra modellerna sida vid sida.

## <a name="side-by-side-model-comparisons"></a>Jämförelser av modeller sida vid sida

När test status är *klar*klickar du på test objekt namnet för att se information om testet. Den här informations sidan visar en lista över alla yttranden i din data uppsättning, vilket anger tolknings resultatet för de två modellerna jämte avskriften från den skickade data mängden.

För att kunna kontrol lera jämförelsen sida vid sida kan du växla mellan olika typer av fel, inklusive infogning, borttagning och ersättning. Genom att lyssna på ljudet och jämföra igenkännings resultaten i varje kolumn (med mänsklig avskrift och resultatet av två tal till text-modeller) kan du bestämma vilken modell som uppfyller dina behov och var förbättringar behövs.

Att kontrol lera kvalitets testning är användbart för att verifiera om kvaliteten på en slut punkt för tal igenkänning är tillräckligt för ett program.  Om du vill ha ett objektivt mått på precision, som kräver uppmätta ljud, följer du anvisningarna i [utvärdera noggrannhet](how-to-custom-speech-evaluate-data.md).

## <a name="next-steps"></a>Nästa steg

* [Utvärdera dina data](how-to-custom-speech-evaluate-data.md)
* [Träna din modell](how-to-custom-speech-train-model.md)
* [Distribuera din modell](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>Ytterligare resurser

* [Förbered test data för Custom Speech](how-to-custom-speech-test-data.md)
