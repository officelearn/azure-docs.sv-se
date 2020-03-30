---
title: Så här väljer du en maskininlärningsalgoritm
titleSuffix: Azure Machine Learning
description: Så här väljer du Azure Machine Learning-algoritmer för övervakad och oövervakad inlärning i kluster-, klassificerings- eller regressionsexperiment.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: FrancescaLazzeri
ms.author: lazzeri
ms.reviewer: cgronlun
ms.date: 03/05/2020
ms.openlocfilehash: e0482bac9569a834adf3e1cdef2b3f702980eac0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78328671"
---
# <a name="how-to-select-algorithms-for-azure-machine-learning"></a>Så här väljer du algoritmer för Azure Machine Learning

En vanlig fråga är "Vilken maskininlärningsalgoritm ska jag använda?" Vilken algoritm du väljer beror främst på två olika aspekter av ditt datavetenskapscenario:

 - **Vad vill du göra med dina data?** Vad är den företagsfråga du vill besvara genom att lära av dina tidigare data?

 - **Vilka är kraven i ditt datavetenskapscenario?** Närmare bestämt, vad är noggrannhet, träningstid, linjäritet, antal parametrar och antal funktioner som din lösning stöder?

 ![Överväganden för att välja algoritmer: Vad vill du veta? Vilka är scenariokraven?](./media/how-to-select-algorithms/how-to-select-algorithms.png)

## <a name="business-scenarios-and-the-machine-learning-algorithm-cheat-sheet"></a>Affärsscenarier och maskininlärningsalgoritmens fuskblad

