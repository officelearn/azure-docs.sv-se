---
title: Så identifierar data drift (förhandsversion) på AKS-distributioner
titleSuffix: Azure Machine Learning service
description: Lär dig att identifiera data drift på Azure Kubernetes Service distribuerade modeller i Azure Machine Learning-tjänsten.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: copeters
author: cody-dkdc
ms.date: 06/20/2019
ms.openlocfilehash: e4deeab28fb643ff32624ba9dd16574e621f508c
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/22/2019
ms.locfileid: "67333359"
---
# <a name="how-to-detect-data-drift-preview-on-models-deployed-to-azure-kubernetes-service"></a>Så identifierar data drift (förhandsversion) för modeller som distribueras till Azure Kubernetes Service
I den här artikeln får du lära dig hur du övervakar för [data drift](concept-data-drift.md) mellan utbildningsdata för datauppsättningen och inferens av en distribuerad modell. 

Data drift är en av de främsta skälen där modellens Precision försämras med tiden. Det händer när data som hanteras av en modell i produktionen skiljer sig från data som används för att träna modellen. Azure Machine Learning-tjänsten kan övervaka data drift Drift-detektor Data. Om drift har identifierats, kan tjänsten skickar en avisering till dig.  

> [!Note]
> Den här tjänsten är (förhandsversion) och begränsade i konfigurationsalternativ. Se våra [API-dokumentation](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/?view=azure-ml-py) och [viktig](azure-machine-learning-release-notes.md) information och uppdateringar. 

Du kan använda Azure Machine Learning-tjänsten för att övervaka indata till en modell som distribuerats i AKS och jämföra dessa data till datauppsättning för träning för modellen. Med jämna mellanrum inferens data är [ögonblicksbilden och profileras](how-to-explore-prepare-data.md), sedan beräknas baslinje-datauppsättning för att producera en dataanalys drift som: 

+ Mäter storleken på data drift, kallas koefficienten drift.
+ Mått data avviker bidrag av funktionen, om vilka funktioner orsakade data drift.
+ Mått avstånd mått. För närvarande beräknas Wasserstein och energi avstånd.
+ Mäter distributioner av funktioner. För närvarande kernel densitet uppskattning och histogram.
+ Skicka aviseringar till data som avviker via e-post.

Mer information om hur de här måtten beräknas finns den [data drift konceptet](concept-data-drift.md) artikeln.

## <a name="prerequisites"></a>Nödvändiga komponenter

- Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnadsfria versionen eller betalversionen av Azure Machine Learning-tjänsten](https://aka.ms/AMLFree) i dag.

- En arbetsyta för Azure Machine Learning-tjänsten och Azure Machine Learning-SDK för Python installerat. Lär dig hur du hämtar dessa krav med hjälp av den [så här konfigurerar du en utvecklingsmiljö](how-to-configure-environment.md) dokumentet.

- [Konfigurera din miljö](how-to-configure-environment.md), och sedan installera data drift SDK med hjälp av följande kommando:

    ```
    pip install azureml-contrib-datadrift
    ```

- Skapa en [datauppsättning](how-to-create-register-datasets.md) från din modell träningsdata.

- Ange datauppsättning för träning när [registrerar](concept-model-management-and-deployment.md) modellen. I följande exempel visar hur du använder den `datasets` datauppsättning för träning med parametern:

    ```python
    model = Model.register(model_path=model_file,
                        model_name=model_name,
                        workspace=ws,
                        datasets=datasets)

    print(model_name, image_name, service_name, model)
    ```

- Konfigurera den [modell för datainsamling](how-to-enable-data-collection.md) att samla in data från AKS-distributionen av modellen och bekräfta data som samlas in i den `modeldata` blob-behållare.

## <a name="import-dependencies"></a>Importera beroenden 
Importera beroenden som används i den här guiden:

```python
# Azure ML service packages 
from azureml.core import Experiment, Run, RunDetails
from azureml.contrib.datadrift import DataDriftDetector, AlertConfiguration
``` 

## <a name="configure-data-drift"></a>Konfigurera data drift 

Python i exemplet nedan visas hur du konfigurerar den `DataDriftDetector` objekt:

```python
# if email address is specified, setup AlertConfiguration
alert_config = AlertConfiguration('your_email@contoso.com')

# create a new DatadriftDetector object
datadrift = DataDriftDetector.create(ws, model.name, model.version, services, frequency="Day", alert_config=alert_config)
    
print('Details of Datadrift Object:\n{}'.format(datadrift))
```

Mer information finns i den [DataDrift](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/?view=azure-ml-py) referens.

## <a name="submit-a-datadriftdetector-run"></a>Skicka en DataDriftDetector körning

Med DataDriftDetector konfigurerad, kan du skicka en [data drift kör](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/azureml.contrib.datadrift.datadriftdetector%28class%29?view=azure-ml-py#run-target-date--services--compute-target-name-none--create-compute-target-false--feature-list-none--drift-threshold-none-) på ett speciellt datum för modellen. 

```python
# adhoc run today
target_date = datetime.today()

# create a new compute - creates datadrift-server
run = datadrift.run(target_date, services, feature_list=feature_list, create_compute_target=True)

# or specify existing compute cluster
run = datadrift.run(target_date, services, feature_list=feature_list, compute_target='cpu-cluster')

# show details of the data drift run
exp = Experiment(ws, datadrift._id)
dd_run = Run(experiment=exp, run_id=run)
RunDetails(dd_run).show()
```

## <a name="get-data-drift-analysis-results"></a>Hämta data drift analysresultat

I följande Python-exempel visar hur du rita relevanta data drift mått. Du kan använda de returnerade mått för att bygga anpassade visualiseringar:

```python
# start and end are datetime objects 
drift_metrics = datadrift.get_output(start_time=start, end_time=end)

# Show all data drift result figures, one per serivice.
# If setting with_details is False (by default), only the data drift magnitude will be shown; if it's True, all details will be shown.
drift_figures = datadrift.show(with_details=True)
```

![Data Drift Show](media/how-to-monitor-data-drift/drift_show.png)

Mer information om de mått som beräknas finns den [data drift konceptet](concept-data-drift.md) artikeln.

## <a name="schedule-data-drift-detection"></a>Schema för data drift identifiering 

Aktivera ett schema för drift utför en DataDriftDetector körs vid den angivna frekvensen för. Om drift är tröskelvärdet för angivna, skickas ett e-postmeddelande. 

```python
datadrift.enable_schedule()
datadrift.disable_schedule()
```

Konfigurationen av drift-detektor data visas på sidan modell i Azure-portalen.

![Azure-portalen Data Drift Config](media/how-to-monitor-data-drift/drift_config.png)

## <a name="view-results-in-azure-ml-workspace-ui"></a>Visa resultat i Användargränssnittet för Azure ML-arbetsyta

Gå till sidan modell om du vill visa resultatet i Användargränssnittet för Azure ML-arbetsyta. På fliken information modellens visas data drift konfigurationen. En flik för 'Data Drift (förhandsversion) ”är nu tillgänglig visualiserar data drift mått. 

![Azure-portalen Data Drift](media/how-to-monitor-data-drift/drift_ui.png)

## <a name="setting-up-alerts"></a>Ställa in aviseringar 

Genom att drift koefficienten tröskelvärde för avisering och får en e-postadress, en [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) e-postavisering skickas om drift är över tröskeln. Alla mått för drift av data lagras i app insights-resurs som är associerade med Azure Machine Learning-arbetsytan som du kan ställa in anpassade varningar eller åtgärder för tjänsten. Du kan följa länken i e-postavisering till app insights-fråga.

![Data Drift e-postavisering](media/how-to-monitor-data-drift/drift_email.png)

## <a name="next-steps"></a>Nästa steg

* Ett fullständigt exempel på hur du använder data drift, finns det [Azure ML data avviker notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/data-drift/azure-ml-datadrift.ipynb). Den här Jupyter-anteckningsbok visar hur du använder en [Azure öppna datauppsättningen](https://docs.microsoft.com/azure/open-datasets/overview-what-are-open-datasets) distribuerar det till AKS för att träna en modell för att förutse vädret och övervaka data drift. 

* Vi skulle avsevärt uppskattar dina frågor, kommentarer eller förslag som data drift flyttar mot allmän tillgänglighet. Använd produkt feedback-knappen nedan! 
