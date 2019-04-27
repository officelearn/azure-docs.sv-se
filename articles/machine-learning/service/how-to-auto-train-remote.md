---
title: Automatiserad ML remote beräkningsmål
titleSuffix: Azure Machine Learning service
description: Lär dig att bygga modeller med automatiserade maskininlärning på Data Science Virtual machine (DSVM) remote beräkningsmål med Azure Machine Learning-tjänsten
services: machine-learning
author: nacharya1
ms.author: nilesha
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 6f2d71abeacee531b21a8276f621367dd39a39d9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60820334"
---
# <a name="train-models-with-automated-machine-learning-in-the-cloud"></a>Träna modeller med automatiserade maskininlärning i molnet

I Azure Machine Learning träna din modell på olika typer av beräkningsresurser som du hanterar. Compute-mål kan vara en lokal dator eller en dator i molnet.

Du kan enkelt skala upp eller skala ut din machine learning-experiment genom att lägga till ytterligare beräkningsmål. Beräkningsalternativ för mål är Ubuntu-baserad Data Science Virtual Machine (DSVM) eller beräkning av Azure Machine Learning. DSVM är en anpassad VM-avbildning på Microsoft Azure-molnet som skapats specifikt för datavetenskap. Det har många populära datavetenskaps och andra verktyg som förinstallerade och förkonfigurerade.  

I den här artikeln lär du dig att skapa en modell med automatiserade ML på DSVM.

## <a name="how-does-remote-differ-from-local"></a>Hur skiljer sig remote från lokal?

Självstudien ”[tränar en modell för klassificering med automatiserade machine learning](tutorial-auto-train-models.md)” Lär dig hur du använder en lokal dator för att träna modellen med automatiserade ML.  Arbetsflödet när utbildning lokalt gäller även för samt fjärranslutna mål. Men med remote beräkning körs automatiserade iterationer av experiment ML asynkront. Den här funktionen kan du avbryta en viss iteration, se status för körning eller fortsätta att arbeta med andra celler i Jupyter-anteckningsboken. För att träna via fjärranslutning skapa du först fjärransluten beräkningsmål, till exempel en Azure-DSVM.  Sedan konfigurerar om fjärresursen och skicka koden där.

Den här artikeln visar de extra stegen som behövs för att köra ett automatiserat ML-experiment på en fjärransluten DSVM.  Objektet arbetsytan `ws`, från självstudierna används i hela koden här.

```python
ws = Workspace.from_config()
```

## <a name="create-resource"></a>Skapa resurs

Skapa DSVM i din arbetsyta (`ws`) om den inte redan finns. Om DSVM skapades tidigare finns den här koden hoppar över skapandeprocessen och läser in befintliga resurs-information till den `dsvm_compute` objekt.  

**Uppskattad tidsåtgång**: Skapandet av den virtuella datorn tar cirka 5 minuter.

```python
from azureml.core.compute import DsvmCompute

dsvm_name = 'mydsvm' #Name your DSVM
try:
    dsvm_compute = DsvmCompute(ws, dsvm_name)
    print('found existing dsvm.')
except:
    print('creating new dsvm.')
    # Below is using a VM of SKU Standard_D2_v2 which is 2 core machine. You can check Azure virtual machines documentation for additional SKUs of VMs.
    dsvm_config = DsvmCompute.provisioning_configuration(vm_size = "Standard_D2_v2")
    dsvm_compute = DsvmCompute.create(ws, name = dsvm_name, provisioning_configuration = dsvm_config)
    dsvm_compute.wait_for_completion(show_output = True)
```

Du kan nu använda den `dsvm_compute` -objektet som den fjärranslutna beräkningsmål.

Begränsningar för DSVM är:
+ Måste vara kortare än 64 tecken.  
+ Får inte innehålla följande tecken: `\` ~! @ # $ % ^ & * () = + _ [] {} \\ \\ |;: \' \\”, < > /?. `

