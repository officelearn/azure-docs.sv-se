---
title: 'Skapa Python-modell: Modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen Skapa Python-modell i Azure Machine Learning för att skapa en anpassad modell- eller databehandlingsmodul.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: c8be0882452dc120f538394a5481769e26e3fa15
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682811"
---
# <a name="create-python-model-module"></a>Skapa Python-modellmodul

I den här artikeln beskrivs en modul i Azure Machine Learning designer (förhandsversion).

Lär dig hur du använder modulen Skapa Python-modell för att skapa en otränad modell från ett Python-skript. Du kan basera modellen på alla elever som ingår i ett Python-paket i designermiljön Azure Machine Learning. 

När du har skapat modellen kan du använda [Train Model](train-model.md) för att träna modellen på en datauppsättning, som alla andra elever i Azure Machine Learning. Den tränade modellen kan skickas till [Poängmodell för](score-model.md) att göra förutsägelser. Du kan sedan spara den tränade modellen och publicera bedömningsarbetsflödet som en webbtjänst.

> [!WARNING]
> För närvarande är det inte möjligt att skicka de poängsatta resultaten av en Python-modell för att [utvärdera modell](evaluate-model.md). Om du behöver utvärdera en modell kan du skriva ett anpassat Python-skript och köra det med modulen [Kör Python-skript.](execute-python-script.md)  


## <a name="configure-the-module"></a>Konfigurera modulen

Användning av den här modulen kräver mellanliggande eller expertkunskaper om Python. Modulen stöder användning av alla elever som ingår i Python-paket som redan är installerade i Azure Machine Learning. Se den förinstallerade Python-paketlistan i [Kör Python Script](execute-python-script.md).

> [!NOTE]
> Var mycket försiktig när du skriver skriptet och ser till att det inte finns något syntaxfel, till exempel att använda ett odeklarerat objekt eller en icke-importerad modul.

> [!NOTE]
Ägna också extra uppmärksamhet åt den förinstallerade modulens listan i [Kör Python Script](execute-python-script.md). Importera endast förinstallerade moduler. Installera inte extra paket som "pip installera xgboost" i det här skriptet, annars fel kommer att höjas när du läser modeller i ned-stream moduler.
  
Den här artikeln visar hur du använder **Skapa Python-modell** med en enkel pipeline. Här är ett diagram över rörledningen:

![Diagram över Skapa Python-modell](./media/module/create-python-model.png)

1. Välj **Skapa Python-modell**och redigera skriptet för att implementera din modellerings- eller datahanteringsprocess. Du kan basera modellen på alla elever som ingår i ett Python-paket i Azure Machine Learning-miljön.

> [!NOTE]
> Var extra uppmärksam på kommentarerna i exempelkoden för skriptet och se till att skriptet strikt följer kravet, inklusive klassnamn, metoder samt metodsignatur. Överträdelse kommer att leda till undantag. 

   Följande exempelkod för klassificeraren Med två klasser Av Naive Bayes används det populära *sklearnpaketet:*

   ```Python

   # The script MUST define a class named AzureMLModel.
   # This class MUST at least define the following three methods:
       # __init__: in which self.model must be assigned,
       # train: which trains self.model, the two input arguments must be pandas DataFrame,
       # predict: which generates prediction result, the input argument and the prediction result MUST be pandas DataFrame.
   # The signatures (method names and argument names) of all these methods MUST be exactly the same as the following example.

   # Please do not install extra packages such as "pip install xgboost" in this script,
   # otherwise errors will be raised when reading models in down-stream modules.
   
   import pandas as pd
   from sklearn.naive_bayes import GaussianNB


   class AzureMLModel:
       def __init__(self):
           self.model = GaussianNB()
           self.feature_column_names = list()

       def train(self, df_train, df_label):
           # self.feature_column_names records the column names used for training.
           # It is recommended to set this attribute before training so that the
           # feature columns used in predict and train methods have the same names.
           self.feature_column_names = df_train.columns.tolist()
           self.model.fit(df_train, df_label)

       def predict(self, df):
           # The feature columns used for prediction MUST have the same names as the ones for training.
           # The name of score column ("Scored Labels" in this case) MUST be different from any other columns in input data.
           return pd.DataFrame(
               {'Scored Labels': self.model.predict(df[self.feature_column_names]), 
                'probabilities': self.model.predict_proba(df[self.feature_column_names])[:, 1]}
           )


   ```

1. Anslut modulen **Skapa Python-modell** som du just skapade för att **träna modell-** och **poängmodell**.

1. Om du behöver utvärdera modellen lägger du till en [Kör Python Script-modul](execute-python-script.md) och redigerar Python-skriptet.

   Följande skript är exempelutvärderingskod:

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

## <a name="next-steps"></a>Nästa steg

Se uppsättningen [moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 