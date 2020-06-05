---
title: Identifiera data drift på AKS-distributioner
titleSuffix: Azure Machine Learning
description: Identifiera data drift (för hands version) i Azure Kubernetes-tjänst distribuerade modeller i Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: jmartens
ms.author: copeters
author: cody-dkdc
ms.date: 11/04/2019
ms.openlocfilehash: 0f56ab853983ebf9b3e27f38ae1737c0c2bce4ed
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2020
ms.locfileid: "84430293"
---
# <a name="detect-data-drift-preview-on-models-deployed-to-azure-kubernetes-service-aks"></a>Identifiera data drift (för hands version) i modeller som distribuerats till Azure Kubernetes service (AKS)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

I den här artikeln får du lära dig hur du övervakar data drift mellan inlärnings data uppsättningen och data härlednings data i en distribuerad modell. I samband med Machine Learning kan tränade maskin inlärnings modeller uppleva försämrade förutsägelser på grund av driften. Med Azure Machine Learning kan du övervaka data driften och tjänsten kan skicka en e-postavisering till dig när en avvikelse upptäcks.

## <a name="what-is-data-drift"></a>Vad är data avvikelser?

I samband med Machine Learning är data driften ändringen i modell indata som leder till modell prestanda försämring. Det är en av de främsta orsakerna till att modell precisionen försämras över tid, vilket innebär att övervakning av data driften hjälper till att identifiera problem med modell prestanda. 

## <a name="what-can-i-monitor"></a>Vad kan jag övervaka?

Med Azure Machine Learning kan du övervaka indata till en modell som distribueras på AKS och jämföra dessa data med data uppsättningen för träning för modellen. Med jämna mellanrum är det en [ögonblicks bild och profilerad](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py), som sedan beräknas mot bas linje data uppsättningen för att skapa en data avvikelse analys som: 

+ Mäter omfattningen av data avvikelsen, som kallas för drift koefficienten.
+ Mäter data drifts bidraget efter funktion, som anger vilka funktioner som orsakade data drift.
+ Mäter avstånds mått. För närvarande beräknas Wasserstein och energi avstånd.
+ Mäter distribution av funktioner. Uppskattning och histogram för aktuell kernel-densitet.
+ Skicka aviseringar till data drift per e-post.

> [!Note]
> Den här tjänsten är i (för hands version) och är begränsad i konfigurations alternativen. Information och uppdateringar finns i vår [API-dokumentation](https://docs.microsoft.com/python/api/azureml-datadrift/) och [viktig](azure-machine-learning-release-notes.md) information. 

### <a name="how-data-drift-is-monitored-in-azure-machine-learning"></a>Så här övervakas data driften i Azure Machine Learning

Med hjälp av Azure Machine Learning övervakas data driften via data uppsättningar eller distributioner. För att övervaka data, en bas linje uppsättning – vanligt vis är inlärnings data uppsättningen för en modell-angiven. En andra data uppsättning – vanligt vis modell indata som samlas in från en distribution, testas mot bas linje data uppsättningen. Båda data uppsättningarna är profilerade och indata för data tjänst övervaknings tjänsten. En maskin inlärnings modell är utbildad för att identifiera skillnader mellan de två data uppsättningarna. Modellens prestanda konverteras till drivgarn, som mäter storleken på driften mellan de två data uppsättningarna. Med [modell tolkning](how-to-machine-learning-interpretability.md)kan de funktioner som bidrar till drifts koefficienten beräknas. Statistisk information om varje funktion spåras från data uppsättnings profilen. 

## <a name="prerequisites"></a>Krav

- En Azure-prenumeration. Om du inte har ett konto kan du skapa ett kostnads fritt konto innan du börjar. Prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree) idag.

- Azure Machine Learning SDK för python har installerats. Följ anvisningarna på [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) för att göra följande:

    - Skapa en Miniconda-miljö
    - Installera Azure Machine Learning SDK för python

- En [Azure Machine Learning-arbetsyta](how-to-manage-workspace.md).

