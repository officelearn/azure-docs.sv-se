---
title: Identifiera data drift (förhandsversion) på AKS-distributioner
titleSuffix: Azure Machine Learning service
description: Identifiera data drift på Azure Kubernetes Service distribuerade modeller i Azure Machine Learning-tjänsten.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: copeters
author: cody-dkdc
ms.date: 07/08/2019
ms.openlocfilehash: 3b8152bde8b7e44dde1b0b9c82216333778f83da
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67806009"
---
# <a name="detect-data-drift-preview-on-models-deployed-to-azure-kubernetes-service-aks"></a>Identifiera data drift (förhandsversion) för modeller som distribuerats till Azure Kubernetes Service (AKS)

I den här artikeln får du lära dig övervaka data för en distribuerad modell för drift av data mellan datauppsättning för träning och inferens. I samband med maskininlärning, kan tränade maskininlärningsmodeller prestanda försämrad förutsägelse på grund av drift. Du kan övervaka data drift med Azure Machine Learning-tjänsten och tjänsten kan skicka en e-postavisering till dig när drift har identifierats.

## <a name="what-is-data-drift"></a>Vad är data drift?

Data drift inträffar när data som hanteras av en modell i produktionen skiljer sig från data som används för att träna modellen. Det är en av de främsta skälen där modellens Precision försämras med tiden, vilket övervakningsdata drift hjälper dig att identifiera prestandaproblem för modellen. 

## <a name="what-can-i-monitor"></a>Vad kan jag övervaka?

Du kan använda Azure Machine Learning-tjänsten för att övervaka indata till en modell som distribuerats i AKS och jämföra dessa data till datauppsättning för träning för modellen. Med jämna mellanrum inferens data är [ögonblicksbilden och profileras](how-to-explore-prepare-data.md), sedan beräknas baslinje-datauppsättning för att producera en dataanalys drift som: 

+ Mäter storleken på data drift, kallas koefficienten drift.
+ Mått data avviker bidrag av funktionen, om vilka funktioner orsakade data drift.
+ Mått avstånd mått. För närvarande beräknas Wasserstein och energi avstånd.
+ Mäter distributioner av funktioner. För närvarande kernel densitet uppskattning och histogram.
+ Skicka aviseringar till data som avviker via e-post.

> [!Note]
> Den här tjänsten är (förhandsversion) och begränsade i konfigurationsalternativ. Se våra [API-dokumentation](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/?view=azure-ml-py) och [viktig](azure-machine-learning-release-notes.md) information och uppdateringar. 

### <a name="how-data-drift-is-monitored-in-azure-machine-learning-service"></a>Hur data drift övervakas i Azure Machine Learning-tjänsten

