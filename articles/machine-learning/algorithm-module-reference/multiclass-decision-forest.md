---
title: 'Besluts skog i flera klasser: modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen för besluts skogar i flera klasser i Azure Machine Learning för att skapa en Machine Learning-modell som baseras på algoritmen för *besluts skogar* .
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: 85459fe586ccbb5adb1e86b153ec63252c0cce2d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73466019"
---
# <a name="multiclass-decision-forest-module"></a>Modul för besluts skog för flera klasser

I den här artikeln beskrivs en modul i Azure Machine Learning designer (för hands version).

Använd den här modulen för att skapa en Machine Learning-modell som baseras på algoritmen för *besluts skogar* . En besluts skog är en ensemble-modell som snabbt skapar en serie besluts träd, medan man lär sig från Taggade data.

## <a name="more-about-decision-forests"></a>Mer om besluts skogar

Besluts skogens algoritm är en ensemble Learning-metod för klassificering. Algoritmen fungerar genom att skapa flera besluts träd och sedan *rösta* på den mest populära utmatnings klassen. Röstning är en form av agg regering, där varje träd i en klassificerings besluts skog utvärderar ett icke-normaliserat frekvens histogram med etiketter. Agg regerings processen summerar dessa histogram och normaliserar resultatet för att få "sannolikhet" för varje etikett. De träd som har hög förutsägelse säkerhet har en större vikt i det slutliga beslutet av ensemblen.

Besluts träd i allmänhet är icke-parameter modeller, vilket innebär att de har stöd för data med varierande distributioner. I varje träd körs en sekvens med enkla tester för varje klass, vilket ökar nivåerna i en träd struktur tills en lövnod (beslut) har nåtts.

Besluts träd har många fördelar:

+ De kan representera icke-linjära besluts gränser.
+ De är effektiva för beräknings-och minnes användning vid utbildning och förutsägelse.
+ De utför integrerade funktions val och klassificering.
+ De är elastiska i närvaro av brus funktioner.

Besluts skogens klassificerare i Azure Machine Learning består av en ensemble i besluts träd. Vanligt vis ger Ensemble-modeller bättre täckning och exakthet än enskilda besluts träd. Mer information finns i [besluts träd](https://go.microsoft.com/fwlink/?LinkId=403677).

## <a name="how-to-configure-multiclass-decision-forest"></a>Konfigurera besluts skogen i flera klasser

1. Lägg till modulen för **besluts skog** i flera klasser i din pipeline i designern. Du kan hitta den här modulen under **Machine Learning**, **initiera modell**och **klassificering**.

2. Dubbelklicka på modulen för att öppna fönstret **Egenskaper** .

3. För **metoden**för att sampla om väljer du den metod som används för att skapa enskilda träd.  Du kan välja mellan bagage eller replikering.

    + **Bagage**: bagage kallas även *Start agg regering*. I den här metoden odlas varje träd på ett nytt exempel som skapats genom att slumpmässigt sampla den ursprungliga data uppsättningen med ersättning tills du har en data uppsättning som är den ursprungliga storleken. Modellernas utdata kombineras med *röstning*, vilket är en form av agg regering. Mer information finns i Wikipedia-posten för start agg regering.

    + **Replikera**: i replikering tränas varje träd på exakt samma indata. Bestämning av vilka delade predikat som används för varje trädnod förblir slumpmässiga och skapar olika träd.

   

4. Ange hur du vill att modellen ska tränas genom att ställa in alternativet **skapa utbildare läge** .

    + **Enskild parameter**: Välj det här alternativet om du vet hur du vill konfigurera modellen och ange en uppsättning värden som argument.


5. **Antal besluts träd**: Ange det maximala antalet besluts träd som kan skapas i ensemblen. Genom att skapa fler besluts träd kan du eventuellt få bättre täckning, men inlärnings tiden kan öka.

    Det här värdet styr också antalet träd som visas i resultaten vid visualisering av den tränade modellen. Om du vill se eller skriva ut ett enda träd kan du ange värdet till 1. Det innebär dock att endast ett träd kan skapas (trädet med den inledande uppsättningen parametrar) och inga ytterligare iterationer utförs.

6. **Högsta djup för besluts träden**: Ange ett tal för att begränsa det maximala djupet för besluts träd. Att öka djupet i trädet kan öka precisionen på risken för viss överanpassning och ökad inlärnings tid.

7. **Antal slumpmässiga delningar per nod**: Ange antalet delningar som ska användas när du skapar varje nod i trädet. En *delning* innebär att funktionerna i varje nivå i trädet (noden) är slumpmässigt uppdelade.

8. **Minsta antal sampel per lövnod**: Ange det minsta antal fall som krävs för att skapa en terminalsession (löv) i ett träd. Genom att öka det här värdet ökar du tröskelvärdet för att skapa nya regler.

    Till exempel, med standardvärdet 1, kan ett enda ärende orsaka att en ny regel skapas. Om du ökar värdet till 5 måste tränings data innehålla minst fem fall som uppfyller samma villkor.



10. Anslut en etikettad data uppsättning och en av modulerna för utbildning:

    + Om du ställer in **skapa utbildare** för **en parameter**använder du modulen [träna modell](./train-model.md) .

11. Köra en pipeline.

## <a name="results"></a>Resultat

När utbildningen är klar:

+ Om du vill se trädet som skapades på varje iteration högerklickar du på utdata från modulen [träna modell](./train-model.md) och väljer **visualisera**.
+ Om du vill se reglerna för varje nod klickar du på varje träd för att öka detalj nivån i delningarna.


## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 