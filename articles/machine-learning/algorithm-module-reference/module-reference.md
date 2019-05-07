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
ms.openlocfilehash: 8825f1dc3b66a5c4981ba25a90813aec63975b1f
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65145138"
---
# <a name="algorithm--module-reference-overview"></a>Algoritm- och modulreferens referens-översikt

Den här Referensinnehåll innehåller de tekniska förutsättningarna för var och en av de machine learning-algoritmer och moduler som är tillgängliga i det visuella gränssnittet (förhandsversion) för Azure Machine Learning-tjänsten.

Varje modul representerar en uppsättning kod som kan köras oberoende av varandra och utföra en machine learning-aktivitet, med indata som krävs. En modul kan innehålla en viss algoritm eller utföra en uppgift som är viktigt för Machine learning, till exempel saknas värdet ersättning eller statistiska analyser.

> [!TIP]
> Du kan få information om en specifik modul i alla experiment i det visuella gränssnittet. Välj modulen och välj sedan den **mer hjälp** länken i den **snabb hjälp** fönstret.

## <a name="modules"></a>Moduler

Moduler som är ordnade efter funktioner:

| Funktioner | Beskrivning | Modul |
| --- |--- | ---- |
| Dataformatkonverteringar | Konvertera data mellan olika filformat som används i machine learning | [Konvertera till CSV](convert-to-csv.md) |
| Data indata och utdata | Flytta data från molntjänster i experimentet. Skriv din resultatet eller mellanliggande data till Azure Storage, en SQL-databas eller Hive, samtidigt som du kör ett experiment, eller använda molnlagring för att utbyta data mellan experiment.  | [Importera Data](import-data.md)<br/>[Exportera Data](export-data.md)<br/>[Ange Data manuellt](enter-data-manually.md) |
| Dataomvandling | Åtgärder på data som är unika för maskininlärning, till exempel normaliserar eller datagruppering data, val av funktioner och minskar dimensionalitet.| [Välj kolumner i datauppsättning](select-columns-in-dataset.md) <br/> [Edit Metadata](edit-metadata.md) <br/> [Rensa Data som saknas](clean-missing-data.md) <br/> [Lägga till kolumner](add-columns.md) <br/> [Lägga till rader](add-rows.md) <br/> [Ta bort dubblettrader](remove-duplicate-rows.md) <br/> [Split Data](split-data.md) <br/> [Normalisera Data](normalize-data.md) <br/> [Partitionera och ta prover](partition-and-sample.md) |
| Python-modulen | Skriva kod och bädda in den i en modul för att integrera Python med experimentet. | [Köra Python-skriptet](execute-python-script.md)   <br/> [Skapa Python-modell](create-python-model.md)
|  | **Machine learning-algoritmer**: | |
| Klassificering | Förutsäga en klass.  Välj bland binary (tvåklassförhöjt) eller inom algoritmer.| [Beslutsskog med multiclass](multiclass-decision-forest.md) <br/> [Inom Logistic Regression](multiclass-logistic-regression.md)  <br/> [Inom Neuralt nätverk](multiclass-neural-network.md)  <br/>  [Två Logistic Regression](two-class-logistic-regression.md)  <br/>[Två genomsnitt Perceptron](two-class-averaged-perceptron.md) <br/> [Två&nbsp;förstärkta&nbsp;beslut&nbsp;trädet](two-class-boosted-decision-tree.md)  <br/> [Beslutsskog med två](two-class-decision-forest.md)  <br/> [Två Neuralt nätverk](two-class-neural-network.md)  <br/> [Två&#8209;klass&nbsp;Support&nbsp;vektor&nbsp;dator](two-class-support-vector-machine.md) 
| Klustring | Gruppera data.| [K-Means klustring](k-means-clustering.md)
| Regression | Förutsäga ett värde. | [Linjär Regression](linear-regression.md)  <br/> [Neuralt nätverk Regression](neural-network-regression.md)  <br/> [Beslutsskog Regression](decision-forest-regression.md)  <br/> [Förstärkta&nbsp;beslut&nbsp;trädet&nbsp;Regression](boosted-decision-tree-regression.md)
|  | **Bygga och utvärdera modeller**: | |
| Träna   | Kör data via algoritmen. | [Träna modell](train-model.md)  <br/> [Träna Clustering-modellen](train-clustering-model.md)    |
| Utvärdera modell | Mät det arbete du utfört den tränade modellen. |  [Utvärdera modellen](evaluate-model.md)
| Poäng | Få förutsägelser från modellen som du precis har tränat. | [Använda transformering](apply-transformation.md)<br/>[Tilldela&nbsp;Data&nbsp;till&nbsp;kluster](assign-data-to-clusters.md) <br/>[Poängsätta modell](score-model.md)

## <a name="error-messages"></a>Felmeddelanden

Lär dig mer om den [felmeddelanden och undantag koder](machine-learning-module-error-codes.md) som kan uppstå med moduler i det visuella gränssnittet i Azure Machine Learning-tjänsten.
