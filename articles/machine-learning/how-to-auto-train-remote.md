---
title: Automatiserade ML-fjärrberäkningsmål
titleSuffix: Azure Machine Learning
description: Lär dig hur du skapar modeller med automatiserad maskininlärning på ett azure machine learning-fjärrberäkningsmål med Azure Machine Learning
services: machine-learning
author: cartacioS
ms.author: sacartac
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: 9e499d609a3f78dc5f422b9ed90df09be30f2e7c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79080419"
---
# <a name="train-models-with-automated-machine-learning-in-the-cloud"></a>Träna modeller med automatiserad maskininlärning i molnet

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I Azure Machine Learning tränar du din modell på olika typer av beräkningsresurser som du hanterar. Beräkningsmålet kan vara en lokal dator eller en resurs i molnet.

Du kan enkelt skala upp eller skala ut ditt maskininlärningsexperiment genom att lägga till ytterligare beräkningsmål, till exempel Azure Machine Learning Compute (AmlCompute). AmlCompute är en hanterad beräkningsinfrastruktur där du enkelt kan skapa en beräkning med en enda eller flera noder.

I den här artikeln får du lära dig hur du skapar en modell med automatisk ML med AmlCompute.

## <a name="how-does-remote-differ-from-local"></a>Hur skiljer sig fjärrkontrollen från lokala?

Handledningen "[Train a classification model with automated machine learning](tutorial-auto-train-models.md)" lär dig hur man använder en lokal dator för att träna en modell med automatiserad ML. Arbetsflödet när utbildning lokalt gäller även för fjärrmål också. Men med fjärrberäkning körs automatiserade ML-experimentiterationer asynkront. Med den här funktionen kan du avbryta en viss iteration, titta på körningens status eller fortsätta att arbeta med andra celler i den jupyter anteckningsboken. Om du vill träna på distans skapar du först ett fjärrberäkningsmål, till exempel AmlCompute. Sedan konfigurerar du fjärrresursen och skickar koden dit.

Den här artikeln visar de extra steg som behövs för att köra ett automatiserat ML-experiment på ett avlägset AmlCompute-mål. Arbetsyteobjektet `ws`från självstudien används i hela koden här.

```python
ws = Workspace.from_config()
```

## <a name="create-resource"></a>Skapa resurs

Skapa [`AmlCompute`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute%28class%29?view=azure-ml-py) målet på arbetsytan`ws`( ) om det inte redan finns.

**Tidsuppskattning**: Skapandet av AmlCompute-målet tar cirka 5 minuter.

```python
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget
import os

# choose a name for your cluster
compute_name = os.environ.get("AML_COMPUTE_CLUSTER_NAME", "cpu-cluster")
compute_min_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MIN_NODES", 0)
compute_max_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MAX_NODES", 4)

# This example uses CPU VM. For using GPU VM, set SKU to STANDARD_NC6
vm_size = os.environ.get("AML_COMPUTE_CLUSTER_SKU", "STANDARD_D2_V2")


if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('found compute target. just use it. ' + compute_name)
else:
    print('creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size = vm_size,
                                                                min_nodes = compute_min_nodes, 
                                                                max_nodes = compute_max_nodes)

    # create the cluster
    compute_target = ComputeTarget.create(ws, compute_name, provisioning_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current AmlCompute status, use get_status()
    print(compute_target.get_status().serialize())
```

Du kan nu `compute_target` använda objektet som fjärrberäkningsmål.

Begränsningar för klusternamn omfattar:
+ Måste vara kortare än 64 tecken.
+ Det går inte att `\` inkludera något av följande tecken: ~ ! @ # $ % ^ & * ( ) = \\ \\ + _ [ ] { } | ; : \' \\" , < > / ?. `

## <a name="access-data-using-tabulardataset-function"></a>Få tillgång till data med funktionen Tabellformdatauppsättning

