---
title: Utvärdera noggrannhet för anpassat tal – Speech Services
titlesuffix: Azure Cognitive Services
description: I det här dokumentet lär du dig att mäta kvantitativt kvaliteten på Microsofts tal till text modell eller din anpassade modell. Ljud + mänskliga etikett avskrift data krävs för att testa Precision och 5 timmar med representativa ljud måste anges.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 7c1b3602b09c1a129923180c4b1d4a5f8293de2c
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025915"
---
# <a name="evaluate-custom-speech-accuracy"></a>Utvärdera noggrannhet för anpassat tal

I det här dokumentet lär du dig att mäta kvantitativt kvaliteten på Microsofts tal till text modell eller din anpassade modell. Ljud + mänskliga etikett avskrift data krävs för att testa Precision och 5 timmar med representativa ljud måste anges.

## <a name="what-is-word-error-rate-wer"></a>Vad är priset för Word-fel (WER)?

Branschstandard att mäta modellens Precision är *Word Felfrekvens* (WER). WER räknar antalet felaktiga ord som identifieras vid erkännande och dividerar med det totala antalet ord i avskriften mänskliga etikett. Slutligen multipliceras det talet med 100% för att beräkna WER.

![WER formel](./media/custom-speech/custom-speech-wer-formula.png)

Felaktigt identifierade ord som kan delas in i tre kategorier:

* Infogning (I): Ord som felaktigt lagts till i hypotesen avskrift
* Borttagning (D): Ord som är oidentifierade i avskriften hypotesen
* Ersättningen (S): Ord som har använts istället mellan referens och hypotesen

Här är ett exempel:

![Exempel på felaktigt identifierade ord](./media/custom-speech/custom-speech-dis-words.png)

## <a name="resolve-errors-and-improve-wer"></a>Lös problem och förbättra WER

Du kan använda WER från igenkänningsresultat datorn för att utvärdera kvaliteten på modellen som du använder med din app, ett verktyg eller en produkt. WER av 5-10% anses vara god kvalitet och är klart att användas. En WER 20% är acceptabla, men du kanske vill överväga ytterligare utbildning. En WER på 30% eller mer signalerar dålig kvalitet och som kräver anpassning och utbildning.

Det är viktigt att hur felen distribueras. När många fel vid borttagning av uppstår är det vanligtvis på grund av svaga ljud signalstyrka. För att lösa problemet måste du samla in ljuddata närmare till källan. Infogning fel innebär att ljudet registrerades i en miljö med bort störande och samtidigt tal kan finnas, orsakar igenkänning av problem. Ersättningen ofta fel när en otillräcklig exempel på domänspecifika villkor har angetts som antingen mänskliga etikett avskrifter eller relaterade text.

Genom att analysera enskilda filer kan bestämma du vilken typ av fel finns och vilka fel som är unika för en specifik fil. Förstå problem på filnivå hjälper dig att rikta förbättringar.

## <a name="create-a-test"></a>Skapa ett test

Om du vill testa kvaliteten på Microsofts tal till text baslinje modell eller en anpassad modell som du har tränat kan jämföra du två modeller sida vid sida för att utvärdera precision. Jämförelsen inkluderar WER och igenkänning av resultat. Vanligtvis jämförs en anpassad modell med Microsofts baslinje-modellen.

Utvärdera modeller sida vid sida:

1. Gå till **tal till text > anpassat tal > Testa**.
2. Klicka på **Lägg till Test**.
3. Välj **utvärdera Precision**. Ge testet ett namn, beskrivning, och välj din ljud + mänskliga etikett avskrift datauppsättning.
4. Välj upp till två modeller som du vill testa.
5. Klicka på **Skapa**.

När testet har skapats, kan du jämföra resultaten sida vid sida.

## <a name="side-by-side-comparison"></a>Sida-vid-sida-jämförelse

När testet är klart, anges genom att ändra status till *lyckades*, hittar du ett WER-nummer för båda modellerna som ingår i testet. Klicka på test-namnet för att visa sidan testning. Den här sidan listar alla uttryck i din datauppsättning som anger igenkänningsresultat av de två modellerna tillsammans med transkription från den överförda datamängden. För att inspektera sida-vid-sida-jämförelse, kan du växla olika feltyper inklusive infogning och borttagning av ersättningen. Genom att lyssna på ljudet och jämföra resultat i varje kolumn som visar den mänskliga etikett avskrift och resultaten för två tal till text-modeller kan bestämma du vilken modell som passar dina behov och där ytterligare utbildning och förbättringar är krävs.

## <a name="next-steps"></a>Nästa steg

* [Träna din modell](how-to-custom-speech-train-model.md)
* [Distribuera din modell](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>Ytterligare resurser

* [Förbereda och testa dina data](how-to-custom-speech-test-data.md)
* [Granska dina data](how-to-custom-speech-inspect-data.md)
