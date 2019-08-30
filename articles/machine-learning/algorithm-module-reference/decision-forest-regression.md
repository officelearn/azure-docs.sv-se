---
title: 'Besluts skogs regression: Modulreferens'
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder besluts skogs Regressions modulen i Azure Machine Learning-tjänsten för att skapa en Regressions modell som baseras på en ensemble i besluts träd.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: b8bb3285aecb6aff399606e6263f014027a86581
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128893"
---
# <a name="decision-forest-regression-module"></a>Besluts skogs Regressions modul

I den här artikeln beskrivs en modul i Visual Interface (för hands version) för Azure Machine Learning tjänst.

Använd den här modulen för att skapa en Regressions modell som baseras på en ensemble i besluts träd.

När du har konfigurerat modellen måste du träna modellen med hjälp av en etikettad data uppsättning och modulen [träna modell](./train-model.md) .  Den tränade modellen kan sedan användas för att göra förutsägelser. 

## <a name="how-it-works"></a>Hur det fungerar

Besluts träd är icke-parameter modeller som utför en sekvens med enkla tester för varje instans, och som påbörjar en binär träd data struktur tills en lövnod (beslut) har nåtts.

Besluts träd har följande fördelar:

- De är effektiva i både beräknings-och minnes användning vid utbildning och förutsägelse.

- De kan representera icke-linjära besluts gränser.

- De utför integrerade funktions val och klassificering och är elastiska i närvaro av brus funktioner.

Denna Regressions modell består av en ensemble i besluts träd. Varje träd i en Regressions besluts skog matar ut en Gaussisk-distribution som en förutsägelse. En agg regering utförs över ensemblen i träd för att hitta en Gaussisk-distribution som är närmast den kombinerade distributionen för alla träd i modellen.

Mer information om det teoretiska ramverket för den här algoritmen och dess implementering finns i den här artikeln: [Besluts skogar: Ett enhetligt ramverk för klassificering, regression, densitets uppskattning, plattforms inlärning och delvis övervakad inlärning](https://www.microsoft.com/en-us/research/publication/decision-forests-a-unified-framework-for-classification-regression-density-estimation-manifold-learning-and-semi-supervised-learning/?from=http%3A%2F%2Fresearch.microsoft.com%2Fapps%2Fpubs%2Fdefault.aspx%3Fid%3D158806#)

## <a name="how-to-configure-decision-forest-regression-model"></a>Konfigurera Regressions modellen för besluts skogar

1. Lägg till **besluts skogs Regressions** modulen i experimentet. Du kan hitta modulen i gränssnittet under **Machine Learning**, **initiera modellen**och **regression**.

2. Öppna egenskaperna för modulen och välj den metod som används för att skapa enskilda träd för omsamplings **metod**.  Du kan välja mellan **bagage** eller **Replikera**.

    - **Bagage**: Bagaget kallas även *bootstrap-aggregering*. Varje träd i en Regressions besluts skog matar ut en Gaussisk fördelning genom förutsägelse. Aggregation är att hitta en Gaussisk vars första två moment motsvarar momentet av en blandning av Gaussisk som anges genom att kombinera alla Gaussisk som returneras av enskilda träd.

         Mer information finns i Wikipedia-posten för [Start agg regering](https://wikipedia.org/wiki/Bootstrap_aggregating).

    - **Replikera**: I replikering tränas varje träd på exakt samma indata. Bestämning av vilket delat predikat som används för varje trädnod förblir slumpmässigt och träden är olika.

         Mer information om inlärnings processen med alternativet **Replikera** finns i [besluts skogar för visuellt innehåll och medicinsk bild analys. Criminisi och J. Shotton. Springer 2013. ](https://research.microsoft.com/projects/decisionforests/).

3. Ange hur du vill att modellen ska tränas genom att ställa in alternativet **skapa utbildare läge** .

    - **Enskild parameter**

      Om du vet hur du vill konfigurera modellen kan du ange en speciell uppsättning värden som argument. Du kanske har lärt dig dessa värden genom att experimentera eller ta emot dem som vägledning.



4. För **antalet besluts träd**anger du det totala antalet besluts träd som ska skapas i ensemblen. Genom att skapa fler besluts träd kan du eventuellt få bättre täckning, men inlärnings tiden ökar.

    > [!TIP]
    > Det här värdet styr också antalet träd som visas vid visualisering av den tränade modellen. Om du vill se eller skriva ut ett enda träd kan du ange värdet till 1. Det innebär dock att endast ett träd skapas (trädet med den inledande uppsättningen parametrar) och inga ytterligare iterationer utförs.

5. Ange ett tal för att begränsa det maximala djupet för besluts träden för att begränsa det maximala djupet för **besluts träd**. Att öka djupet i trädet kan öka precisionen på risken för viss överanpassning och ökad inlärnings tid.

6. För **antalet slumpmässiga delningar per nod**anger du antalet delningar som ska användas när du skapar varje nod i trädet. En *delning* innebär att funktionerna i varje nivå i trädet (noden) är slumpmässigt uppdelade.

7. För **minsta antal sampel per lövnod**anger du det minsta antal fall som krävs för att skapa en terminalsession (löv) i ett träd.

     Genom att öka det här värdet ökar du tröskelvärdet för att skapa nya regler. Till exempel, med standardvärdet 1, kan ett enda ärende orsaka att en ny regel skapas. Om du ökar värdet till 5 måste tränings data innehålla minst fem fall som uppfyller samma villkor.


9. Anslut en etikettad data uppsättning, Välj en enstaka etikett-kolumn som innehåller högst två resultat och Anslut till [träna modell](./train-model.md).

    - Om du anger alternativet **skapa utbildare läge** till **en enda parameter**tränar du modellen med modulen [träna modell](./train-model.md) .

   

10. Kör experimentet.

### <a name="results"></a>Resultat

När utbildningen är klar:

+ Om du vill se trädet som skapades på varje iteration högerklickar du på utdata i modulen utbildning och väljer **visualisera**.

+ Om du vill se reglerna för varje nod klickar du på varje träd och ökar detalj nivån i delningarna.

+ Om du vill spara en ögonblicks bild av den tränade modellen högerklickar du på utdata från modulen utbildning och väljer **Spara som utbildad modell**. Den här kopian av modellen uppdateras inte vid efterföljande körningar av experimentet. 

## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för att Azure Machine Learning-tjänsten. 