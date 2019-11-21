---
title: 'Skapa python-modell: modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder Create python modell-modellen i Azure Machine Learning för att skapa anpassade modeller eller data bearbetnings moduler.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 11/19/2019
ms.openlocfilehash: 0c1a4f33da7e1f39951d641ed1d563c46fb664ca
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232653"
---
# <a name="create-python-model"></a>Skapa Python-modell

I den här artikeln beskrivs en modul i Azure Machine Learning designer (för hands version).

Lär dig hur du använder modulen **skapa python-modell** för att skapa en modell som inte är tränad från ett Python-skript. Du kan basera modellen på valfri elev som ingår i ett python-paket i Azure Machine Learning designer-miljön. 

När du har skapat modellen kan du använda [träna modell](train-model.md) för att träna modellen på en data uppsättning, t. ex. en annan elev i Azure Machine Learning. Den tränade modellen kan skickas till [Poäng modell](score-model.md) för att använda modellen för att göra förutsägelser. Den tränade modellen kan sedan sparas och poängsättnings arbets flödet kan publiceras som en webb tjänst.

> [!WARNING]
> För närvarande går det inte att skicka resultatet av en python-modell för att [utvärdera modellen](evaluate-model.md). Om du behöver utvärdera en modell kan du skriva anpassat Python-skript och köra det med hjälp av modulen [Kör Python-skript](execute-python-script.md) .  


## <a name="how-to-configure-create-python-model"></a>Konfigurera skapande av python-modell

Användningen av den här modulen kräver en mellanliggande eller expert kunskap om python. Modulen stöder användning av valfri elev som ingår i python-paketen som redan har installerats i Azure Machine Learning. Se förinstallerade python-paket lista i [Kör Python-skript](execute-python-script.md).
  

I den här artikeln visas hur du använder **create python-modellen** med en enkel pipeline. Nedan visas diagrammet i pipelinen.

![Skapa python-modell](./media/module/aml-create-python-model.png)

1.  Klicka på **skapa python-modell**, redigera skriptet för att implementera modellerings-eller data hanterings processen. Du kan basera modellen på valfri elev som ingår i ett python-paket i Azure Machine Learnings miljön.


    Nedan visas en exempel kod för Naive Bayes-klassificeraren i två klasser med hjälp av det populära *sklearn* -paketet.

```Python

# The script MUST define a class named AzureMLModel.
# This class MUST at least define the following three methods:
    # __init__: in which self.model must be assigned,
    # train: which trains self.model, the two input arguments must be pandas DataFrame,
    # predict: which generates prediction result, the input argument and the prediction result MUST be pandas DataFrame.
# The signatures (method names and argument names) of all these methods MUST be exactly the same as the following example.


import pandas as pd
from sklearn.naive_bayes import GaussianNB


class AzureMLModel:
    def __init__(self):
        self.model = GaussianNB()
        self.feature_column_names = list()

    def train(self, df_train, df_label):
        self.feature_column_names = df_train.columns.tolist()
        self.model.fit(df_train, df_label)

    def predict(self, df):
        return pd.DataFrame(
            {'Scored Labels': self.model.predict(df[self.feature_column_names]), 
             'probabilities': self.model.predict_proba(df[self.feature_column_names])[:, 1]}
        )


```


2. Anslut den **skapa python modell** -modul som du nyss skapade **till en modell** och **Poäng modell**

3. Om du behöver utvärdera modellen lägger du till ett [Kör Python-skript](execute-python-script.md) och redigerar python-skriptet för att implementera utvärderingen.

Nedan visas en exempel utvärderings kod.

```Python


# The script MUST contain a function named azureml_main
# which is the entry point for this module.

# imports up here can be used to 
import pandas as pd

# The entry point function can contain up to two input arguments:
#   Param<dataframe1>: a pandas.DataFrame
#   Param<dataframe2>: a pandas.DataFrame
def azureml_main(dataframe1 = None, dataframe2 = None):
    
    from sklearn.metrics import accuracy_score, precision_score, recall_score, roc_auc_score, roc_curve
    import pandas as pd
    import numpy as np
    
    scores = dataframe1.ix[:, ("income", "Scored Labels", "probabilities")]
    ytrue = np.array([0 if val == '<=50K' else 1 for val in scores["income"]])
    ypred = np.array([0 if val == '<=50K' else 1 for val in scores["Scored Labels"]])    
    probabilities = scores["probabilities"]
    
    accuracy, precision, recall, auc = \
    accuracy_score(ytrue, ypred),\
    precision_score(ytrue, ypred),\
    recall_score(ytrue, ypred),\
    roc_auc_score(ytrue, probabilities)
    
    metrics = pd.DataFrame();
    metrics["Metric"] = ["Accuracy", "Precision", "Recall", "AUC"];
    metrics["Value"] = [accuracy, precision, recall, auc]
    
    return metrics,

```
