---
title: Identifiera datadrift på AKS-distributioner
titleSuffix: Azure Machine Learning
description: Identifiera datadrift (förhandsversion) på Azure Kubernetes Service-distribuerade modeller i Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: copeters
author: cody-dkdc
ms.date: 11/04/2019
ms.openlocfilehash: d1da7309b296b57db0c28d5b52fe91efa86709c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75537011"
---
# <a name="detect-data-drift-preview-on-models-deployed-to-azure-kubernetes-service-aks"></a>Identifiera datadrift (förhandsversion) på modeller som distribueras till Azure Kubernetes Service (AKS)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

I den här artikeln får du lära dig hur du övervakar datadrift mellan träningsdatauppsättningen och inferensdata för en distribuerad modell. I samband med maskininlärning kan utbildade maskininlärningsmodeller uppleva försämrade förutsägelseprestanda på grund av drift. Med Azure Machine Learning kan du övervaka datadrift och tjänsten kan skicka en e-postavisering till dig när drift upptäcks.

## <a name="what-is-data-drift"></a>Vad är datadrift?

I samband med maskininlärning är datadrift förändringen i modellindata som leder till försämrad modellprestanda. Det är en av de främsta anledningarna till att modellens noggrannhet försämras med tiden, vilket hjälper till att identifiera prestandaproblem för modeller. 

## <a name="what-can-i-monitor"></a>Vad kan jag övervaka?

Med Azure Machine Learning kan du övervaka indata till en modell som distribueras på AKS och jämföra dessa data med träningsdatauppsättningen för modellen. Med jämna mellanrum är inferensdata [ögonblicksbild och profilerade](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py)och beräknas sedan mot baslinjedatauppsättningen för att producera en datadriftanalys som: 

+ Mäter storleken på datadrift, kallad avdriftskoefficienten.
+ Mäter datadriftbidraget efter funktion, som anger vilka funktioner som orsakade datadrift.
+ Mäter avståndsmått. För närvarande beräknas Wasserstein och Energi distanserar.
+ Mäter fördelning av funktioner. För närvarande kärndensitet uppskattning och histogram.
+ Skicka aviseringar till datadrift via e-post.