[Azure Machine Learning Algorithm Cheat Sheet](https://docs.microsoft.com/azure/machine-learning/algorithm-cheat-sheet?WT.mc_id=docs-article-lazzeri) hjälper dig med det första övervägandet: Vad du vill göra med dina **data?** På maskininlärningsalgoritmens fusklista letar du efter en uppgift som du vill utföra och hittar sedan en [Azure Machine Learning-designeralgoritm](https://docs.microsoft.com/azure/machine-learning/concept-designer?WT.mc_id=docs-article-lazzeri) för den prediktiva analyslösningen. 

Machine Learning designer ger en omfattande portfölj av algoritmer, såsom [Multiclass Decision Forest](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-decision-forest?WT.mc_id=docs-article-lazzeri), [Rekommendation system](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/evaluate-recommender?WT.mc_id=docs-article-lazzeri), Neural Network [Regression,](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/neural-network-regression?WT.mc_id=docs-article-lazzeri) [Multiclass Neural Network](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-neural-network?WT.mc_id=docs-article-lazzeri)och [K-Medel Clustering](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/k-means-clustering?WT.mc_id=docs-article-lazzeri). Varje algoritm är utformad för att lösa en annan typ av maskininlärningsproblem. Se [Machine Learning designer algoritm och modul referens](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/module-reference?WT.mc_id=docs-article-lazzeri) för en komplett lista tillsammans med dokumentation om hur varje algoritm fungerar och hur man ställer in parametrar för att optimera algoritmen.

> [!NOTE]
> Om du vill hämta maskininlärningsalgoritmens lathund går du till [Lats machine learning algorithm cheat sheet](https://docs.microsoft.com/azure/machine-learning/algorithm-cheat-sheet?WT.mc_id=docs-article-lazzeri).
> 
> 

Tillsammans med vägledning i Azure Machine Learning Algorithm Cheat Sheet bör du tänka på andra krav när du väljer en maskininlärningsalgoritm för din lösning. Följande är ytterligare faktorer att tänka på, till exempel noggrannhet, träningstid, linjäritet, antal parametrar och antal funktioner.

## <a name="additional-requirements-for-a-data-science-scenario"></a>Ytterligare krav för ett data science-scenario

När du vet vad du vill göra med dina data måste du fastställa ytterligare krav för din lösning. 

Gör val och eventuellt kompromisser för följande krav:

- Noggrannhet
- Träningstid
- Linjäritet
- Antal parametrar
- Antal funktioner

## <a name="accuracy"></a>Noggrannhet

Noggrannhet i maskininlärning mäter effektiviteten av en modell som andelen verkliga resultat till totala fall. I Machine Learning-designern beräknar [modulen Utvärdera modell](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/evaluate-model?WT.mc_id=docs-article-lazzeri) en uppsättning utvärderingsmått av branschstandard. Du kan använda den här modulen för att mäta noggrannheten hos en tränad modell.

Att få det mest exakta svaret är inte alltid nödvändigt. Ibland är en approximation tillräcklig, beroende på vad du vill använda den till. Om så är fallet, kanske du kan minska din handläggningstid dramatiskt genom att hålla fast vid mer ungefärliga metoder. Ungefärliga metoder tenderar också naturligt att undvika övermontering.

Det finns tre sätt att använda modulen Utvärdera modell:

- Generera poäng över dina träningsdata för att utvärdera modellen
- Generera poäng på modellen, men jämför dessa poäng med poäng på en reserverad testuppsättning
- Jämför poäng för två olika men relaterade modeller med samma uppsättning data

En fullständig lista över mått och metoder som du kan använda för att utvärdera maskininlärningsmodellernas noggrannhet finns i [Module Utvärdera modell](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/evaluate-model?WT.mc_id=docs-article-lazzeri).

## <a name="training-time"></a>Träningstid

I övervakad inlärning innebär utbildning att använda historiska data för att skapa en maskininlärningsmodell som minimerar fel. Antalet minuter eller timmar som krävs för att träna en modell varierar mycket mellan algoritmer. Träningstiden är ofta nära knuten till noggrannhet; den ena följer vanligtvis med den andra. 

Dessutom är vissa algoritmer mer känsliga för antalet datapunkter än andra. Du kan välja en specifik algoritm eftersom du har en tidsbegränsning, särskilt när datauppsättningen är stor.

I Machine Learning designer, skapa och använda en maskininlärningsmodell är vanligtvis en trestegsprocess:

1.  Konfigurera en modell genom att välja en viss typ av algoritm och sedan definiera dess parametrar eller hyperparametrar. 

2.  Ange en datauppsättning som är märkt och har data som är kompatibel med algoritmen. Anslut både data och modell till [Tågmodellmodulen](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/train-model?WT.mc_id=docs-article-lazzeri).

3.  När utbildningen är klar använder du den tränade modellen med en av [bedömningsmodulerna](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/score-model?WT.mc_id=docs-article-lazzeri) för att göra förutsägelser om nya data.

## <a name="linearity"></a>Linjäritet

Linjäritet i statistik och maskininlärning innebär att det finns ett linjärt samband mellan en variabel och en konstant i datauppsättningen. Linjära klassificeringsalgoritmer förutsätter till exempel att klasser kan avgränsas med en rak linje (eller dess högre dimensionella analog).

Massor av maskininlärningsalgoritmer använder sig av linjäritet. I Azure Machine Learning-designern ingår: 

- [Multiklass logistisk regression](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-logistic-regression?WT.mc_id=docs-article-lazzeri)
- [Tvåklassad logistisk regression](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-logistic-regression?WT.mc_id=docs-article-lazzeri)
- [Stöd vektor maskiner](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-support-vector-machine?WT.mc_id=docs-article-lazzeri)  

Linjära regressionsalgoritmer förutsätter att datatrender följer en rak linje. Detta antagande är inte dåligt för vissa problem, men för andra minskar noggrannheten. Trots sina nackdelar, linjära algoritmer är populära som en första strategi. De tenderar att vara algoritmiskt enkla och snabba att träna.

![Ickelinjär klassgräns](./media/how-to-select-algorithms/nonlinear-class-boundary.png)

***Ickelinjär klassgräns:*** *Om du förlitar dig på en linjär klassificeringsalgoritm skulle det leda till låg noggrannhet.*

![Data med en ickelinjär trend](./media/how-to-select-algorithms/nonlinear-trend.png)

***Data med en ickelinjär trend***: *Om du använder en linjär regressionsmetod skulle det generera mycket större fel än nödvändigt.*

## <a name="number-of-parameters"></a>Antal parametrar

Parametrar är de rattar en datavetare får vända när du ställer in en algoritm. De är tal som påverkar algoritmens beteende, till exempel feltolerans eller antal iterationer, eller alternativ mellan varianter av hur algoritmen beter sig. Algoritmens träningstid och noggrannhet kan ibland vara känsliga för att få rätt inställningar. Vanligtvis kräver algoritmer med ett stort antal parametrar mest trial and error för att hitta en bra kombination.

Alternativt finns det [modulen Tune Model Hyperparameters](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/tune-model-hyperparameters?WT.mc_id=docs-article-lazzeri) i Machine Learning designer: Målet med den här modulen är att bestämma de optimala hyperparametrarna för en maskininlärningsmodell. Modulen bygger och testar flera modeller med hjälp av olika kombinationer av inställningar. Den jämför mått över alla modeller för att få kombinationer av inställningar. 

Även om detta är ett bra sätt att se till att du har spänt ut parameterutrymmet, ökar den tid som krävs för att träna en modell exponentiellt med antalet parametrar. Fördelen är att många parametrar vanligtvis indikerar att en algoritm har större flexibilitet. Det kan ofta uppnå mycket god noggrannhet, förutsatt att du kan hitta rätt kombination av parameterinställningar.

## <a name="number-of-features"></a>Antal funktioner

I maskininlärning är en funktion en kvantifierbar variabel av fenomenet du försöker analysera. För vissa typer av data kan antalet funktioner vara mycket stort jämfört med antalet datapunkter. Detta är ofta fallet med genetik eller textdata. 

Ett stort antal funktioner kan mosse ner vissa lärande algoritmer, vilket gör utbildningstiden ogenomtraktigt lång. [Supportvektordatorer](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-support-vector-machine?WT.mc_id=docs-article-lazzeri) är särskilt väl lämpade för scenarier med ett stort antal funktioner. Av denna anledning har de använts i många program från informationshämtning till text- och bildklassificering. Stöd vektordatorer kan användas för både klassificerings- och regressionsaktiviteter.

Funktionsval avser processen att tillämpa statistiska tester på indata, med tanke på en angiven utgång. Målet är att avgöra vilka kolumner som är mer prediktiva för utdata. Modulen [Filterbaserad funktionsval](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/filter-based-feature-selection?WT.mc_id=docs-article-lazzeri) i Machine Learning designer innehåller flera funktionsvalsalgoritmer att välja mellan. Modulen innehåller korrelationsmetoder som Pearson korrelation och chi-kvadratvärden.

Du kan också använda [modulen Permutationsfunktionsbetesvikt](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/permutation-feature-importance?WT.mc_id=docs-article-lazzeri) för att beräkna en uppsättning prioritetspoäng för funktioner för din datauppsättning. Du kan sedan utnyttja dessa poäng för att hjälpa dig att avgöra de bästa funktionerna som ska användas i en modell.


## <a name="next-steps"></a>Nästa steg

 - [Läs mer om Azure Machine Learning designer](https://docs.microsoft.com/azure/machine-learning/service/concept-designer?WT.mc_id=docs-article-lazzeri)
 - Beskrivningar av alla maskininlärningsalgoritmer som är tillgängliga i Azure Machine Learning designer finns i [Machine Learning designer algoritm och modulreferens](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/module-reference?WT.mc_id=docs-article-lazzeri)
 - För att utforska förhållandet mellan djupinlärning, maskininlärning och AI, se [Djupinlärning kontra maskininlärning](https://docs.microsoft.com/azure/machine-learning/service/concept-deep-learning-vs-machine-learning?WT.mc_id=docs-article-lazzeri)
