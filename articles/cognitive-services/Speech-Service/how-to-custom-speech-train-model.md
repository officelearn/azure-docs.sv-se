---
title: Träna en modell för tjänsten Custom Speech tal
titleSuffix: Azure Cognitive Services
description: Träna en tal-till-text-modell kan förbättra igenkännings precisionen för Microsofts bas linje modell eller en anpassad modell. En modell tränas med hjälp av välmärkta avskrifter och relaterad text.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: 09867d6739998902ea0fd11281d1b975866c1242
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2020
ms.locfileid: "83722634"
---
# <a name="train-a-model-for-custom-speech"></a>Träna en modell för Custom Speech

Träna en tal-till-text-modell kan förbättra igenkännings precisionen för Microsofts bas linje modell. En modell tränas med hjälp av välmärkta avskrifter och relaterad text. Dessa data uppsättningar tillsammans med tidigare överförda ljud data används för att förfina och träna tal-till-text-modellen att identifiera ord, fraser, akronymer, namn och andra produktspecifika villkor. Fler data uppsättningar i domänen som du anger (data som är relaterade till vad användarna säger och vad du förväntar dig att identifiera), desto mer exakta är din modell, vilket leder till förbättrad igenkänning. Kom ihåg att genom att mata in orelaterade data i din utbildning kan du minska eller försämra precisionen i din modell.

## <a name="use-training-to-resolve-accuracy-issues"></a>Använd utbildning för att lösa problem med problemlösning

Om du stöter på igenkännings problem med din modell kan du förbättra precisionen med hjälp av välmärkta avskrifter och relaterade data för ytterligare utbildning. Använd den här tabellen för att avgöra vilken data mängd som ska användas för att åtgärda dina problem:

| Användningsfall | Datatyp |
| -------- | --------- |
| Förbättra igenkännings precisionen för branschspecifika vokabulär och grammatik, till exempel medicinsk terminologi eller IT-jargong. | Relaterad text (meningar/yttranden) |
| Definiera den fonetiska och visade formen av ett ord eller en term som innehåller uttal som inte är standard, t. ex. produkt namn eller akronymer. | Relaterad text (uttal) |
| Förbättra igenkännings precisionen för tal format, accenttecken eller vissa bakgrunds brus. | Ljud + medmärkta avskrifter |

> [!IMPORTANT]
> Om du inte har överfört en data uppsättning kan du läsa [förbereda och testa dina data](how-to-custom-speech-test-data.md). Det här dokumentet innehåller instruktioner för att ladda upp data och rikt linjer för att skapa data uppsättningar med hög kvalitet.

## <a name="train-and-evaluate-a-model"></a>Träna och utvärdera en modell

Det första steget för att träna en modell är att överföra tränings data. Använd [förbereda och testa dina data](how-to-custom-speech-test-data.md) för steg-för-steg-instruktioner för att förbereda medmärkta avskrifter och relaterad text (yttranden och uttal). När du har överfört tränings data följer du de här anvisningarna för att börja träna din modell:

1. Logga in på [Custom Speech Portal](https://speech.microsoft.com/customspeech).
2. Navigera till **> Custom Speech > utbildning för tal till text**.
3. Klicka på **träna modell**.
4. Ge sedan din utbildning ett **namn** och en **Beskrivning**.
5. Välj det scenario som passar din domän bäst på den nedrullningsbara menyn för **scenario och bas linje modell** . Om du är osäker på vilket scenario du väljer väljer du **Allmänt**. Bas linje modellen är start punkten för utbildning. Den senaste modellen är vanligt vis det bästa valet.
6. På sidan **Välj tränings data** väljer du en eller flera ljud-och data uppsättningar med mänsklig etikett som du vill använda för utbildning.
7. När utbildningen är klar kan du välja att utföra testning av precisionen för den nytränade modellen. Det här steget är valfritt.
8. Välj **skapa** för att skapa din anpassade modell.

I övnings tabellen visas en ny post som motsvarar den nyligen skapade modellen. Tabellen visar också status: bearbetning, lyckades, misslyckades.

## <a name="evaluate-the-accuracy-of-a-trained-model"></a>Utvärdera precisionen för en utbildad modell

Du kan granska data och utvärdera modell precisionen med hjälp av dessa dokument:

- [Inspektera dina data](how-to-custom-speech-inspect-data.md)
- [Utvärdera dina data](how-to-custom-speech-evaluate-data.md)

Om du väljer att testa noggrannhet är det viktigt att välja en akustisk data uppsättning som skiljer sig från den som du använde med din modell för att få en realistisk känsla för modellens prestanda.

## <a name="next-steps"></a>Nästa steg

- [Distribuera din modell](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>Ytterligare resurser

- [Förbered och testa dina data](how-to-custom-speech-test-data.md)
- [Inspektera dina data](how-to-custom-speech-inspect-data.md)
- [Utvärdera dina data](how-to-custom-speech-evaluate-data.md)
- [Träna din modell](how-to-custom-speech-train-model.md)