Definierade training_data som [`TabularDataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) och etiketten, som skickas till Automated [`AutoMLConfig`](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?view=azure-ml-py)ML i . Metoden `TabularDataset` `from_delimited_files`anger som standard `infer_column_types` värdet till true, som kommer att härleda kolumntypen automatiskt. 

Om du vill ställa in kolumntyperna manuellt kan `set_column_types` du ställa in argumentet så att du manuellt anger typen av varje kolumn. I följande kodexempel kommer data från sklearn-paketet.

```python
from sklearn import datasets
from azureml.core.dataset import Dataset
from scipy import sparse
import numpy as np
import pandas as pd
import os

# Create a project_folder if it doesn't exist
if not os.path.isdir('data'):
    os.mkdir('data')
    
if not os.path.exists('project_folder'):
    os.makedirs('project_folder')

X = pd.DataFrame(data_train.data[100:,:])
y = pd.DataFrame(data_train.target[100:])

# merge X and y
label = "digit"
X[label] = y

training_data = X

training_data.to_csv('data/digits.csv')
ds = ws.get_default_datastore()
ds.upload(src_dir='./data', target_path='digitsdata', overwrite=True, show_progress=True)

training_data = Dataset.Tabular.from_delimited_files(path=ds.path('digitsdata/digits.csv'))
```

## <a name="configure-experiment"></a>Konfigurera experiment
Ange inställningarna `AutoMLConfig`för .  (Se en [fullständig lista över parametrar](how-to-configure-auto-train.md#configure-experiment) och deras möjliga värden.)

```python
from azureml.train.automl import AutoMLConfig
import time
import logging

automl_settings = {
    "name": "AutoML_Demo_Experiment_{0}".format(time.time()),
    "experiment_timeout_minutes" : 20,
    "enable_early_stopping" : True,
    "iteration_timeout_minutes": 10,
    "n_cross_validations": 5,
    "primary_metric": 'AUC_weighted',
    "max_concurrent_iterations": 10,
}

automl_config = AutoMLConfig(task='classification',
                             debug_log='automl_errors.log',
                             path=project_folder,
                             compute_target=compute_target,
                             training_data=training_data,
                             label_column_name=label,
                             **automl_settings,
                             )
```

## <a name="submit-training-experiment"></a>Skicka träningsexperiment

Skicka nu konfigurationen för att automatiskt välja algoritmen, hyperparametrarna och träna modellen.

```python
from azureml.core.experiment import Experiment
experiment = Experiment(ws, 'automl_remote')
remote_run = experiment.submit(automl_config, show_output=True)
```

Du kommer att se utdata som liknar följande exempel:

    Running on remote compute: mydsvmParent Run ID: AutoML_015ffe76-c331-406d-9bfd-0fd42d8ab7f6
    ***********************************************************************************************
    ITERATION: The iteration being evaluated.
    PIPELINE:  A summary description of the pipeline being evaluated.
    DURATION: Time taken for the current iteration.
    METRIC: The result of computing score on the fitted pipeline.
    BEST: The best observed score thus far.
    ***********************************************************************************************

     ITERATION     PIPELINE                               DURATION                METRIC      BEST
             2      Standardize SGD classifier            0:02:36                  0.954     0.954
             7      Normalizer DT                         0:02:22                  0.161     0.954
             0      Scale MaxAbs 1 extra trees            0:02:45                  0.936     0.954
             4      Robust Scaler SGD classifier          0:02:24                  0.867     0.954
             1      Normalizer kNN                        0:02:44                  0.984     0.984
             9      Normalizer extra trees                0:03:15                  0.834     0.984
             5      Robust Scaler DT                      0:02:18                  0.736     0.984
             8      Standardize kNN                       0:02:05                  0.981     0.984
             6      Standardize SVM                       0:02:18                  0.984     0.984
            10      Scale MaxAbs 1 DT                     0:02:18                  0.077     0.984
            11      Standardize SGD classifier            0:02:24                  0.863     0.984
             3      Standardize gradient boosting         0:03:03                  0.971     0.984
            12      Robust Scaler logistic regression     0:02:32                  0.955     0.984
            14      Scale MaxAbs 1 SVM                    0:02:15                  0.989     0.989
            13      Scale MaxAbs 1 gradient boosting      0:02:15                  0.971     0.989
            15      Robust Scaler kNN                     0:02:28                  0.904     0.989
            17      Standardize kNN                       0:02:22                  0.974     0.989
            16      Scale 0/1 gradient boosting           0:02:18                  0.968     0.989
            18      Scale 0/1 extra trees                 0:02:18                  0.828     0.989
            19      Robust Scaler kNN                     0:02:32                  0.983     0.989


## <a name="explore-results"></a>Utforska resultat

Du kan använda samma [Jupyter widget](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py) som visas i [utbildningen handledning](tutorial-auto-train-models.md#explore-the-results) för att se ett diagram och tabell över resultat.

```python
from azureml.widgets import RunDetails
RunDetails(remote_run).show()
```

Här är en statisk bild av widgeten.  I anteckningsboken kan du klicka på valfri rad i tabellen för att se köregenskaper och utdataloggar för den körningen.   Du kan också använda listrutan ovanför diagrammet för att visa ett diagram över varje tillgängligt mått för varje iteration.

![widgettabell](./media/how-to-auto-train-remote/table.png)
![widgetdiagram](./media/how-to-auto-train-remote/plot.png)

Widgeten visar en URL som du kan använda för att se och utforska de enskilda kördetaljerna.  

Om du inte är i en Jupyter-anteckningsbok kan du visa webbadressen från själva körningen:

```
remote_run.get_portal_url()
```

Samma information finns på arbetsytan.  Mer information om dessa resultat finns i [Förstå automatiska maskininlärningsresultat](how-to-understand-automated-ml.md).

## <a name="example"></a>Exempel

Följande [anteckningsbok](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression/auto-ml-regression.ipynb) visar begrepp i den här artikeln.

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Nästa steg

* Läs om hur du [konfigurerar inställningar för automatisk träning](how-to-configure-auto-train.md).
* Se hur du aktiverar funktioner för modelltolkning i automatiserade [ML-experiment.](how-to-machine-learning-interpretability-automl.md)
