---
title: Utvärdera precisionen för Custom Speech tal-tjänsten
titleSuffix: Azure Cognitive Services
description: I det här dokumentet får du lära dig hur du kvantitativt mäter kvaliteten på vår tal-till-text-modell eller din anpassade modell. Ljud + mänskligt avskrifts data krävs för att testa precisionen och 30 minuter till 5 timmar representativt ljud bör tillhandahållas.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: 27229d36dbe9592b6ad6fc2740f7714f99fd6857
ms.sourcegitcommit: d7fba095266e2fb5ad8776bffe97921a57832e23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/09/2020
ms.locfileid: "84629060"
---
# <a name="evaluate-custom-speech-accuracy"></a>Utvärdera noggrannhet i Custom Speech

I det här dokumentet får du lära dig hur du kvantitativt mäter kvaliteten på Microsofts tal-till-text-modell eller din anpassade modell. Ljud + mänskligt avskrifts data krävs för att testa precisionen och 30 minuter till 5 timmar representativt ljud bör tillhandahållas.

## <a name="what-is-word-error-rate-wer"></a>Vad är en fel frekvens i Word (WER)?

Bransch standarden för att mäta modell precisionen är en *ord Fels frekvens* (WER). WER räknar antalet felaktiga ord som identifierats under igenkänning och dividerar sedan med det totala antalet ord som anges i den medskriften med mänsklig märkning. Slutligen multipliceras talet med 100% för att beräkna WER.

![WER-formel](./media/custom-speech/custom-speech-wer-formula.png)

Felaktigt identifierade ord hamnar i tre kategorier:

* Infogning (I): ord som har lagts till felaktigt i hypotes avskrift
* Borttagning (D): ord som inte kan identifieras i hypotes avskriften
* Ersättning (er): ord som ersatts mellan referens och hypotes

Här är ett exempel:

![Exempel på felaktigt identifierade ord](./media/custom-speech/custom-speech-dis-words.png)

## <a name="resolve-errors-and-improve-wer"></a>Lös fel och förbättra WER

Du kan använda WER från dator igenkännings resultat för att utvärdera kvaliteten på den modell som du använder med din app, ditt verktyg eller din produkt. En WER med 5%-10% anses vara av bra kvalitet och är redo att användas. En WER med 20% är acceptabelt, men du kanske vill överväga ytterligare utbildning. En WER av 30% eller mer signalerar dålig kvalitet och kräver anpassning och utbildning.

Hur felen distribueras är viktigt. När många borttagnings fel påträffas beror det vanligt vis på svag ljud Signals styrka. För att lösa det här problemet måste du samla in ljud data närmare källan. Infognings fel innebär att ljudet registrerades i en miljö med störningar och att Crosstalk kan finnas, vilket orsakar igenkännings problem. Ersättnings fel uppstår ofta när ett otillräckligt exempel på domänautentiseringsuppgifter har angetts som antingen mänskligt avskrifter eller relaterad text.

Genom att analysera enskilda filer kan du bestämma vilken typ av fel som finns och vilka fel som är unika för en viss fil. Att förstå problem på filnivå hjälper dig att fokusera på förbättringar.

## <a name="create-a-test"></a>Skapa ett test

Om du vill testa kvaliteten på Microsofts bas linje modell för tal till text eller en anpassad modell som du har tränat, kan du jämföra två modeller sida vid sida för att utvärdera noggrannheten. Jämförelsen innehåller WER-och igenkännings resultat. Normalt jämförs en anpassad modell med Microsofts bas linje modell.

Utvärdera modeller sida vid sida:

1. Logga in på [Custom Speech Portal](https://speech.microsoft.com/customspeech).
2. Navigera till **> för tal till text Custom Speech > [namn på projektet] > testning**.
3. Klicka på **Lägg till test**.
4. Välj **utvärdera noggrannhet**. Ge testet ett namn, en beskrivning och välj din ljud-eller data uppsättning med mänskligt märkt skrift.
5. Välj upp till två modeller som du vill testa.
6. Klicka på **Skapa**.

När testet har skapats kan du jämföra resultatet sida vid sida.

## <a name="side-by-side-comparison"></a>Jämförelse sida vid sida

När testet har slutförts, som anges av status ändringen till *lyckades*, hittar du ett WER-nummer för båda modellerna i testet. Klicka på test namnet för att Visa test informations sidan. Den här informations sidan visar en lista över alla yttranden i din data uppsättning, vilket anger tolknings resultatet för de två modellerna jämte avskriften från den skickade data mängden. För att kunna kontrol lera jämförelsen sida vid sida kan du växla mellan olika typer av fel, inklusive infogning, borttagning och ersättning. Genom att lyssna på ljudet och jämföra igenkännings resultaten i varje kolumn, som visar den mänskligt avskriften och resultaten för två tal till text-modeller, kan du bestämma vilken modell som uppfyller dina behov och var ytterligare utbildning och förbättringar krävs.

## <a name="next-steps"></a>Nästa steg

* [Träna din modell](how-to-custom-speech-train-model.md)
* [Distribuera din modell](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>Ytterligare resurser

* [Förbered och testa dina data](how-to-custom-speech-test-data.md)
* [Inspektera dina data](how-to-custom-speech-inspect-data.md)
