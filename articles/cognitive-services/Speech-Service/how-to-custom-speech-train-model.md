---
title: Träna en modell för anpassad tal - taltjänst
titleSuffix: Azure Cognitive Services
description: Om du tränar en tal-till-text-modell kan du förbättra igenkänningsnoggrannheten för Microsofts baslinjemodell eller en anpassad modell. En modell tränas med hjälp av mänskligt märkta transkriptioner och relaterad text.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: a2bc39a35299f56ba52a0143ce123560bd4d88fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77137773"
---
# <a name="train-a-model-for-custom-speech"></a>Träna en modell för anpassat tal

Om du tränar en tal-till-text-modell kan du förbättra igenkänningsnoggrannheten för Microsofts baslinjemodell. En modell tränas med hjälp av mänskligt märkta transkriptioner och relaterad text. Dessa datauppsättningar tillsammans med tidigare uppladdade ljuddata används för att förfina och träna tal-till-text-modellen för att känna igen ord, fraser, förkortningar, namn och andra produktspecifika termer. Ju fler datauppsättningar i domäner som du tillhandahåller (data som är relaterade till vad användarna kommer att säga och vad du förväntar dig att känna igen), desto mer exakt blir din modell, vilket resulterar i förbättrad igenkänning. Tänk på att genom att mata in orelaterade data i din träning kan du minska eller skada din modells noggrannhet.

## <a name="use-training-to-resolve-accuracy-issues"></a>Använd utbildning för att lösa noggrannhetsproblem

Om du stöter på igenkänningsproblem med din modell kan du använda avskrifter med mänskliga etiketter och relaterade data för ytterligare utbildning, vilket kan bidra till att förbättra noggrannheten. Använd den här tabellen för att avgöra vilken datauppsättning som ska användas för att lösa dina problem:

| Användningsfall | Datatyp |
| -------- | --------- |
| Förbättra igenkänningsnoggrannheten på branschspecifik vokabulär och grammatik, till exempel medicinsk terminologi eller IT-jargong. | Relaterad text (meningar/yttranden) |
| Definiera den fonetiska och visade formen av ett ord eller en term som inte är standard, till exempel produktnamn eller akronymer. | Relaterad text (uttal) |
| Förbättra igenkänningsprecisionen på talstilar, accenter eller specifika bakgrundsljud. | Ljud + mänskliga märkta avskrifter |

> [!IMPORTANT]
> Om du inte har laddat upp en datauppsättning läser du [Förbereda och testa dina data](how-to-custom-speech-test-data.md). Det här dokumentet innehåller instruktioner för att ladda upp data och riktlinjer för att skapa datauppsättningar av hög kvalitet.

## <a name="train-and-evaluate-a-model"></a>Träna och utvärdera en modell

Det första steget för att träna en modell är att ladda upp träningsdata. Använd [Förbered och testa dina data](how-to-custom-speech-test-data.md) för steg-för-steg-instruktioner för att förbereda mänskligt märkta transkriptioner och relaterad text (yttranden och uttal). När du har laddat upp träningsdata följer du de här anvisningarna för att börja träna din modell:

1. Logga in på [portalen För anpassat tal](https://speech.microsoft.com/customspeech).
2. Navigera till **tal-till-text > anpassad >-utbildning**.
3. Klicka på **Tågmodell**.
4. Ge sedan din utbildning ett **namn** och **beskrivning**.
5. Välj det scenario som bäst passar domänen på listrutan **Scenario- och baslinjemodell.** Om du är osäker på vilket scenario du ska välja väljer du **Allmänt**. Baslinjemodellen är utgångspunkten för träning. Om du inte har någon inställning kan du använda det senaste.
6. På sidan **Välj träningsdata** väljer du en eller flera ljud + datauppsättningar med mänskligt märkt transkription som du vill använda för utbildning.
7. När utbildningen är klar kan du välja att utföra noggrannhetstestning på den nyutbildade modellen. Det här steget är valfritt.
8. Välj **Skapa** för att skapa din anpassade modell.

Tabellen Utbildning visar en ny post som motsvarar den här nyskapade modellen. Tabellen visar också status: Bearbetning, lyckades, Misslyckades.

## <a name="evaluate-the-accuracy-of-a-trained-model"></a>Utvärdera noggrannheten hos en utbildad modell

Du kan granska data och utvärdera modellens noggrannhet med hjälp av dessa dokument:

- [Granska dina data](how-to-custom-speech-inspect-data.md)
- [Utvärdera dina data](how-to-custom-speech-evaluate-data.md)

Om du väljer att testa noggrannheten är det viktigt att välja en akustisk datauppsättning som skiljer sig från den du använde med din modell för att få en realistisk känsla av modellens prestanda.

## <a name="next-steps"></a>Nästa steg

- [Distribuera din modell](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>Ytterligare resurser

- [Förbereda och testa dina data](how-to-custom-speech-test-data.md)
- [Granska dina data](how-to-custom-speech-inspect-data.md)
- [Utvärdera dina data](how-to-custom-speech-evaluate-data.md)
- [Träna din modell](how-to-custom-speech-train-model.md)