>[!Warning]
>Om det inte går att skapa ett meddelande om Marketplace-köp behörighet:
>    1. Gå till [Azure Portal](https://portal.azure.com)
>    1. Börja skapa en DSVM 
>    1. Välj ”vill skapa programmässigt” att skapa ett programmässig
>    1. Avsluta utan att faktiskt skapa den virtuella datorn
>    1. Kör koden skapas

Den här koden skapar inte något användarnamn eller lösenord för DSVM som tillhandahålls. Om du vill ansluta direkt till den virtuella datorn går du till den [Azure-portalen](https://portal.azure.com) att skapa autentiseringsuppgifter.  

### <a name="attach-existing-linux-dsvm"></a>Bifoga befintlig Linux DSVM

Du kan också bifoga en befintlig Linux DSVM som beräkningsmål. Det här exemplet använder en befintlig DSVM, men skapa inte en ny resurs.

> [!NOTE]
>
> I följande kod används den [RemoteCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.remote.remotecompute?view=azure-ml-py) målklassen att bifoga en befintlig virtuell dator som din beräkningsmål.
> Den [DsvmCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.dsvmcompute?view=azure-ml-py) klassen kommer att inaktualiseras i framtida versioner av det här designmönstret.

Kör följande kod för att skapa beräkningsmål från en befintlig Linux DSVM.

```python
from azureml.core.compute import ComputeTarget, RemoteCompute 

attach_config = RemoteCompute.attach_configuration(username='<username>',
                                                   address='<ip_address_or_fqdn>',
                                                   ssh_port=22,
                                                   private_key_file='./.ssh/id_rsa')
compute_target = ComputeTarget.attach(workspace=ws,
                                      name='attached-vm',
                                      attach_configuration=attach_config)

compute_target.wait_for_completion(show_output=True)
```

Du kan nu använda den `compute_target` -objektet som den fjärranslutna beräkningsmål.

## <a name="access-data-using-getdata-file"></a>Åtkomst till data med hjälp av get_data fil

Ger fjärresursen åtkomst till dina utbildningsdata. För automatiserade machine learning-experiment som körs på fjärranslutna beräkning, data ska hämtas med hjälp av en `get_data()` funktion.  

För att ge åtkomst måste du:
+ Skapa en fil som get_data.py innehåller en `get_data()` funktion 
+ Placera filen i en katalog som är tillgänglig som en absolut sökväg 

Du kan kapsla kod för att läsa data från blob storage- eller lokal disk i filen get_data.py. I följande kodexempel kommer data från sklearn-paketet.

>[!Warning]
>Om du använder fjärranslutna beräkning så måste du använda `get_data()` där dina data-transformeringar utförs. Om du behöver installera ytterligare bibliotek för Datatransformationer som en del av get_data() finns ytterligare steg ska följas. Referera till den [automatisk-ml-förberedelse av data exempel notebook](https://aka.ms/aml-auto-ml-data-prep ) mer information.


```python
# Create a project_folder if it doesn't exist
if not os.path.exists(project_folder):
    os.makedirs(project_folder)

#Write the get_data file.
%%writefile $project_folder/get_data.py

from sklearn import datasets
from scipy import sparse
import numpy as np

def get_data():
    
    digits = datasets.load_digits()
    X_digits = digits.data[10:,:]
    y_digits = digits.target[10:]

    return { "X" : X_digits, "y" : y_digits }
```

## <a name="configure-experiment"></a>Konfigurera experiment

Ange inställningar för `AutoMLConfig`.  (Finns i en [fullständig lista över parametrar](how-to-configure-auto-train.md#configure-experiment) och deras möjliga värden.)

I inställningarna för `run_configuration` är inställd på den `run_config` objekt som innehåller de inställningar och konfiguration för DSVM.  

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
                             compute_target = dsvm_compute,
                             data_script=project_folder + "/get_data.py",
                             **automl_settings,
                            )
```

### <a name="enable-model-explanations"></a>Aktivera modellen förklaringar

Ange den valfria `model_explainability` parametern i den `AutoMLConfig` konstruktor. Dessutom kan en verifiering dataframe objektet måste skickas som en parameter `X_valid` kan använda funktionen explainability modellen.

```python
automl_config = AutoMLConfig(task='classification',
                             debug_log='automl_errors.log',
                             path=project_folder,
                             compute_target = dsvm_compute,
                             data_script=project_folder + "/get_data.py",
                             **automl_settings,
                             model_explainability=True,
                             X_valid = X_test
                            )
```

## <a name="submit-training-experiment"></a>Skicka träningsexperiment

Nu skicka konfigurationen för att automatiskt välja algoritmen, hyper parametrar och träna modellen.

```python
from azureml.core.experiment import Experiment
experiment=Experiment(ws, 'automl_remote')
remote_run = experiment.submit(automl_config, show_output=True)
```

Du ser utdata som liknar följande exempel:

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

Du kan använda samma Jupyter widgeten som den i [utbildning självstudien](tutorial-auto-train-models.md#explore-the-results) att se ett diagram och en tabell med resultat.

```python
from azureml.widgets import RunDetails
RunDetails(remote_run).show()
```
Här är en statisk bild av widgeten.  Du kan klicka på någon av staplarna i tabell för att visa egenskaper för körning och utdataloggar för som körs i anteckningsboken.   Du kan också använda listrutan ovanför diagrammet för att visa ett diagram över alla tillgängliga mått för varje iteration.

![widgettabell](./media/how-to-auto-train-remote/table.png)
![widgetdiagram](./media/how-to-auto-train-remote/plot.png)

Widgeten visar en URL som du kan använda för att visa och utforska de enskilda körningsinformation.
 
### <a name="view-logs"></a>Visa loggar

Finns det loggar på DSVM under `/tmp/azureml_run/{iterationid}/azureml-logs`.

## <a name="best-model-explanation"></a>Den bästa modellen förklaring

Hämtning av modellen förklaring data kan du se detaljerad information om modeller för att öka transparens för program som körs på serverdelen. I det här exemplet kör du modellen förklaringar endast för den bästa anpassa modellen. Om du kör för alla modeller i pipelinen, resulterar det i betydande körningstid. Förklaring modellinformation innehåller:

* shap_values: Förklaring-information som genereras av formdata lib
* expected_values: Det förväntade värdet av modellen som används för att ställa in X_train data.
* overall_summary: Modellen på funktionen vikten värden sorteras i fallande ordning
* overall_imp: Funktionsnamn sorteras i samma ordning som i overall_summary
* per_class_summary: Klass på funktionen vikten värden sorteras i fallande ordning. Endast tillgängligt för klassificering
* per_class_imp: Funktionsnamnen sorteras i samma ordning som i per_class_summary. Endast tillgängligt för klassificering

Använd följande kod för att välja den bästa pipelinen från din iterationer. Den `get_output` metoden returnerar den bästa körningen och den anpassade modellen för senaste passar anrop.

```python
best_run, fitted_model = remote_run.get_output()
```

Importera den `retrieve_model_explanation` fungerar och körs på den bästa modellen.

```python
from azureml.train.automl.automlexplainer import retrieve_model_explanation

shap_values, expected_values, overall_summary, overall_imp, per_class_summary, per_class_imp = \
    retrieve_model_explanation(best_run)
```

Skriv ut resultat för den `best_run` förklaring variabler som du vill visa.

```python
print(overall_summary)
print(overall_imp)
print(per_class_summary)
print(per_class_imp)
```

Skriva ut den `best_run` förklaring sammanfattning variabler resulterar i följande utdata.

![Modellen explainability konsolens utdata](./media/how-to-auto-train-remote/expl-print.png)

Du också visualisera funktionen vikten via widget Användargränssnittet, samt webbgränssnittet på Azure-portalen i din arbetsyta.

![Modellen explainability UI](./media/how-to-auto-train-remote/model-exp.png)

## <a name="example"></a>Exempel

Den [how-to-use-azureml/automated-machine-learning/remote-execution/auto-ml-remote-execution.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/remote-execution/auto-ml-remote-execution.ipynb) notebook demonstrerar begreppen i den här artikeln. 

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Nästa steg

Lär dig [hur du konfigurerar inställningar för automatisk träning](how-to-configure-auto-train.md).
