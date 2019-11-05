---
title: Automatiserade ML fjärrberäknings mål
titleSuffix: Azure Machine Learning
description: Lär dig hur du skapar modeller med hjälp av automatisk maskin inlärning på ett Azure Machine Learning fjärrberäknings mål med Azure Machine Learning
services: machine-learning
author: cartacioS
ms.author: sacartac
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 4276a713e62f96cc5340fc7be0e8391939d32342
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73497324"
---
# <a name="train-models-with-automated-machine-learning-in-the-cloud"></a>Träna modeller med automatiserad maskin inlärning i molnet

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

I Azure Machine Learning tränar du din modell på olika typer av beräknings resurser som du hanterar. Compute-målet kan vara en lokal dator eller en resurs i molnet.

Du kan enkelt skala upp eller skala ut dator inlärnings experimentet genom att lägga till ytterligare beräknings mål, till exempel Azure Machine Learning Compute (AmlCompute Compute). AmlCompute är en hanterad beräknings infrastruktur som gör att du enkelt kan skapa en beräkning med en enda eller flera noder.

I den här artikeln får du lära dig hur du skapar en modell med hjälp av automatiserad ML med AmlCompute.

## <a name="how-does-remote-differ-from-local"></a>Hur skiljer sig fjärrifrån från lokala objekt?

I självstudien "[träna en klassificerings modell med automatisk maskin inlärning](tutorial-auto-train-models.md)" lär du dig hur du använder en lokal dator för att träna en modell med automatiserad ml. Arbets flödet när utbildning lokalt även gäller för fjärranslutna mål. Med fjärrberäkning utförs dock automatiskt ML experiment upprepningar asynkront. Med den här funktionen kan du avbryta en viss iteration, se status för körningen eller fortsätta att arbeta med andra celler i Jupyter Notebook. För att kunna träna via fjärr anslutning skapar du först ett fjärrberäknings mål som AmlCompute. Sedan konfigurerar du fjär resursen och skickar koden dit.

Den här artikeln visar de extra steg som krävs för att köra ett automatiserat ML-experiment på ett fjärran slutet AmlCompute-mål. Objektet arbets yta `ws`från självstudien används i hela koden här.

```python
ws = Workspace.from_config()
```

## <a name="create-resource"></a>Skapa resurs

Skapa AmlCompute-målet på din arbets yta (`ws`) om det inte redan finns.

**Tids uppskattning**: skapandet av AmlCompute-målet tar cirka 5 minuter.

```python
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget

amlcompute_cluster_name = "automlcl"  # Name your cluster
provisioning_config = AmlCompute.provisioning_configuration(vm_size="STANDARD_D2_V2",
                                                            # for GPU, use "STANDARD_NC6"
                                                            # vm_priority = 'lowpriority', # optional
                                                            max_nodes=6)
compute_target = ComputeTarget.create(
    ws, amlcompute_cluster_name, provisioning_config)

# Can poll for a minimum number of nodes and for a specific timeout.
# If no min_node_count is provided, it will use the scale settings for the cluster.
compute_target.wait_for_completion(
    show_output=True, min_node_count=None, timeout_in_minutes=20)
```

Du kan nu använda `compute_target`-objektet som fjärrberäknings mål.

Kluster namns begränsningarna är:
+ Måste vara kortare än 64 tecken.
+ Det får inte finnas något av följande tecken: `\` ~! @ # $% ^ & * () = + _ [] {} \\\\ |; : \' \\, < >/?. `

## <a name="access-data-using-tabulardataset-function"></a>Åtkomst till data med funktionen TabularDataset

Definierat X och y som `TabularDataset`s, som skickas till automatiserad ML i AutoMLConfig. `from_delimited_files` som standard anges `infer_column_types` till true, vilket kommer att härleda kolumn typen automatiskt. 

Om du vill ställa in kolumn typerna manuellt kan du ange att argumentet `set_column_types` manuellt ska ange typ för varje kolumn. I följande kod exempel kommer data från sklearn-paketet.

```python
# Create a project_folder if it doesn't exist
if not os.path.isdir('data'):
    os.mkdir('data')
    
if not os.path.exists(project_folder):
    os.makedirs(project_folder)

from sklearn import datasets
from azureml.core.dataset import Dataset
from scipy import sparse
import numpy as np
import pandas as pd

data_train = datasets.load_digits()

pd.DataFrame(data_train.data[100:,:]).to_csv("data/X_train.csv", index=False)
pd.DataFrame(data_train.target[100:]).to_csv("data/y_train.csv", index=False)

ds = ws.get_default_datastore()
ds.upload(src_dir='./data', target_path='digitsdata', overwrite=True, show_progress=True)

X = Dataset.Tabular.from_delimited_files(path=ds.path('digitsdata/X_train.csv'))
y = Dataset.Tabular.from_delimited_files(path=ds.path('digitsdata/y_train.csv'))

```

## <a name="create-run-configuration"></a>Skapa körnings konfiguration

Om du vill göra beroenden tillgängliga för get_data. py-skriptet definierar du ett `RunConfiguration`-objekt med definierat `CondaDependencies`. Använd det här objektet för parametern `run_configuration` i `AutoMLConfig`.

```python
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies

run_config = RunConfiguration(framework="python")
run_config.target = compute_target
run_config.environment.docker.enabled = True
run_config.environment.docker.base_image = azureml.core.runconfig.DEFAULT_CPU_IMAGE

dependencies = CondaDependencies.create(
    pip_packages=["scikit-learn", "scipy", "numpy"])
run_config.environment.python.conda_dependencies = dependencies
```

