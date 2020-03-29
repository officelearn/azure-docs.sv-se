---
title: Utvärdera noggrannheten för tjänsten Custom Speech - Tal
titleSuffix: Azure Cognitive Services
description: I det här dokumentet får du lära dig hur du kvantitativt mäter kvaliteten på vår tal-till-text-modell eller din anpassade modell. Ljud + human-märkt transkription data krävs för att testa noggrannhet, och 30 minuter till 5 timmar av representativt ljud bör tillhandahållas.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: f710b8bfdd4dcfd3b7a63aa0b457036ab7037016
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "74806104"
---
# <a name="evaluate-custom-speech-accuracy"></a>Utvärdera anpassad talnoggrannhet

I det här dokumentet får du lära dig hur du kvantitativt mäter kvaliteten på Microsofts tal-till-text-modell eller din anpassade modell. Ljud + human-märkt transkription data krävs för att testa noggrannhet, och 30 minuter till 5 timmar av representativt ljud bör tillhandahållas.

## <a name="what-is-word-error-rate-wer"></a>Vad är Word Felfrekvens (WER)?

Branschstandarden för att mäta modellens noggrannhet är *Word Error Rate* (WER). WER räknar antalet felaktiga ord som identifierats under erkännandet och dividerar sedan med det totala antalet ord som anges i den mänskliga avskriften. Slutligen multipliceras detta antal med 100 % för att beräkna WER.

![WER-formel](./media/custom-speech/custom-speech-wer-formula.png)

Felaktigt identifierade ord kan delas in i tre kategorier:

* Insättning (I): Ord som läggs till felaktigt i hypotesavskriften
* Borttagning (D): Ord som inte har upptäckts i hypotesavskriften
* Substitution (S): Ord som ersattes mellan referens och hypotes

Här är ett exempel:

![Exempel på felaktigt identifierade ord](./media/custom-speech/custom-speech-dis-words.png)

## <a name="resolve-errors-and-improve-wer"></a>Lös fel och förbättra WER

Du kan använda WER från resultaten för maskinigenkänning för att utvärdera kvaliteten på den modell du använder med din app, ditt verktyg eller din produkt. En WER på 5%-10% anses vara god kvalitet och är klar att använda. En WER på 20% är acceptabelt, men du kanske vill överväga ytterligare utbildning. En WER på 30% eller mer signalerar dålig kvalitet och kräver anpassning och utbildning.

Hur felen fördelas är viktigt. När många borttagningsfel påträffas beror det oftast på svag ljudsignalstyrka. För att lösa problemet måste du samla in ljuddata närmare källan. Insättningsfel innebär att ljudet spelades in i en bullrig miljö och överhörning kan förekomma, vilket orsakar igenkänningsproblem. Substitutionsfel uppstår ofta när ett otillräckligt urval av domänspecifika termer har angetts som antingen mänskligt märkta transkriptioner eller relaterad text.

Genom att analysera enskilda filer kan du bestämma vilken typ av fel som finns och vilka fel som är unika för en viss fil. Genom att förstå problem på filnivå kan du inrikta dig på förbättringar.

## <a name="create-a-test"></a>Skapa ett test

Om du vill testa kvaliteten på Microsofts baslinjemodell för tal till text eller en anpassad modell som du har tränat kan du jämföra två modeller sida vid sida för att utvärdera noggrannheten. Jämförelsen inkluderar WER och erkännanderesultat. Vanligtvis jämförs en anpassad modell med Microsofts baslinjemodell.

Så här utvärderar du modeller sida vid sida:

1. Logga in på [portalen För anpassat tal](https://speech.microsoft.com/customspeech).
2. Navigera till **tal-till-text-> anpassad > testning.**
3. Klicka på **Lägg till test**.
4. Välj **Utvärdera noggrannhet**. Ge testet ett namn, en beskrivning och välj ljud + datauppsättning med mänskligt märkt transkription.
5. Välj upp till två modeller som du vill testa.
6. Klicka på **Skapa**.

När testet har skapats kan du jämföra resultaten sida vid sida.

## <a name="side-by-side-comparison"></a>Jämförelse sida vid sida

När testet är klart, vilket indikeras av statusändringen till *Lyckades,* hittar du ett WER-nummer för båda modellerna som ingår i testet. Klicka på testnamnet för att visa testinformationssidan. På den här detaljsidan visas alla yttranden i datauppsättningen, som anger igenkänningsresultaten för de två modellerna tillsammans med transkriptionen från den inlämnade datauppsättningen. För att kontrollera jämförelsen sida vid sida kan du växla mellan olika feltyper, inklusive insättning, borttagning och ersättning. Genom att lyssna på ljud- och jämföra igenkänningsresultaten i varje kolumn, som visar den människomärkta transkriptionen och resultaten för två tal-till-text-modeller, kan du bestämma vilken modell som uppfyller dina behov och var ytterligare utbildning och förbättringar Krävs.

## <a name="next-steps"></a>Nästa steg

* [Träna din modell](how-to-custom-speech-train-model.md)
* [Distribuera din modell](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>Ytterligare resurser

* [Förbereda och testa dina data](how-to-custom-speech-test-data.md)
* [Granska dina data](how-to-custom-speech-inspect-data.md)
