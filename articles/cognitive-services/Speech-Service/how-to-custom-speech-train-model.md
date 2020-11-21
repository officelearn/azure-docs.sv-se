---
title: Träna och distribuera en Custom Speech modell med tal tjänster
titleSuffix: Azure Cognitive Services
description: I den här artikeln får du lära dig hur du tränar och distribuerar Custom Speech modeller. Träna en tal-till-text-modell kan förbättra igenkännings precisionen för Microsofts bas linje modell eller en anpassad modell. En modell tränas med hjälp av välmärkta avskrifter och relaterad text.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: trbye
ms.openlocfilehash: 9193da2f3841bb94aa395399c31d7fe826c395e0
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025607"
---
# <a name="train-and-deploy-a-custom-speech-model"></a>Träna och distribuera en Custom Speech-modell

I den här artikeln får du lära dig hur du tränar och distribuerar Custom Speech modeller. Träna en tal-till-text-modell kan förbättra igenkännings precisionen för Microsofts bas linje modell. En modell tränas med hjälp av välmärkta avskrifter och relaterad text. Dessa data uppsättningar tillsammans med tidigare överförda ljud data används för att förfina och träna tal-till-text-modellen.

## <a name="use-training-to-resolve-accuracy-issues"></a>Använd utbildning för att lösa problem med problemlösning

Om du stöter på igenkännings problem med en bas modell kan du förbättra precisionen med hjälp av välmärkta avskrifter och relaterade data för att träna en anpassad modell. Använd den här tabellen för att avgöra vilken data mängd som ska användas för att åtgärda dina problem:

| Användningsfall | Datatyp |
| -------- | --------- |
| Förbättra igenkännings precisionen för branschspecifika vokabulär och grammatik, till exempel medicinsk terminologi eller IT-jargong. | Relaterad text (meningar/yttranden) |
| Definiera den fonetiska och visade formen av ett ord eller en term som innehåller uttal som inte är standard, t. ex. produkt namn eller akronymer. | Relaterad text (uttal) |
| Förbättra igenkännings precisionen för tal format, accenttecken eller vissa bakgrunds brus. | Ljud + medmärkta avskrifter |

## <a name="train-and-evaluate-a-model"></a>Träna och utvärdera en modell

Det första steget för att träna en modell är att överföra tränings data. Använd [förbereda och testa dina data](./how-to-custom-speech-test-and-train.md) för steg-för-steg-instruktioner för att förbereda medmärkta avskrifter och relaterad text (yttranden och uttal). När du har överfört tränings data följer du de här anvisningarna för att börja träna din modell:

1. Logga in på [Custom Speech Portal](https://speech.microsoft.com/customspeech).
2. Navigera till **> för tal till text Custom Speech > [namn på projektet] > träning**.
3. Klicka på **träna modell**.
4. Ge sedan din utbildning ett **namn** och en **Beskrivning**.
5. Välj det scenario som passar din domän bäst på den nedrullningsbara menyn för **scenario och bas linje modell** . Om du är osäker på vilket scenario du väljer väljer du **Allmänt**. Bas linje modellen är start punkten för utbildning. Den senaste modellen är vanligt vis det bästa valet.
6. På sidan **Välj tränings data** väljer du en eller flera ljud-och data uppsättningar med mänsklig etikett som du vill använda för utbildning.
7. När utbildningen är klar kan du välja att utföra testning av precisionen för den nytränade modellen. Det här är valfritt.
8. Välj **skapa** för att skapa din anpassade modell.

I övnings tabellen visas en ny post som motsvarar den nyligen skapade modellen. Tabellen visar också status: bearbetning, lyckades, misslyckades.

Se [hur du kan](how-to-custom-speech-evaluate-data.md) utvärdera och förbättra Custom Speech modellens precision. Om du väljer att testa noggrannhet är det viktigt att välja en akustisk data uppsättning som skiljer sig från den som du använde med din modell för att få en realistisk känsla för modellens prestanda.

## <a name="deploy-a-custom-model"></a>Distribuera en anpassad modell

När du har laddat upp och inspekterat data, utvärderat noggrannhet och tränat en anpassad modell, kan du distribuera en anpassad slut punkt för användning med dina appar, verktyg och produkter. 

Om du vill skapa en ny anpassad slut punkt loggar du in på [Custom Speech Portal](https://speech.microsoft.com/customspeech) och väljer **distribution** på Custom Speech menyn överst på sidan. Om det här är din första körning ser du att det inte finns några slut punkter som visas i tabellen. När du har skapat en slut punkt använder du den här sidan för att spåra varje distribuerad slut punkt.

Välj sedan **Lägg till slut punkt** och ange ett **namn** och en **Beskrivning** för din anpassade slut punkt. Välj sedan den anpassade modell som du vill koppla till den här slut punkten. På den här sidan kan du också aktivera loggning. Med loggning kan du övervaka slut punkts trafik. Om den är inaktive rad lagras inte trafiken.

![Så här distribuerar du en modell](./media/custom-speech/custom-speech-deploy-model.png)

> [!NOTE]
> Glöm inte att acceptera villkoren för användning och pris information.

Välj sedan **skapa**. Den här åtgärden återgår till **distributions** sidan. Tabellen innehåller nu en post som motsvarar din anpassade slut punkt. Slut punktens status visar dess aktuella tillstånd. Det kan ta upp till 30 minuter att instansiera en ny slut punkt med hjälp av dina anpassade modeller. När distributions statusen ändras till **slutförd**, är slut punkten redo att användas.

När slut punkten har distribuerats visas slut punktens namn som en länk. Klicka på länken om du vill visa information som är speciell för din slut punkt, till exempel slut punkts nyckel, slut punkts-URL och exempel kod.

## <a name="view-logging-data"></a>Visa loggnings data

Loggnings data är tillgängliga för hämtning under **slut punkt > information**.
> [!NOTE]
>Loggnings data är tillgängliga i 30 dagar på Microsofts ägda lagring och kommer att tas bort efteråt. Om ett kundägda lagrings konto är länkat till kognitiva tjänster-prenumerationen tas loggnings data inte bort automatiskt.

## <a name="next-steps"></a>Nästa steg

* Lär dig [hur du använder din anpassade modell](how-to-specify-source-language.md).

## <a name="additional-resources"></a>Ytterligare resurser

- [Förbered och testa dina data](./how-to-custom-speech-test-and-train.md)
- [Inspektera dina data](how-to-custom-speech-inspect-data.md)
- [Utvärdera dina data](how-to-custom-speech-evaluate-data.md)
