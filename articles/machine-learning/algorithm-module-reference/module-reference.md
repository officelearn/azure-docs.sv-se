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
ms.date: 11/11/2019
ms.openlocfilehash: 938286f0dafdeb11473bef1b88f876d7918e76ca
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74012759"
---
# <a name="algorithm--module-reference"></a>Algoritm- och modulreferens

Det här referens innehållet ger teknisk bakgrund på var och en av Machine Learning-algoritmerna och modulerna som är tillgängliga i Azure Machine Learning designer (för hands version).

Varje modul representerar en uppsättning kod som kan köras separat och utföra en maskin inlärnings uppgift, med de nödvändiga inmatningarna. En modul kan innehålla en viss algoritm eller utföra en uppgift som är viktig i Machine Learning, till exempel värde utbyte som saknas eller statistisk analys.

> [!TIP]
> I en pipeline i designern kan du hämta information om en speciell modul. Välj modulen och välj sedan länken **mer hjälp** i fönstret **snabb hjälp** .

## <a name="modules"></a>Moduler

Moduler är ordnade efter funktion:

| Funktioner | Beskrivning | Modul |
| --- |--- | ---- |
| Data indata och utdata | Flytta data från moln källor till din pipeline. Skriv resultaten eller mellanliggande data till Azure Storage, en SQL-databas eller Hive, medan du kör en pipeline eller Använd moln lagring för att utbyta data mellan pipeliner.  | [Importera data](import-data.md) <br/> [Ange data manuellt](enter-data-manually.md) <br/>[Exportera data](export-data.md) |
| Dataomvandling | Åtgärder för data som är unika för maskin inlärning, till exempel normaliserande eller diskretisering data, Dimensional reducering och konvertering av data mellan olika fil format.| [Lägg till kolumner](add-columns.md) <br/> [Lägg till rader](add-rows.md) <br/> [Rensa saknade data](clean-missing-data.md) <br/> [Konvertera till CSV](convert-to-csv.md) <br/> [Konvertera till data uppsättning](convert-to-dataset.md) <br/> [Redigera metadata](edit-metadata.md) <br/> [Koppla data](join-data.md) <br/> [Normalisera data](normalize-data.md) <br/> [Ta bort dubblettrader](remove-duplicate-rows.md) <br/> [Välj kolumner-transformering](select-columns-transform.md) <br/> [Välj kolumner i data uppsättning](select-columns-in-dataset.md) |
| Samling | Dela dina data i en eller flera del mängder för att träna och testa Machine Learning-modeller.  | [Kors validerings modell](cross-validate-model.md) <br/> [Partition och exempel](partition-and-sample.md) <br/> [SMOTE](smote.md) <br/> [Dela data](split-data.md) |
| Val av funktion | Välj en delmängd av relevanta, användbara funktioner som du kan använda när du skapar en analys modell. | [Filtrera baserat funktions val](filter-based-feature-selection.md) <br/> [Viktighets funktion för permutation](permutation-feature-importance.md) |
| Python och R | Skriv kod och bädda in den i en modul för att integrera python och R med din pipeline. | [Skapa python-modell](create-python-model.md) <br/> [Kör Python-skript](execute-python-script.md)   <br/>  [Kör R-skript](execute-r-script.md)
| Textanalys | Tillhandahåll specialiserade beräknings verktyg för att arbeta med både strukturerad och ostrukturerad text. | [Extrahera N g-funktioner från text](extract-n-gram-features-from-text.md) <br/> [Hashing av funktioner](feature-hashing.md) <br/> [Förbearbeta text](preprocess-text.md) |
|  | **Machine Learning-algoritmer**: | |
| Klassificering | Förutsäg en klass.  Välj mellan binära (två klasser) eller algoritmer för multiklasser.| [Besluts skog med flera klasser](multiclass-decision-forest.md) <br/> [Besluts träd med djup klass](multiclass-boosted-decision-tree.md) <br/> [Logistik regression med multiklass](multiclass-logistic-regression.md)  <br/> [Neurala nätverk i multiklass](multiclass-neural-network.md) <br/> [En vs. alla multiklass](one-vs-all-multiclass.md) <br/>  [Logistik regression med två klasser](two-class-logistic-regression.md)  <br/>[Genomsnittlig Perceptron i två klasser](two-class-averaged-perceptron.md) <br/> [Besluts träd med två klasser](two-class-boosted-decision-tree.md)  <br/> [Besluts skog med två klasser](two-class-decision-forest.md)  <br/> [Neurala nätverk med två klasser](two-class-neural-network.md) <br/> [Två klass stöd för Vector Machine](two-class-support-vector-machine.md) | 
| Redundanskluster | Gruppera data tillsammans.| [K-innebär klustring](k-means-clustering.md)
| Regression | Förutsäg ett värde. | [Regressions analys av besluts träd](boosted-decision-tree-regression.md) <br/> [Besluts skogs regression](decision-forest-regression.md) <br/> [Linjär regression](linear-regression.md)  <br/> [Neurala nätverks regression](neural-network-regression.md)  <br/> |
| Rekommenderare | Bygg rekommendationers modeller. | [Utvärdera rekommendation](evaluate-recommender.md) <br/> [Poäng för SVD-rekommendation](score-svd-recommender.md) <br/> [Träna SVD-rekommendation](train-SVD-recommender.md) |
|  | **Bygga och utvärdera modeller**: | |
| Träna   | Kör data via algoritmen. | [Träna modell](train-model.md)  <br/> [Träna kluster modell](train-clustering-model.md) <br/>  [Finjustera modellens egenskaper](tune-model-hyperparameters.md) |
| Utvärdera modell | Mät noggrannheten för den tränade modellen. |  [Utvärdera modell](evaluate-model.md) |
| Poäng | Hämta förutsägelser från modellen som du precis har tränat. | [Tillämpa omvandling](apply-transformation.md)<br/>[Tilldela data till kluster](assign-data-to-clusters.md) <br/>[Poäng modell](score-model.md) |
| Statistiska funktioner | Tillhandahålla en mängd olika statistiska metoder för data vetenskap. | [Använd matematik åtgärd](apply-math-operation.md) <br/> [Sammanfatta data](summarize-data.md)|

## <a name="error-messages"></a>Felmeddelanden

Lär dig mer om [fel meddelanden och undantags koder](machine-learning-module-error-codes.md) som du kan stöta på med moduler i Azure Machine Learning designer.