> [!Note]
> Den här tjänsten är i (förhandsversion) och begränsad i konfigurationsalternativ. Mer information och uppdateringar finns i vår [API-dokumentation](https://docs.microsoft.com/python/api/azureml-datadrift/) [och viktig information.](azure-machine-learning-release-notes.md) 

### <a name="how-data-drift-is-monitored-in-azure-machine-learning"></a>Så här övervakas datadrift i Azure Machine Learning

Med hjälp av Azure Machine Learning övervakas datadrift via datauppsättningar eller distributioner. För att övervaka för datadrift anges en baslinjedatauppsättning - vanligtvis träningsdatauppsättningen för en modell. En andra datauppsättning - vanligtvis modell indata som samlats in från en distribution - testas mot baslinjedatauppsättningen. Båda datauppsättningarna profileras och matas in till datadriftövervakningstjänsten. En maskininlärningsmodell tränas för att identifiera skillnader mellan de två datauppsättningarna. Modellens prestanda omvandlas till avdriftskoefficienten, som mäter omfattningen av drift mellan de två datauppsättningarna. Med hjälp av [modelltolkbarhet](how-to-machine-learning-interpretability.md)beräknas de funktioner som bidrar till avdriftskoefficienten. Från datauppsättningsprofilen spåras statistisk information om varje funktion. 

## <a name="prerequisites"></a>Krav

- En Azure-prenumeration. Om du inte har ett, skapa ett gratis konto innan du börjar. Prova den [kostnadsfria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree) idag.

- Azure Machine Learning SDK för Python har installerats. Använd instruktionerna på [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) för att göra följande:

    - Skapa en Miniconda-miljö
    - Installera Azure Machine Learning SDK för Python

- En [Azure Machine Learning-arbetsyta](how-to-manage-workspace.md).

- En [konfigurationsfil](how-to-configure-environment.md#workspace)för arbetsytan .

- Installera datadrift SDK med följande kommando:

    ```shell
    pip install azureml-datadrift
    ```

- Skapa en [datauppsättning](how-to-create-register-datasets.md) från modellens träningsdata.

- Ange utbildningsdatauppsättningen när [du registrerar](concept-model-management-and-deployment.md) modellen. I följande exempel visas `datasets` hur du använder parametern för att ange träningsdatauppsättningen:

    ```python
    model = Model.register(model_path=model_file,
                        model_name=model_name,
                        workspace=ws,
                        datasets=datasets)

    print(model_name, image_name, service_name, model)
    ```

- Aktivera insamling av [modelldata](how-to-enable-data-collection.md) för att samla in data från AKS-distributionen av modellen och bekräfta att data samlas in i `modeldata` blob-behållaren.

## <a name="configure-data-drift"></a>Konfigurera dataavdrift
Om du vill konfigurera datadrift för experimentet importerar du beroenden som visas i följande Python-exempel. 

I det här exemplet [`DataDriftDetector`](/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector) visas att objektet konfigureras:

```python
# Import Azure ML packages
from azureml.core import Experiment, Run, RunDetails
from azureml.datadrift import DataDriftDetector, AlertConfiguration

# if email address is specified, setup AlertConfiguration
alert_config = AlertConfiguration('your_email@contoso.com')

# create a new DataDriftDetector object
datadrift = DataDriftDetector.create(ws, model.name, model.version, services, frequency="Day", alert_config=alert_config)
    
print('Details of Datadrift Object:\n{}'.format(datadrift))
```

## <a name="submit-a-datadriftdetector-run"></a>Skicka en DataDriftDetector-körning

Med `DataDriftDetector` objektet konfigurerat kan du skicka en [datadrift köra](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector#run-target-date--services-none--compute-target-none--create-compute-target-false--feature-list-none--drift-threshold-none-) på ett visst datum för modellen. Som en del av körningen aktiverar du DataDriftDetector-aviseringar genom att ange parametern. `drift_threshold` Om [datadrift_coefficient](#visualize-drift-metrics) ligger över det `drift_threshold`angivna skickas ett e-postmeddelande.

```python
# adhoc run today
target_date = datetime.today()

# create a new compute - creates datadrift-server
run = datadrift.run(target_date, services, feature_list=feature_list, create_compute_target=True)

# or specify existing compute cluster
run = datadrift.run(target_date, services, feature_list=feature_list, compute_target='cpu-cluster')

# show details of the data drift run
exp = Experiment(ws, datadrift._id)
dd_run = Run(experiment=exp, run_id=run.id)
RunDetails(dd_run).show()
```

## <a name="visualize-drift-metrics"></a>Visualisera drivvärden

<a name="metrics"></a>

När du har skickat in din DataDriftDetector-körning kan du se de avdriftmått som sparas i varje körningsiteration för en datadrift-uppgift:


|Mått|Beskrivning|
--|--|
wasserstein_distance|Statistiskt avstånd definierat för endimensionell numerisk fördelning.|
energy_distance|Statistiskt avstånd definierat för endimensionell numerisk fördelning.|
datadrift_coefficient|Beräknas på samma sätt som Matthews korrelationskoefficient, men denna utgång är ett verkligt antal som sträcker sig från 0 till 1. I samband med drift anger 0 ingen drift och 1 indikerar maximal drift.|
datadrift_contribution|Funktionens betydelse för funktioner som bidrar till drift.|

Det finns flera sätt att visa drift-mått:

* `RunDetails`Använd [Jupyter widget](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py).
* Använd [`get_metrics()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py#get-metrics-name-none--recursive-false--run-type-none--populate-false-) funktionen på `datadrift` alla körningsobjekt.
* Visa måtten från avsnittet **Modeller** på arbetsytan i [Azure Machine Learning Studio](https://ml.azure.com).

Följande Python-exempel visar hur du ritar relevanta datadriftmått. Du kan använda de returnerade måtten för att skapa anpassade visualiseringar:

```python
# start and end are datetime objects 
drift_metrics = datadrift.get_output(start_time=start, end_time=end)

# Show all data drift result figures, one per service.
# If setting with_details is False (by default), only the data drift magnitude will be shown; if it's True, all details will be shown.
drift_figures = datadrift.show(with_details=True)
```

![Se datadrift som identifieras av Azure Machine Learning](./media/how-to-monitor-data-drift/drift_show.png)


## <a name="schedule-data-drift-scans"></a>Schemalägg datadriftsökningar 

När du aktiverar identifiering av datadrift körs en DataDriftDetector med den angivna schemalagda frekvensen. Om datadrift_coefficient når angivet `drift_threshold`skickas ett e-postmeddelande med varje schemalagd körning. 

```python
datadrift.enable_schedule()
datadrift.disable_schedule()
```

Konfigurationen av dataavdriftdetektorn kan ses under **Modeller** på fliken **Detaljer** på arbetsytan i [Azure Machine Learning studio](https://ml.azure.com).

[![Azure Machine Learning studio Data Drift](./media/how-to-monitor-data-drift/drift-config.png)](media/how-to-monitor-data-drift/drift-config-expanded.png)

## <a name="view-results-in-your-azure-machine-learning-studio"></a>Visa resultat i din Azure Machine Learning-studio

Om du vill visa resultat på arbetsytan i [Azure Machine Learning Studio](https://ml.azure.com)navigerar du till modellsidan. På informationsfliken för modellen visas datadriftkonfigurationen. En **datadrift** flik är nu tillgänglig visualisera data drift mått. 

[![Azure Machine Learning studio Data Drift](./media/how-to-monitor-data-drift/drift-ui.png)](media/how-to-monitor-data-drift/drift-ui-expanded.png)

## <a name="receiving-drift-alerts"></a>Ta emot driftvarningar

Genom att ange tröskelvärdet för avdriftskoefficient och tillhandahålla en e-postadress skickas en e-postavisering [för Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) automatiskt när avdriftskoefficienten ligger över tröskelvärdet. 

För att du ska kunna ställa in anpassade aviseringar och åtgärder lagras alla datadriftmått i [application insights-resursen](how-to-enable-app-insights.md) som skapades tillsammans med arbetsytan Azure Machine Learning. Du kan följa länken i e-postaviseringen till frågan Application Insights.

![E-postavisering för datadrift](./media/how-to-monitor-data-drift/drift_email.png)

## <a name="retrain-your-model-after-drift"></a>Omskola din modell efter drift

När datadrift negativt påverkar prestanda för din distribuerade modell, är det dags att omskola modellen. Det gör du genom att fortsätta med följande steg.

* Undersök de insamlade uppgifterna och förbered data för att träna den nya modellen.
* Dela upp den i tåg-/testdata.
* Träna modellen igen med hjälp av de nya data.
* Utvärdera prestanda för den nygenererade modellen.
* Distribuera ny modell om prestanda är bättre än produktionsmodellen.

## <a name="next-steps"></a>Nästa steg

* Ett fullständigt exempel på hur du använder datadrift finns i [Azure ML-datadrift notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/monitor-models/data-drift/drift-on-aks.ipynb). Den här Jupyter-anteckningsboken demonstrerar med hjälp av en [Azure Open Dataset](https://docs.microsoft.com/azure/open-datasets/overview-what-are-open-datasets) för att träna en modell för att förutsäga vädret, distribuera den till AKS och övervaka datadrift. 

* Identifiera datadrift med [datauppsättningsövervakare](how-to-monitor-datasets.md).

* Vi skulle uppskatta dina frågor, kommentarer eller förslag när datadrift rör sig mot allmän tillgänglighet. Använd knappen för produktfeedback nedan! 
