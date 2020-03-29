---
title: Kontrollera datakvaliteten för tjänsten Custom Speech - Tal
titleSuffix: Azure Cognitive Services
description: Custom Speech innehåller verktyg som gör att du kan visuellt kontrollera en modells igenkänningskvalitet genom att jämföra ljuddata med motsvarande igenkänningsresultat. Du kan spela upp uppladdat ljud och avgöra om det angivna igenkänningsresultatet är korrekt.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: erhopf
ms.openlocfilehash: 60b415f69800885f37ee53cc4f090098f5ebf1f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "74806087"
---
# <a name="inspect-custom-speech-data"></a>Granska anpassade taldata

> [!NOTE]
> Den här sidan förutsätter att du har läst [Förbered testdata för anpassat tal](how-to-custom-speech-test-data.md) och har laddat upp en datauppsättning för inspektion.

Custom Speech innehåller verktyg som gör att du kan visuellt kontrollera en modells igenkänningskvalitet genom att jämföra ljuddata med motsvarande igenkänningsresultat. Från [portalen Anpassat tal](https://speech.microsoft.com/customspeech)kan du spela upp uppladdat ljud och avgöra om det angivna igenkänningsresultatet är korrekt. Med det här verktyget kan du snabbt kontrollera kvaliteten på Microsofts tal-till-text-modell vid baslinjen eller en utbildad anpassad modell utan att behöva transkribera några ljuddata.

I det här dokumentet får du lära dig hur du visuellt inspekterar kvaliteten på en modell med hjälp av de träningsdata som du tidigare har laddat upp.

På den här sidan får du lära dig hur du visuellt kan kontrollera kvaliteten på Microsofts modell för tal-till-text från baslinje och/eller en anpassad modell som du har tränat. Du ska använda de data som du har laddat upp till fliken **Data** för testning.

## <a name="create-a-test"></a>Skapa ett test

Följ dessa instruktioner för att skapa ett test:

1. Logga in på [portalen För anpassat tal](https://speech.microsoft.com/customspeech).
2. Navigera till **tal-till-text-> anpassad > testning.**
3. Klicka på **Lägg till test**.
4. Välj **Kontrollera kvalitet (endast ljuddata)**. Ge testet ett namn, en beskrivning och välj ljuddatauppsättning.
5. Välj upp till två modeller som du vill testa.
6. Klicka på **Skapa**.

När ett test har skapats kan du jämföra modellerna sida vid sida.

[!INCLUDE [service-pricing-advisory](includes/service-pricing-advisory.md)]

## <a name="side-by-side-model-comparisons"></a>Jämförelser av modell sida vid sida

När teststatusen _har slutförts_klickar du i testobjektnamnet för att se information om testet. På den här detaljsidan visas alla yttranden i datauppsättningen, som anger igenkänningsresultaten för de två modellerna tillsammans med transkriptionen från den inlämnade datauppsättningen.

För att kontrollera jämförelsen sida vid sida kan du växla mellan olika feltyper, inklusive insättning, borttagning och ersättning. Genom att lyssna på ljud- och jämföra igenkänningsresultaten i varje kolumn (som visar mänsklig transkription och resultaten av två tal-till-text-modeller) kan du bestämma vilken modell som uppfyller dina behov och var förbättringar behövs.

Att inspektera kvalitetstestning är användbart för att verifiera om kvaliteten på en slutpunkt för taligenkänning är tillräcklig för ett program. För ett objektivt mått på noggrannhet, som kräver transkriberat ljud, följ instruktionerna som finns i [Utvärdera noggrannhet](how-to-custom-speech-evaluate-data.md).

## <a name="next-steps"></a>Nästa steg

- [Utvärdera dina data](how-to-custom-speech-evaluate-data.md)
- [Träna din modell](how-to-custom-speech-train-model.md)
- [Distribuera din modell](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>Ytterligare resurser

- [Förbereda testdata för anpassat tal](how-to-custom-speech-test-data.md)
