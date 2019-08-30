---
title: Algoritm- och modulreferens
titleSuffix: Azure Machine Learning service
description: Lär dig mer om modulerna som är tillgängliga i Azure Machine Learning Visual Interface
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 065931140894478caee9d4ea49dac49f2415716b
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128642"
---
# <a name="algorithm--module-reference-overview"></a>Översikt över algoritmen & modul

Det här referens innehållet ger teknisk bakgrund på var och en av de algoritmer för maskin inlärning och moduler som är tillgängliga i Visual Interface (för hands version) av Azure Machine Learning tjänst.

Varje modul representerar en uppsättning kod som kan köras separat och utföra en maskin inlärnings uppgift, med de nödvändiga inmatningarna. En modul kan innehålla en viss algoritm eller utföra en uppgift som är viktig i Machine Learning, till exempel värde utbyte som saknas eller statistisk analys.

> [!TIP]
> I ett experiment i det visuella gränssnittet kan du hämta information om en speciell modul. Välj modulen och välj sedan länken **mer hjälp** i fönstret **snabb hjälp** .

## <a name="modules"></a>Moduler

Moduler är ordnade efter funktion:

| Funktioner | Beskrivning | Modul |
| --- |--- | ---- |
| Dataformatkonverteringar | Konvertera data mellan olika fil format som används i Machine Learning, | [Konvertera till CSV](convert-to-csv.md) |
| Data indata och utdata | Flytta data från moln källor till experimentet. Skriv resultaten eller mellanliggande data till Azure Storage, en SQL-databas eller Hive, medan du kör ett experiment eller Använd moln lagring för att utbyta data mellan experiment.  | [Importera data](import-data.md)<br/>[Exportera data](export-data.md)<br/>[Ange data manuellt](enter-data-manually.md) |
| Datatransformering | Åtgärder för data som är unika för maskin inlärning, till exempel normaliserande eller diskretisering data, funktions val och en minskning av dimensionalitet.| [Välj kolumner i data uppsättning](select-columns-in-dataset.md) <br/> [Redigera metadata](edit-metadata.md) <br/> [Rensa saknade data](clean-missing-data.md) <br/> [Lägg till kolumner](add-columns.md) <br/> [Lägg till rader](add-rows.md) <br/> [Ta bort dubblettrader](remove-duplicate-rows.md) <br/> [Koppla data](join-data.md) <br/> [Dela data](split-data.md) <br/> [Normalisera data](normalize-data.md) <br/> [Partition och exempel](partition-and-sample.md) |
| Python-och R-moduler | Skriv kod och bädda in den i en modul för att integrera python och R med experimentet. | [Kör Python-skript](execute-python-script.md)   <br/> [Skapa python-modell](create-python-model.md) <br/> [Kör R-skript](execute-r-script.md)
|  | **Machine Learning**-algoritmer: | |
| Klassificering | Förutsäg en klass.  Välj mellan binära (två klasser) eller algoritmer för multiklasser.| [Besluts skog med flera klasser](multiclass-decision-forest.md) <br/> [Besluts träd med djup klass](multiclass-boosted-decision-tree.md) <br/> [Logistik regression med multiklass](multiclass-logistic-regression.md)  <br/> [Neurala nätverk i multiklass](multiclass-neural-network.md)  <br/>  [Logistik regression med två klasser](two-class-logistic-regression.md)  <br/>[Genomsnittlig Perceptron i två klasser](two-class-averaged-perceptron.md) <br/> [&nbsp;&nbsp;Besluts&nbsp;träd med två klasser](two-class-boosted-decision-tree.md)  <br/> [Besluts skog med två klasser](two-class-decision-forest.md)  <br/> [Neurala nätverk med två klasser](two-class-neural-network.md)  <br/> [Två&#8209;klass&nbsp;stöd&nbsp;förVector&nbsp;Machine](two-class-support-vector-machine.md) 
| Klustring | Gruppera data tillsammans.| [K-innebär klustring](k-means-clustering.md)
| Regression | Förutsäg ett värde. | [Linjär regression](linear-regression.md)  <br/> [Neurala nätverks regression](neural-network-regression.md)  <br/> [Besluts skogs regression](decision-forest-regression.md)  <br/> [Regressions analys av&nbsp;&nbsp;&nbsp;besluts träd](boosted-decision-tree-regression.md)
|  | **Bygga och utvärdera modeller**: | |
| Träna   | Kör data via algoritmen. | [Träna modell](train-model.md)  <br/> [Träna kluster modell](train-clustering-model.md)    |
| Utvärdera modell | Mät noggrannheten för den tränade modellen. |  [Utvärdera modell](evaluate-model.md)
| Poäng | Hämta förutsägelser från modellen som du precis har tränat. | [Tillämpa omvandling](apply-transformation.md)<br/>[Tilldela&nbsp;data&nbsp;tillkluster&nbsp;](assign-data-to-clusters.md) <br/>[Poäng modell](score-model.md)

## <a name="error-messages"></a>Felmeddelanden

Lär dig mer om [fel meddelanden och undantags koder](machine-learning-module-error-codes.md) som du kan stöta på med moduler i det visuella gränssnittet i Azure Machine Learning-tjänsten.
