---
title: Algoritm- och modulreferens
description: Lär dig mer om de moduler som finns i Azure Machine Learning designer (förhandsversion)
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: peterclu
ms.author: peterlu
ms.date: 02/22/2020
ms.openlocfilehash: 7fcfc7c15f94d7b569629e53534d731126172260
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399067"
---
# <a name="algorithm--module-reference-for-azure-machine-learning-designer-preview"></a>Algoritm & modulreferens för Azure Machine Learning designer (förhandsversion)

Det här referensinnehållet ger den tekniska bakgrunden för var och en av maskininlärningsalgoritmerna och modulerna som är tillgängliga i Azure Machine Learning designer (förhandsversion).

Varje modul representerar en uppsättning kod som kan köras oberoende av varandra och utföra en maskininlärningsuppgift, med tanke på de indata som krävs. En modul kan innehålla en viss algoritm eller utföra en uppgift som är viktig i maskininlärning, till exempel värdeersättning som saknas eller statistisk analys.

Om du vill ha hjälp med att välja algoritmer finns i 
* [Så här väljer du algoritmer](../how-to-select-algorithms.md)
* [Azure Machine Learning Algoritm lathund](../algorithm-cheat-sheet.md)

> [!TIP]
> I alla pipeline i designern kan du få information om en viss modul. Markera modulen och välj sedan länken **mer hjälp** i **snabbhjälpsfönstret.**

## <a name="data-preparation-modules"></a>Moduler för förberedelse av data


| Funktioner | Beskrivning | Modul |
| --- |--- | --- |
| Indata och utdata | Flytta data från molnkällor till pipelinen. Skriv dina resultat eller mellanliggande data till Azure Storage, en SQL-databas eller Hive, medan du kör en pipeline, eller använd molnlagring för att utbyta data mellan pipelines.  | [Ange data manuellt](enter-data-manually.md) <br/> [Exportera data](export-data.md) <br/> [Importera data](import-data.md) |
| Dataomvandling | Åtgärder på data som är unika för maskininlärning, till exempel normalisera eller binning data, dimensionalitet minskning och konvertera data mellan olika filformat.| [Lägga till kolumner](add-columns.md) <br/> [Lägga till rader](add-rows.md) <br/> [Använda matematisk åtgärd](apply-math-operation.md) <br/> [Använda SQL-transformering](apply-sql-transformation.md) <br/> [Rensa data som saknas](clean-missing-data.md) <br/> [Beskärningsvärden](clip-values.md) <br/> [Konvertera till CSV](convert-to-csv.md) <br/> [Konvertera till datamängd](convert-to-dataset.md) <br/> [Konvertera till indikatorvärden](convert-to-indicator-values.md) <br/> [Redigera metadata](edit-metadata.md) <br/> [Koppla data](join-data.md) <br/> [Normalisera Data](normalize-data.md) <br/> [Partitionera och prova](partition-and-sample.md)  <br/> [Ta bort dubblettrader](remove-duplicate-rows.md) <br/> [SMOTE](smote.md) <br/> [Välja kolumntranformering](select-columns-transform.md) <br/> [Välja kolumner i datauppsättning](select-columns-in-dataset.md) <br/> [Dela data](split-data.md) |
| Val av funktion | Välj en delmängd av relevanta, användbara funktioner som ska användas för att skapa en analytisk modell. | [Filterbaserat funktionsval](filter-based-feature-selection.md) <br/> [Permutationfunktionsprioritet](permutation-feature-importance.md) |
| Statistiska funktioner | Tillhandahålla ett brett utbud av statistiska metoder relaterade till datavetenskap. | [Sammanfatta data](summarize-data.md)|

## <a name="machine-learning-algorithms"></a>Maskininlärningsalgoritmer