- En [konfigurations fil](how-to-configure-environment.md#workspace)för arbets ytan.

- Installera data drivgarn SDK med följande kommando:

    ```shell
    pip install azureml-datadrift
    ```

- Skapa en [data uppsättning](how-to-create-register-datasets.md) från din modells utbildnings data.

- Ange inlärnings data uppsättningen när modellen [registreras](concept-model-management-and-deployment.md) . Följande exempel visar hur du använder- `datasets` parametern för att ange inlärnings data uppsättningen:

    ```python
    model = Model.register(model_path=model_file,
                        model_name=model_name,
                        workspace=ws,
                        datasets=datasets)

    print(model_name, image_name, service_name, model)
    ```

- [Aktivera modell data insamling](how-to-enable-data-collection.md) för att samla in data från AKS-distributionen av modellen och bekräfta att data samlas in i `modeldata` BLOB-behållaren.

## <a name="configure-data-drift"></a>Konfigurera data avvikelse
Om du vill konfigurera data drift för experimentet kan du importera beroenden som visas i följande python-exempel. 

I det här exemplet visas hur du konfigurerar [`DataDriftDetector`](/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector) objektet:

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

Med `DataDriftDetector` objektet konfigurerat kan du skicka en [data körnings körning](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector#run-target-date--services-none--compute-target-none--create-compute-target-false--feature-list-none--drift-threshold-none-) på ett visst datum för modellen. Som en del av körningen aktiverar du DataDriftDetector-aviseringar genom att ange `drift_threshold` parametern. Om [datadrift_coefficient](#visualize-drift-metrics) är över angivet `drift_threshold` , skickas ett e-postmeddelande.

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

## <a name="visualize-drift-metrics"></a>Visualisera avvikelse mått

<a name="metrics"></a>

När du har skickat in din DataDriftDetector-körning kan du se de drift mått som sparas i varje körnings iteration för en data avvikelse:


|Metric|Beskrivning|
--|--|
wasserstein_distance|Statistisk avstånd har definierats för en dimensionell numerisk distribution.|
energy_distance|Statistisk avstånd har definierats för en dimensionell numerisk distribution.|
datadrift_coefficient|Beräknas på samma sätt som Matthew, men utdata är ett reellt tal mellan 0 och 1. I samband med en drifts omgivning anger 0 ingen avvikelse och 1 anger maximal avvikelse.|
datadrift_contribution|Funktions betydelsen hos funktioner som bidrar till drift.|

Det finns flera sätt att Visa avvikelse mått:

* Använd `RunDetails` [widgeten Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py).
* Använd [`get_metrics()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py#get-metrics-name-none--recursive-false--run-type-none--populate-false-) funktionen på alla `datadrift` körnings objekt.
* Visa måtten från **modell** avsnittet på arbets ytan i [Azure Machine Learning Studio](https://ml.azure.com).

Följande python-exempel visar hur du kan rita relevanta mått för data avvikelser. Du kan använda de returnerade måtten för att bygga anpassade visualiseringar:

```python
# start and end are datetime objects 
drift_metrics = datadrift.get_output(start_time=start, end_time=end)

# Show all data drift result figures, one per service.
# If setting with_details is False (by default), only the data drift magnitude will be shown; if it's True, all details will be shown.
drift_figures = datadrift.show(with_details=True)
```

![Se data avvikelse som upptäckts av Azure Machine Learning](./media/how-to-monitor-data-drift/drift_show.png)


## <a name="schedule-data-drift-scans"></a>Schemalägga data avvikelser 

När du aktiverar data identifieringen körs en DataDriftDetector med den angivna schemalagda frekvensen. Om datadrift_coefficient uppnås `drift_threshold` skickas ett e-postmeddelande med varje schemalagd körning. 

```python
datadrift.enable_schedule()
datadrift.disable_schedule()
```

Konfigurationen av data avvikelse detektorn visas under **modeller** på fliken **information** på arbets ytan i [Azure Machine Learning Studio](https://ml.azure.com).

[![Data avvikelse i Azure Machine Learning Studio](./media/how-to-monitor-data-drift/drift-config.png)](media/how-to-monitor-data-drift/drift-config-expanded.png)

## <a name="view-results-in-your-azure-machine-learning-studio"></a>Visa resultat i Azure Machine Learning Studio

Om du vill visa resultat i din arbets yta i [Azure Machine Learning Studio](https://ml.azure.com)går du till sidan modell. På fliken information i modellen visas konfigurationen för data riktning. En **datadrivgarn** -flik är nu tillgänglig för visualisering av data drifts måtten. 

[![Data avvikelse i Azure Machine Learning Studio](./media/how-to-monitor-data-drift/drift-ui.png)](media/how-to-monitor-data-drift/drift-ui-expanded.png)

## <a name="receiving-drift-alerts"></a>Ta emot larm

Genom att ange ett tröskelvärde för aktive ras aviserings tröskel och tillhandahålla en e-postadress skickas en [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) e-postavisering automatiskt när en avvikelse är över tröskeln. 

För att du ska kunna ställa in anpassade aviseringar och åtgärder lagras alla data inriktnings mått i den [Application Insights](how-to-enable-app-insights.md) resurs som skapades tillsammans med arbets ytan Azure Machine Learning. Du kan följa länken i e-postaviseringen till den Application Insights frågan.

![E-postavisering om data larm](./media/how-to-monitor-data-drift/drift_email.png)

## <a name="retrain-your-model-after-drift"></a>Träna om din modell efter drift

När data påverkar negativ påverkan på den distribuerade modellens prestanda är det dags att omträna modellen. Det gör du genom att gå vidare med följande steg.

* Undersök insamlade data och Förbered data för att träna den nya modellen.
* Dela upp den i träna/testa data.
* Träna modellen igen med nya data.
* Utvärdera prestanda för den nyligen genererade modellen.
* Distribuera ny modell om prestanda är bättre än produktions modellen.

## <a name="next-steps"></a>Nästa steg

* Ett komplett exempel på hur du använder data drift finns i den [bärbara datorn för Azure ml data drivgarn](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/monitor-models/data-drift/drift-on-aks.ipynb). Den här Jupyter Notebook visar hur du använder en [Azure Open-datauppsättning](https://docs.microsoft.com/azure/open-datasets/overview-what-are-open-datasets) för att träna en modell för att förutsäga väder, distribuera den till AKS och övervaka data driften. 

* Identifiera data drivgarn med [data uppsättnings övervakare](how-to-monitor-datasets.md).

* Vi skulle kraftigt uppskatta dina frågor, kommentarer eller förslag när data driften flyttas mot allmän tillgänglighet. Använd knappen produkt feedback nedan! 
