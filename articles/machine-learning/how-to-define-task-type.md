---
title: Definiera en maskin inlärnings uppgift för en automatiserad Machine Learning-körning
titleSuffix: Azure Machine Learning
description: Lär dig hur du definierar en maskin inlärnings uppgift för en automatiserad Machine Learning-körning
services: machine-learning
author: RachelKellam
ms.author: rakellam
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 11/04/2019
ms.openlocfilehash: a58959904559cd3b2cec27762f2df93cfea72abd
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/28/2019
ms.locfileid: "75540534"
---
# <a name="how-to-define-a-machine-learning-task"></a>Definiera en maskin inlärnings uppgift 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här artikeln får du lära dig vilka Machine Learning-uppgifter som stöds och hur du definierar dem för att köra en automatiserad ML-experiment (Automatisk maskin inlärning).


## <a name="What is a machine learning task?"></a>Vad är en maskin inlärnings uppgift?

En Machine Learning-uppgift representerar den typ av problem som löses genom att skapa en förutsägelse modell. Automatisk ML stöder tre olika typer av uppgifter, till exempel klassificering, regression och tids serie prognoser.

Uppgifts typ| Beskrivning| Exempel
----|----|----
Klassificering | Uppgift för att förutsäga kategorin för en viss rad i en data uppsättning. | Bedrägeri identifiering på ett kredit kort. Mål kolumnen skulle vara **bedrägerier identifierad** med kategorier som är *True* eller *false*. I det här fallet klassificerar vi varje rad i data som antingen sant eller falskt.
Regression | Uppgift för förutsägelse av utdata för kontinuerlig kvantitet. | Automobile-kostnader baserat på dess funktioner blir mål kolumnen **pris**.
Prognoser |Uppgift för att göra välgrundade uppskattningar i att fastställa riktningen för framtida trender.| Prognostisera energi efter frågan för de kommande 48 timmarna. Mål kolumnen skulle bli **efter frågan** och de förväntade värdena skulle användas för att visa mönster i energi efter frågan.

Automatisk ML stöder följande algoritmer under Automation-och fin justerings processen. Som en användare finns det inget behov av att ange algoritmen.

Klassificering | Regression | Prognosticering för tids serier
-- |-- |--
[Logistic Regression](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)| [Elastisk Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)| [Elastisk Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Ljus GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Ljus GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Ljus GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[Gradient Boosting](https://scikit-learn.org/stable/modules/ensemble.html#classification)|[Gradient Boosting](https://scikit-learn.org/stable/modules/ensemble.html#regression)|[Gradient Boosting](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Beslutsträd](https://scikit-learn.org/stable/modules/tree.html#decision-trees)|[Beslutsträd](https://scikit-learn.org/stable/modules/tree.html#regression)|[Beslutsträd](https://scikit-learn.org/stable/modules/tree.html#regression)
[K närmaste grannar](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K närmaste grannar](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K närmaste grannar](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[Linjär SVC](https://scikit-learn.org/stable/modules/svm.html#classification)|[Dell Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)|[Dell Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[C-Support Vector klassificering (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)|[Stokastisk brantaste Lutningsmetoden (Descent)](https://scikit-learn.org/stable/modules/sgd.html#regression)|[Stokastisk brantaste Lutningsmetoden (Descent)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Slumpmässig skog](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Slumpmässig skog](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Slumpmässig skog](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Extremt slumpmässig träd](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Extremt slumpmässig träd](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Extremt slumpmässig träd](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)|[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)| [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[DNN-klassificerare](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier)|[DNN modellerings regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [DNN modellerings regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[DNN linjär klassificerare](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[Linjär modellerings regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)|[Linjär modellerings regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Naive Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)||
[Stokastisk brantaste Lutningsmetoden (Descent)](https://scikit-learn.org/stable/modules/sgd.html#sgd)||


### <a name="set-the-task-type"></a>Ange aktivitets typ
Du kan ställa in aktivitets typen för dina automatiserade ML-experiment med SDK eller Azure Machine Learning Studio.

Använd `task`-parametern i konstruktorn `AutoMLConfig` för att ange din experiment typ.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task="classification")
```

Du kan ställa in uppgiften som en del av din automatiserade ML experiment körning skapa i Azure Machine Learning Studio. 

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku-inline.md)]

![Val av aktivitets typ](./media/how-to-define-task-type/task-type.png)


## <a name="next-steps"></a>Nästa steg

+ Läs mer om [automatiserad ml](concept-automated-ml.md) i Azure Machine Learning.
+ Lär dig mer om [Automatisk utbildning en prognos modell för tids serier](how-to-auto-train-forecast.md) i Azure Machine Learning
+ Testa själv studie kursen om [automatiserad Machine Learning klassificering](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/model-explanation) .
+ Prova den [automatiska Machine Learning Regressions](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/model-explanation) exempel antecknings boken.

