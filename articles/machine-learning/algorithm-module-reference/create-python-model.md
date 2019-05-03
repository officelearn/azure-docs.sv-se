---
title: 'Skapa Python-modell: Modulreferens'
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder modellen skapa Python-modell i Azure Machine Learning-tjänsten för att skapa anpassade modellering eller bearbetning av modulen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ea9b50cede3e2d264ca0476b6a987ca6896c3c79
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029752"
---
# <a name="create-python-model"></a>Skapa Python-modell

Den här artikeln beskriver hur du använder den **skapa Python-modell** modul för att skapa en modell för omdöme från ett Python-skript. 

Du kan basera modellen på alla learner som ingår i ett Python-paket i Azure Machine Learning-miljö. 

När du skapar modellen kan du använda [träna modell](train-model.md) som andra learner i Azure Machine Learning för att träna modellen på en datauppsättning. Den tränade modellen kan skickas till [Poängmodell](score-model.md) du använder modellen för att göra förutsägelser. Den tränade modellen kan sedan sparas och bedömnings arbetsflödet kan publiceras som en webbtjänst.

> [!WARNING]
> För närvarande går det inte att skicka poängsatta resultatet av en Python-modell till [utvärdera modell](evaluate-model.md). Om du vill utvärdera en modell kan du skriva anpassad Python-skript och kör den med hjälp av den [kör Python-skript](execute-python-script.md) modulen.  


## <a name="how-to-configure-create-python-model"></a>Så här konfigurerar du skapa Python-modell

Användningen av den här modulen kräver mellanliggande eller expert kunskaper om Python. Användning av alla learner som ingår i Python-paket som redan är installerad i Azure Machine Learning har stöd för modulen. Se förinstallerade Python paketlistan i [kör Python-skript](execute-python-script.md).
  

Den här artikeln beskriver hur du använder den **skapa Python-modell** med ett enkelt experiment. Nedan visas i diagrammet av experimentet.

![Skapa-python-modell](./media/module/aml-create-python-model.png)

1.  Klicka på **skapa Python-modell**, redigera skriptet för att implementera din modellering eller bearbeta datahantering. Du kan basera modellen på alla learner som ingår i ett Python-paket i Azure Machine Learning-miljö.


    Nedan följer ett kodexempel i två Naive Bayes klassificerare med hjälp av populära *sklearn* paketet.

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


2. Anslut den **skapa Python-modell** modulen som du nyss skapade till en **Träningsmodell** och **poängsätta modell**

3. Om du vill utvärdera modellen kan du lägga till en [kör Python-skript](execute-python-script.md) och redigera Python-skriptet för att implementera utvärdering.

Nedan visas exempelkod för utvärdering.

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
