---
title: Definiera en maskininlärningsuppgift för en automatiserad maskininlärningskörning
titleSuffix: Azure Machine Learning
description: Lär dig hur du definierar en maskininlärningsuppgift för en automatiserad maskininlärningskörning
services: machine-learning
author: RachelKellam
ms.author: rakellam
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 11/04/2019
ms.openlocfilehash: fef3f0347d963b8a7d5fe2f1ca771293e9c3d2a9
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80475500"
---
# <a name="how-to-define-a-machine-learning-task"></a>Så här definierar du en maskininlärningsuppgift 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här artikeln får du lära dig de maskininlärningsuppgifter som stöds och hur du definierar dem för en automatisk datorinlärningskörning (automated ML).


## <a name="what-is-a-machine-learning-task"></a><a name="What is a machine learning task?"></a>Vad är en maskininlärningsuppgift?

En maskininlärningsuppgift representerar den typ av problem som löses genom att skapa en förutsägande modell. Automatiserad ML stöder tre olika typer av uppgifter, inklusive klassificering, regression och tidsserieprognoser.

Uppgiftstyp| Beskrivning| Exempel
----|----|----
Klassificering | Uppgift för att förutsäga kategorin för en viss rad i en datauppsättning. | Upptäckt av bedrägerier på ett kreditkort. Målkolumnen är **Bedrägeri som upptäckts** med kategorier av *Sant* eller *Falskt*. I det här fallet klassificerar vi varje rad i data som antingen sant eller falskt.
Regression | Uppgift för att förutsäga en kontinuerlig kvantitetsutflöde. | Bil kostnad baserat på dess funktioner, skulle målet kolumnen vara **pris**.
Prognosticering |Uppgift att göra välgrundade uppskattningar för att fastställa riktningen för framtida trender.| Prognostisera efterfrågan på energi för dem nästa 48 timmar. Målkolumnen skulle vara **efterfrågan** och de förväntade värdena skulle användas för att visa mönster i energibehovet.

Automatiserad ML stöder följande algoritmer under automatiserings- och justeringsprocessen. Som användare behöver du inte ange algoritmen.

Klassificering | Regression | Prognostisering för tidsserier
-- |-- |--
[Logistisk regression](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)| [Elastiskt nät](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)| [Elastiskt nät](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Ljus GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Ljus GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Ljus GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[Uppbackning av övertoning](https://scikit-learn.org/stable/modules/ensemble.html#classification)|[Uppbackning av övertoning](https://scikit-learn.org/stable/modules/ensemble.html#regression)|[Uppbackning av övertoning](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Beslut Träd](https://scikit-learn.org/stable/modules/tree.html#decision-trees)|[Beslut Träd](https://scikit-learn.org/stable/modules/tree.html#regression)|[Beslut Träd](https://scikit-learn.org/stable/modules/tree.html#regression)
[K Närmaste grannar](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K Närmaste grannar](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K Närmaste grannar](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[Linjär SVC](https://scikit-learn.org/stable/modules/svm.html#classification)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[C-stöd vektorklassificering (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)|[Stokastisk lutningsnedstigning (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)|[Stokastisk lutningsnedstigning (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Slumpmässig skog](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Slumpmässig skog](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Slumpmässig skog](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Extremt randomiserade träd](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Extremt randomiserade träd](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Extremt randomiserade träd](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)|[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)| [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[DNN-klassificerare](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier)|[DNN Regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [DNN Regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[KLASSIFICERARE FÖR DNN-linjära](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[Linjär regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)|[Linjär regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Naive Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)||
[Stokastisk lutningsnedstigning (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)||


### <a name="set-the-task-type"></a>Ange uppgiftstyp
Du kan ange uppgiftstypen för dina automatiserade ML-experiment med SDK eller Azure Machine Learning-studion.

Använd `task` parametern `AutoMLConfig` i konstruktorn för att ange experimenttypen.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task="classification")
```

Du kan ange uppgiften som en del av din automatiska ML-experimentkörning som skapas i Azure Machine Learning-studion. 

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku-inline.md)]

![Val av aktivitetstyp](./media/how-to-define-task-type/task-type.png)


## <a name="next-steps"></a>Nästa steg

+ Läs mer om [automatiserad ml](concept-automated-ml.md) i Azure Machine Learning.
+ Läs mer om automatisk utbildning av en prognosmodell i [tidsserier](how-to-auto-train-forecast.md) i Azure Machine Learning
+ Prova [självstudiekursen för automatisk maskininlärningsklassificering.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb)
+ Prova exempelboken [för automatisk regressionsprov för maskininlärning.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression-hardware-performance-explanation-and-featurization/auto-ml-regression-hardware-performance-explanation-and-featurization.ipynb)

