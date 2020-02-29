---
title: Algoritm- och modulreferens
description: Lär dig mer om modulerna som är tillgängliga i Azure Machine Learning designer (för hands version)
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: peterclu
ms.author: peterlu
ms.date: 02/22/2020
ms.openlocfilehash: ece4b7a85ff5738900b8f999cc2f14ba35ecab0d
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2020
ms.locfileid: "77920101"
---
# <a name="algorithm--module-reference-for-azure-machine-learning-designer-preview"></a>Algoritm & module-referens för Azure Machine Learning designer (för hands version)

Det här referens innehållet ger teknisk bakgrund på var och en av Machine Learning-algoritmerna och modulerna som är tillgängliga i Azure Machine Learning designer (för hands version).

Varje modul representerar en uppsättning kod som kan köras separat och utföra en maskin inlärnings uppgift, med de nödvändiga inmatningarna. En modul kan innehålla en viss algoritm eller utföra en uppgift som är viktig i Machine Learning, till exempel värde utbyte som saknas eller statistisk analys.

Hjälp med att välja algoritmer finns i 
* [Så här väljer du algoritmer](../how-to-select-algorithms.md)
* [Lathund-blad för Azure Machine Learning algorithm](../algorithm-cheat-sheet.md)

> [!TIP]
> I en pipeline i designern kan du hämta information om en speciell modul. Välj modulen och välj sedan länken **mer hjälp** i fönstret **snabb hjälp** .

## <a name="data-preparation-modules"></a>Moduler för data förberedelse


| Funktioner | Beskrivning | Modul |
| --- |--- | --- |
| Indata och utdata | Flytta data från moln källor till din pipeline. Skriv resultaten eller mellanliggande data till Azure Storage, en SQL-databas eller Hive, medan du kör en pipeline eller Använd moln lagring för att utbyta data mellan pipeliner.  | [Ange data manuellt](enter-data-manually.md) <br/> [Exportera data](export-data.md) <br/> [Importera data](import-data.md) |
| Dataomvandling | Åtgärder för data som är unika för maskin inlärning, till exempel normaliserande eller diskretisering data, Dimensional reducering och konvertering av data mellan olika fil format.| [Lägg till kolumner](add-columns.md) <br/> [Lägg till rader](add-rows.md) <br/> [Använd matematik åtgärd](apply-math-operation.md) <br/> [Använd SQL-transformering](apply-sql-transformation.md) <br/> [Rensa saknade data](clean-missing-data.md) <br/> [Klipp värden](clip-values.md) <br/> [Konvertera till CSV](convert-to-csv.md) <br/> [Konvertera till data uppsättning](convert-to-dataset.md) <br/> [Konvertera till indikator värden](convert-to-indicator-values.md) <br/> [Redigera metadata](edit-metadata.md) <br/> [Koppla data](join-data.md) <br/> [Normalisera data](normalize-data.md) <br/> [Partition och exempel](partition-and-sample.md)  <br/> [Ta bort dubblettrader](remove-duplicate-rows.md) <br/> [SMOTE](smote.md) <br/> [Välj kolumner-transformering](select-columns-transform.md) <br/> [Välj kolumner i data uppsättning](select-columns-in-dataset.md) <br/> [Dela data](split-data.md) |
| Val av funktion | Välj en delmängd av relevanta, användbara funktioner som du kan använda när du skapar en analys modell. | [Filtrera baserat funktions val](filter-based-feature-selection.md) <br/> [Viktighets funktion för permutation](permutation-feature-importance.md) |
| Statistiska funktioner | Tillhandahålla en mängd olika statistiska metoder för data vetenskap. | [Sammanfatta data](summarize-data.md)|

## <a name="machine-learning-algorithms"></a>Maskininlärningsalgoritmer

| Funktioner | Beskrivning | Modul |
| --- |--- | --- |
| Regression | Förutsäg ett värde. | [Regressions analys av besluts träd](boosted-decision-tree-regression.md) <br/> [Besluts skogs regression](decision-forest-regression.md) <br/> [Linjär regression](linear-regression.md)  <br/> [Neurala nätverks regression](neural-network-regression.md)  <br/> |
| Klustring | Gruppera data tillsammans.| [K-innebär klustring](k-means-clustering.md)
| Klassificering | Förutsäg en klass.  Välj mellan binära (två klasser) eller algoritmer för multiklasser.| [Besluts träd med djup klass](multiclass-boosted-decision-tree.md) <br/> [Besluts skog med flera klasser](multiclass-decision-forest.md) <br/> [Logistik regression med multiklass](multiclass-logistic-regression.md)  <br/> [Neurala nätverk i multiklass](multiclass-neural-network.md) <br/> [En vs. alla multiklass](one-vs-all-multiclass.md) <br/> [Genomsnittlig Perceptron i två klasser](two-class-averaged-perceptron.md) <br/>  [Besluts träd med två klasser](two-class-boosted-decision-tree.md)  <br/> [Besluts skog med två klasser](two-class-decision-forest.md) <br/>  [Logistik regression med två klasser](two-class-logistic-regression.md) <br/> [Neurala nätverk med två klasser](two-class-neural-network.md) <br/> [Två klass stöd för Vector Machine](two-class-support-vector-machine.md) | 

## <a name="modules-for-building-and-evaluating-models"></a>Moduler för att skapa och utvärdera modeller

| Funktioner | Beskrivning | Modul |
| --- |--- | --- |
| Modellträning | Kör data via algoritmen. |  [Träna kluster modell](train-clustering-model.md) <br/> [Träna modell](train-model.md)  <br/> [Finjustera modellens egenskaper](tune-model-hyperparameters.md) |
| Bedömnings-och utvärderings modell | Mät noggrannheten för den tränade modellen. | [Tillämpa omvandling](apply-transformation.md) <br/> [Tilldela data till kluster](assign-data-to-clusters.md) <br/> [Kors validerings modell](cross-validate-model.md) <br/> [Utvärdera modell](evaluate-model.md) <br/> [Poäng modell](score-model.md) |
| Python-språk | Skriv kod och bädda in den i en modul för att integrera python med din pipeline. | [Skapa python-modell](create-python-model.md) <br/> [Kör Python-skript](execute-python-script.md) |
| R-språk | Skriv kod och bädda in den i en modul för att integrera R med din pipeline. | [Kör R-skript](execute-r-script.md) |
| Textanalys | Tillhandahåll specialiserade beräknings verktyg för att arbeta med både strukturerad och ostrukturerad text. | [Extrahera N g-funktioner från text](extract-n-gram-features-from-text.md) <br/> [Hashing av funktioner](feature-hashing.md) <br/> [Förbearbeta text](preprocess-text.md) |
| Rekommendation | Bygg rekommendationers modeller. | [Utvärdera rekommendation](evaluate-recommender.md) <br/> [Poäng för SVD-rekommendation](score-svd-recommender.md) <br/> [Träna SVD-rekommendation](train-SVD-recommender.md) |
| Avvikelseidentifiering | Bygg modeller för avvikelse identifiering. | [PCA-baserad avvikelse identifiering](pca-based-anomaly-detection.md) <br/> [Träna avvikelse identifierings modell](train-anomaly-detection-model.md) |

## <a name="error-messages"></a>Felmeddelanden

Lär dig mer om [fel meddelanden och undantags koder](designer-error-codes.md) som du kan stöta på med moduler i Azure Machine Learning designer.

## <a name="next-steps"></a>Nästa steg

* [Självstudie: Bygg en modell i designern för att förutsäga automatiska priser](../tutorial-designer-automobile-price-train-score.md)