I den här [exempel antecknings boken](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/remote-amlcompute/auto-ml-remote-amlcompute.ipynb) finns ytterligare ett exempel på det här design mönstret.

## <a name="configure-experiment"></a>Konfigurera experiment
Ange inställningarna för `AutoMLConfig`.  (Se en [fullständig lista över parametrar](how-to-configure-auto-train.md#configure-experiment) och deras möjliga värden.)

```python
from azureml.train.automl import AutoMLConfig
import time
import logging

automl_settings = {
    "name": "AutoML_Demo_Experiment_{0}".format(time.time()),
    "iteration_timeout_minutes": 10,
    "iterations": 20,
    "n_cross_validations": 5,
    "primary_metric": 'AUC_weighted',
    "preprocess": False,
    "max_concurrent_iterations": 10,
    "verbosity": logging.INFO
}

automl_config = AutoMLConfig(task='classification',
                             debug_log='automl_errors.log',
                             path=project_folder,
                             compute_target=compute_target,
                             run_configuration=run_config,
                             X = X,
                             y = y,
                             **automl_settings,
                             )
```

### <a name="enable-model-explanations"></a>Aktivera modell förklaringar

Ange den valfria `model_explainability` parametern i `AutoMLConfig`-konstruktorn. Dessutom måste ett verifierings dataframe-objekt skickas som en parameter `X_valid` för att använda funktionen modell förklaring.

```python
automl_config = AutoMLConfig(task='classification',
                             debug_log='automl_errors.log',
                             path=project_folder,
                             compute_target=compute_target,
                             run_configuration=run_config,
                             X = X,
                             y = y,
                             **automl_settings,
                             model_explainability=True,
                             X_valid=X_test
                             )
```

## <a name="submit-training-experiment"></a>Skicka utbildnings experiment

Nu ska du skicka konfigurationen för att automatiskt välja algoritmen, Hyper-parametrarna och träna modellen.

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

Du kan använda samma [Jupyter-widget](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py) som visas i [övnings självstudien](tutorial-auto-train-models.md#explore-the-results) för att se ett diagram och en tabell med resultat.

```python
from azureml.widgets import RunDetails
RunDetails(remote_run).show()
```

Här är en statisk bild av widgeten.  I antecknings boken kan du klicka på en rad i tabellen för att se körnings egenskaper och utgående loggar för den körningen.   Du kan också använda List rutan ovanför grafen för att visa ett diagram över varje tillgängligt mått för varje iteration.

![widgettabell](./media/how-to-auto-train-remote/table.png)
![widgetdiagram](./media/how-to-auto-train-remote/plot.png)

Widgeten visar en URL som du kan använda för att visa och utforska de enskilda körnings uppgifterna.  

Om du inte använder en Jupyter-anteckningsbok kan du Visa URL: en från själva köra:

```
remote_run.get_portal_url()
```

Samma information finns på arbets ytan.  Mer information om de här resultaten finns i [förstå automatiserade maskin inlärnings resultat](how-to-understand-automated-ml.md).

### <a name="view-logs"></a>Visa loggar

Hitta loggar på DSVM under `/tmp/azureml_run/{iterationid}/azureml-logs`.

## <a name="explain"></a>Förklaring av bästa modell

Genom att hämta modell förklarings data kan du se detaljerad information om modeller för att öka transparensen i vad som körs på Server delen. I det här exemplet kör du modell förklaringar enbart för den bästa anpassnings modellen. Om du kör för alla modeller i pipelinen leder det till betydande körnings tid. Information om modell förklaringar innehåller:

* shap_values: förklarings informationen som genereras av Shap-lib.
* expected_values: det förväntade värdet för modellen som används för en uppsättning X_train-data.
* overall_summary: funktions värden på modell nivå som är sorterade i fallande ordning.
* overall_imp: funktions namnen sorteras i samma ordning som i overall_summary.
* per_class_summary: funktions värden för funktionen på klass nivå sorteras i fallande ordning. Endast tillgängligt för klassificerings ärendet.
* per_class_imp: funktions namnen sorteras i samma ordning som i per_class_summary. Endast tillgängligt för klassificerings ärendet.

Använd följande kod för att välja den bästa pipelinen från dina iterationer. Metoden `get_output` returnerar den bästa körningen och den monterade modellen för den senaste pass-anropet.

```python
best_run, fitted_model = remote_run.get_output()
```

Importera `retrieve_model_explanation`-funktionen och kör den bästa modellen.

```python
from azureml.train.automl.automlexplainer import retrieve_model_explanation

shap_values, expected_values, overall_summary, overall_imp, per_class_summary, per_class_imp = \
    retrieve_model_explanation(best_run)
```

Skriv ut resultat för variabler för `best_run` förklaring som du vill visa.

```python
print(overall_summary)
print(overall_imp)
print(per_class_summary)
print(per_class_imp)
```

Om du skriver ut sammanfattnings variabler för `best_run` förklaring resulterar det i följande utdata.

![Modeller av förklarande modell konsol](./media/how-to-auto-train-remote/expl-print.png)

Du kan också visualisera funktions prioritet genom gränssnittet i widgeten eller i din arbets yta i [Azure Machine Learning Studio](https://ml.azure.com). 

![Användar gränssnitt för modell förklaring](./media/how-to-auto-train-remote/model-exp.png)

## <a name="example"></a>Exempel

Antecknings boken [How-to-use-azureml/Automated-Machine-Learning/Remote-amlcompute/Auto-ml-Remote-amlcompute. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/remote-amlcompute/auto-ml-remote-amlcompute.ipynb) visar begreppen i den här artikeln.

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Nästa steg

Lär dig [hur du konfigurerar inställningar för automatisk träning](how-to-configure-auto-train.md).