Med Azure Machine Learning-tjänsten kan övervakas data drift via datauppsättningar eller distributioner. För att övervaka data drift, har en baslinje-datauppsättning – vanligtvis utbildning datauppsättningen för en modell - angetts. En andra datauppsättning – vanligtvis modellen inkommande data som samlats in från en distribution – testas mot baslinje-datauppsättningen. Båda datauppsättningar är [profileras](how-to-explore-prepare-data.md#explore-with-summary-statistics) och indata till data avviker övervakningstjänsten. En machine learning-modell tränas att identifiera skillnader mellan de två datauppsättningarna. Modellens prestanda konverteras till koefficienten drift mäter omfattning för drift mellan de två datauppsättningarna. Med hjälp av [modellera interpretability](machine-learning-interpretability-explainability.md), funktioner som bidrar till drift-koefficienten beräknas. Statistisk information om varje funktion spåras från profilen för en datauppsättning. 

## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration. Om du inte har någon kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnadsfria versionen eller betalversionen av Azure Machine Learning-tjänsten](https://aka.ms/AMLFree) i dag.

- En arbetsyta för Azure Machine Learning-tjänsten och Azure Machine Learning-SDK för Python installerat. Följ instruktionerna på [skapa en arbetsyta för Azure Machine Learning-tjänsten](setup-create-workspace.md#sdk) att göra följande:

    - Skapa en Miniconda-miljö
    - Installera Azure Machine Learning SDK för Python
    - Skapa en arbetsyta
    - Skriv en konfigurationsfil för arbetsytan (aml_config/config.json).

- Installera data drift SDK med hjälp av följande kommando:

    ```shell
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

- [Aktivera insamling av modelldata](how-to-enable-data-collection.md) att samla in data från AKS-distributionen av modellen och bekräfta data som samlas in i den `modeldata` blob-behållare.

## <a name="configure-data-drift"></a>Konfigurera data drift
Konfigurera data drift för experimentet genom att importera beroenden som visas i följande exempel för Python. 

Det här exemplet visar hur du konfigurerar den [ `DataDriftDetector` ](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/azureml.contrib.datadrift.datadriftdetector.datadriftdetector?view=azure-ml-py) objekt:

```python
# Import Azure ML packages
from azureml.core import Experiment, Run, RunDetails
from azureml.contrib.datadrift import DataDriftDetector, AlertConfiguration

# if email address is specified, setup AlertConfiguration
alert_config = AlertConfiguration('your_email@contoso.com')

# create a new DatadriftDetector object
datadrift = DataDriftDetector.create(ws, model.name, model.version, services, frequency="Day", alert_config=alert_config)
    
print('Details of Datadrift Object:\n{}'.format(datadrift))
```

## <a name="submit-a-datadriftdetector-run"></a>Skicka en DataDriftDetector körning

Med den `DataDriftDetector` objekt som har konfigurerats, kan du skicka en [data drift kör](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/azureml.contrib.datadrift.datadriftdetector%28class%29?view=azure-ml-py#run-target-date--services--compute-target-name-none--create-compute-target-false--feature-list-none--drift-threshold-none-) på ett speciellt datum för modellen. Som en del av körningen, aktivera DataDriftDetector aviseringar genom att ange den `drift_threshold` parametern. Om den [datadrift_coefficient](#metrics) är över den angivna `drift_threshold`, skickas ett e-postmeddelande.

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

## <a name="visualize-drift-metrics"></a>Visualisera drift mått

<a name="metrics"></a>

När du har skickat din DataDriftDetector kör kan du se de mått som drift som sparas i varje körning iteration för en aktivitet för drift av data:


|Mått|Beskrivning|
--|--|
wasserstein_distance|Statistisk avståndet som definierats för endimensionell numeriska distribution.|
energy_distance|Statistisk avståndet som definierats för endimensionell numeriska distribution.|
datadrift_coefficient|Beräknas på samma sätt som Matthews korrelationskoefficienten, men den här utdatan är ett reellt tal mellan 0 och 1. I samband med drift, 0 indikerar inget drift och 1 anger maximal drift.|
datadrift_contribution|Funktionen vikten av funktioner som påverkar återställningstiden avviker.|

Det finns flera sätt att visa mått för drift:

* Använd Jupyter-widgeten.
* Använd den [ `get_metrics()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py#get-metrics-name-none--recursive-false--run-type-none--populate-false-) funktion på någon `datadrift` köra objekt.
* Visa värdena i Azure portal på din modell

I följande Python-exempel visar hur du rita relevanta data drift mått. Du kan använda de returnerade mått för att bygga anpassade visualiseringar:

```python
# start and end are datetime objects 
drift_metrics = datadrift.get_output(start_time=start, end_time=end)

# Show all data drift result figures, one per serivice.
# If setting with_details is False (by default), only the data drift magnitude will be shown; if it's True, all details will be shown.
drift_figures = datadrift.show(with_details=True)
```

![Se data drift som identifieras av Azure Machine Learning](media/how-to-monitor-data-drift/drift_show.png)


## <a name="schedule-data-drift-scans"></a>Schema för data drift genomsökningar 

När du aktiverar data drift identifiering körs en DataDriftDetector för den angivna, schemalagda frekvensen. Om datadrift_coefficient når den angivna `drift_threshold`, skickas ett e-postmeddelande med varje schemalagda körning. 

```python
datadrift.enable_schedule()
datadrift.disable_schedule()
```

Konfigurationen av drift-detektor data visas på sidan modell i Azure-portalen.

![Azure-portalen Data Drift Config](media/how-to-monitor-data-drift/drift_config.png)

## <a name="view-results-in-azure-ml-workspace-ui"></a>Visa resultat i Användargränssnittet för Azure ML-arbetsyta

Gå till sidan modell om du vill visa resultatet i Användargränssnittet för Azure ML-arbetsyta. På fliken information modellens visas data drift konfigurationen. En flik för 'Data Drift (förhandsversion) ”är nu tillgänglig visualiserar data drift mått. 

![Azure-portalen Data Drift](media/how-to-monitor-data-drift/drift_ui.png)

## <a name="receiving-drift-alerts"></a>Mottagande drift aviseringar

Genom att drift koefficienten tröskelvärde och får en e-postadress, en [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) e-postavisering skickas automatiskt varje gång drift är över tröskeln. 

För att du vill ställa in anpassade varningar och åtgärder, lagras alla data drift mått i den [Application Insights](how-to-enable-app-insights.md) resurs som har skapats tillsammans med den tjänst Azure Machine Learning-arbetsytan. Du kan följa länken i e-postavisering till Application Insights-fråga.

![Data Drift e-postavisering](media/how-to-monitor-data-drift/drift_email.png)

## <a name="retrain-your-model-after-drift"></a>Träna modellen efter drift

När data drift negativt påverkar prestandan för din distribuerade modell, är det dags att träna modellen. Följande [ `diff()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#diff-rhs-dataset--compute-target-none--columns-none-
) metoden ger dig en inledande uppfattning om vad som ändrats mellan de gamla och nya utbildning datauppsättningarna. 

```python
from azureml.core import Dataset

old_training_dataset.diff(new_training_dataset)
```

Baserat på utdata från föregående kod, kan du träna din modell. Du gör detta genom att fortsätta med följande steg.

* Undersök insamlade data och förbereda data för att träna den nya modellen.
* Dela upp den i träna/testdata.
* Träna modellen igen med den nya informationen.
* Utvärdera prestanda för den nya modellen.
* Distribuera nya modellen om prestanda är bättre än modellen för produktion.

## <a name="next-steps"></a>Nästa steg

* Ett fullständigt exempel på hur du använder data drift, finns det [Azure ML data avviker notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/data-drift/azure-ml-datadrift.ipynb). Den här Jupyter-anteckningsbok visar hur du använder en [Azure öppna datauppsättningen](https://docs.microsoft.com/azure/open-datasets/overview-what-are-open-datasets) distribuerar det till AKS för att träna en modell för att förutse vädret och övervaka data drift. 

* Vi skulle avsevärt uppskattar dina frågor, kommentarer eller förslag som data drift flyttar mot allmän tillgänglighet. Använd produkt feedback-knappen nedan! 