| Funktioner | Beskrivning | Modul |
| --- |--- | --- |
| Regression | Förutsäga ett värde. | [Förbättrad regression för beslutsträd](boosted-decision-tree-regression.md) <br/> [Regression för beslutsskog](decision-forest-regression.md) <br/> [Linjär regression](linear-regression.md)  <br/> [Regression för Neural Network](neural-network-regression.md)  <br/> |
| Klustring | Gruppera data tillsammans.| [K-means-klustring](k-means-clustering.md)
| Klassificering | Förutsäg en klass.  Välj mellan binära (tvåklass- eller flerklassalgoritmer).| [Förbättrat beslutsträd med flera klasser](multiclass-boosted-decision-tree.md) <br/> [Beslutsskog med två klasser](multiclass-decision-forest.md) <br/> [Logistic Regression med flera klasser](multiclass-logistic-regression.md)  <br/> [Neural Network med flera klasser](multiclass-neural-network.md) <br/> [En mot alla multiklass](one-vs-all-multiclass.md) <br/> [Genomsnittsperceptron med två klasser](two-class-averaged-perceptron.md) <br/>  [Tvåklassade förstärkta beslutsträd](two-class-boosted-decision-tree.md)  <br/> [Beslutsskog med två klasser](two-class-decision-forest.md) <br/>  [Logistic Regression med två klasser](two-class-logistic-regression.md) <br/> [Neural Network med två klasser](two-class-neural-network.md) <br/> [Tvåklassig dator för vektorstöd](two-class-support-vector-machine.md) | 

## <a name="modules-for-building-and-evaluating-models"></a>Moduler för att bygga och utvärdera modeller

| Funktioner | Beskrivning | Modul |
| --- |--- | --- |
| Modellträning | Kör data genom algoritmen. |  [Träna klustringsmodellen](train-clustering-model.md) <br/> [Träningsmodell](train-model.md)  <br/> [Finjustera hyperparametrar för modell](tune-model-hyperparameters.md) |
| Modellbedömning och utvärdering | Mät noggrannheten hos den tränade modellen. | [Använda transformering](apply-transformation.md) <br/> [Tilldela data till kluster](assign-data-to-clusters.md) <br/> [Korsvalidera modell](cross-validate-model.md) <br/> [Utvärdera modell](evaluate-model.md) <br/> [Poängmodell](score-model.md) |
| Python-språk | Skriv kod och bädda in den i en modul för att integrera Python med din pipeline. | [Skapa Python-modell](create-python-model.md) <br/> [Köra Python-skript](execute-python-script.md) |
| R-språk | Skriv kod och bädda in den i en modul för att integrera R med din pipeline. | [Köra R-skript](execute-r-script.md) |
| Textanalys | Tillhandahålla specialiserade beräkningsverktyg för att arbeta med både strukturerad och ostrukturerad text. | [Extrahera N-Gram-funktioner från text](extract-n-gram-features-from-text.md) <br/> [Funktions-hash](feature-hashing.md) <br/> [Förbearbeta text](preprocess-text.md) <br/> [Latent Dirichlet Tilldelning](latent-dirichlet-allocation.md) |
| Rekommendation | Bygg rekommendationsmodeller. | [Utvärdera rekommenderare](evaluate-recommender.md) <br/> [Poäng för SVD-rekommenderare](score-svd-recommender.md) <br/> [Träna SVD-rekommenderare](train-SVD-recommender.md) |
| Avvikelseidentifiering | Skapa avvikelseidentifieringsmodeller. | [Identifiering av PCA-baserad avvikelse](pca-based-anomaly-detection.md) <br/> [Identifieringsmodell för tågavvikelse](train-anomaly-detection-model.md) |


## <a name="error-messages"></a>Felmeddelanden

Lär dig mer om [felmeddelanden och undantagskoder](designer-error-codes.md) som kan uppstå med hjälp av moduler i Azure Machine Learning-designern.

## <a name="next-steps"></a>Nästa steg

* [Handledning: Bygg en modell i designer för att förutsäga auto priser](../tutorial-designer-automobile-price-train-score.md)
