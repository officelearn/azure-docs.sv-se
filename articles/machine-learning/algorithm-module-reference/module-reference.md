---
title: Algoritm- och modulreferens
titleSuffix: Azure Machine Learning service
description: Lär dig mer om modulerna som är tillgängliga i det visuella gränssnittet i Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 6602eb4bacdc3b6382c1b6873a465cdfc0632693
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029347"
---
# <a name="algorithm--module-reference-overview"></a>Algoritm- och modulreferens referens-översikt

Den här Referensinnehåll innehåller de tekniska förutsättningarna för var och en av de machine learning-algoritmer och moduler som är tillgängliga i det visuella gränssnittet (förhandsversion) för Azure Machine Learning-tjänsten. 

Varje modul representerar en uppsättning kod som kan köras oberoende av varandra och utföra en machine learning-aktivitet, med indata som krävs. En modul kan innehålla en viss algoritm eller utföra en uppgift som är viktigt för Machine learning, till exempel saknas värdet ersättning eller statistiska analyser. 

> [!TIP]
> Du kan få information om en specifik modul i alla experiment i det visuella gränssnittet. Välj modulen och välj sedan den **mer hjälp** länken i den **snabb hjälp** fönstret.

Moduler som är ordnade efter funktioner:

**Format för datakonvertering**

  + [Konvertera till CSV ](convert-to-csv.md)

**Data som indata och utdata moduler** arbete för att flytta data från molntjänster i experimentet. Du kan skriva dina resultat eller mellanliggande data till Azure Storage, en SQL-databas eller Hive, samtidigt som du kör ett experiment eller använda molnlagring för att utbyta data mellan experiment.  

  + [Importera Data](import-data.md)

  + [Exportera Data](export-data.md)

  + [Ange Data manuellt](enter-data-manually.md)


**Moduler för omvandling av data** stöd för åtgärder på data som är unika för maskininlärning, till exempel normaliserar eller datagruppering data, val av funktioner och minskar dimensionalitet.

  + [Välj kolumner i datauppsättning](select-columns-in-dataset.md)

  + [Edit Metadata](edit-metadata.md)

  + [Rensa Data som saknas](clean-missing-data.md)

  + [Lägga till kolumner](add-columns.md)

  + [Lägga till rader](add-rows.md)

  + [Ta bort dubblettrader](remove-duplicate-rows.md)

  + [Split Data](split-data.md)

  + [Normalisera Data](normalize-data.md)

  + [Partitionera och ta prover](partition-and-sample.md)


**Machine learning-algoritmer** till exempel klustring, dator för vektorstöd eller neurala nätverk, är tillgängliga i enskilda moduler som du kan anpassa maskininlärning uppgiften med lämpliga parametrar.  
  + [Poängsätta modell](score-model.md)

  + [Tilldela Data till ett kluster ](assign-data-to-clusters.md)

  + [Träna modell](train-model.md)

  + [Träna Clustering-modellen](train-clustering-model.md)

  + [Utvärdera modellen](evaluate-model.md)

  + [Använda transformering](apply-transformation.md)

  + [Linjär Regression](linear-regression.md)

  + [Neuralt nätverk Regression](neural-network-regression.md)

  + [Beslutsskog Regression](decision-forest-regression.md)

  + [Regression för beslutsträd](boosted-decision-tree-regression.md)

  + [Tvåklassförhöjt beslutsträd](two-class-boosted-decision-tree.md)

  + [Två Logistic Regression](two-class-logistic-regression.md)

  + [Inom Logistic Regression](multiclass-logistic-regression.md)

  + [Inom Neuralt nätverk](multiclass-neural-network.md)

  + [Beslutsskog med multiclass](multiclass-decision-forest.md)

  + [Två genomsnitt Perceptron](two-class-averaged-perceptron.md)

  + [Beslutsskog med två](two-class-decision-forest.md)

  + [Två Neuralt nätverk](two-class-neural-network.md)

  + [Två-Class Support Vector Machine](two-class-support-vector-machine.md)
  
  + [K-Means klustring](k-means-clustering.md)


**Python-modulen** gör det enkelt att köra en anpassad funktion. Du skriva koden och bädda in den i en modul, integrera Python med tjänstens experiment.
  + [Köra Python-skriptet](execute-python-script.md)

  + [Skapa Python-modell](create-python-model.md)


