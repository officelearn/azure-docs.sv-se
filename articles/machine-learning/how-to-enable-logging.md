---
title: Aktivera loggning i Azure Machine Learning
description: Lär dig hur du aktiverar loggning i Azure Machine Learning med både standardpaketet för Python-loggning och med SDK-specifika funktioner.
ms.author: trbye
author: trevorbye
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: trbye
ms.date: 03/05/2020
ms.openlocfilehash: 73b9ae6bc3c15526bfdafd74330c7b86286631b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78396151"
---
# <a name="enable-logging-in-azure-machine-learning"></a>Aktivera loggning i Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure Machine Learning Python SDK kan du aktivera loggning med både standard Python loggningspaket, samt med SDK-specifika funktioner både för lokal loggning och loggning till din arbetsyta i portalen. Loggar ger utvecklare information i realtid om programmets tillstånd och kan hjälpa till med att diagnostisera fel eller varningar. I den här artikeln kan du lära dig olika sätt att aktivera loggning i följande områden:

> [!div class="checklist"]
> * Träningsmodeller och beräkningsmål
> * Skapa bilder
> * Distribuerade modeller
> * Python-inställningar `logging`

[Skapa en Azure Machine Learning-arbetsyta](how-to-manage-workspace.md). Använd [guiden](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) för mer information SDK.

## <a name="training-models-and-compute-target-logging"></a>Träningsmodeller och beräkningsmålloggning

Det finns flera sätt att aktivera loggning under modellutbildningsprocessen, och de exempel som visas illustrerar vanliga designmönster. Du kan enkelt logga körningsrelaterade data till din `start_logging` arbetsyta i `Experiment` molnet med hjälp av funktionen på klassen.

```python
from azureml.core import Experiment

exp = Experiment(workspace=ws, name='test_experiment')
run = exp.start_logging()
run.log("test-val", 10)
```

Mer information om loggningsfunktioner finns i referensdokumentationen för klassen [Run.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py)

Om du vill aktivera lokal loggning av `show_output` programtillstånd under utbildningsstatus använder du parametern. Om du aktiverar utförlig loggning kan du se information från träningsprocessen samt information om eventuella fjärrresurser eller beräkningsmål. Använd följande kod för att aktivera loggning vid experimentöverföring.

```python
from azureml.core import Experiment

experiment = Experiment(ws, experiment_name)
run = experiment.submit(config=run_config_object, show_output=True)
```

Du kan också använda samma `wait_for_completion` parameter i funktionen på den resulterande körningen.

```python
run.wait_for_completion(show_output=True)
```

SDK stöder också användning av standardpaketet för pythonloggning i vissa scenarier för utbildning. I följande exempel aktiveras en `INFO` loggningsnivå i ett `AutoMLConfig` objekt.

```python
from azureml.train.automl import AutoMLConfig
import logging

automated_ml_config = AutoMLConfig(task='regression',
                                   verbosity=logging.INFO,
                                   X=your_training_features,
                                   y=your_training_labels,
                                   iterations=30,
                                   iteration_timeout_minutes=5,
                                   primary_metric="spearman_correlation")
```

Du kan också `show_output` använda parametern när du skapar ett beständigt beräkningsmål. Ange parametern `wait_for_completion` i funktionen för att aktivera loggning när beräkningsmålet skapas.

```python
from azureml.core.compute import ComputeTarget

compute_target = ComputeTarget.attach(
    workspace=ws, name="example", attach_configuration=config)
compute.wait_for_completion(show_output=True)
```

## <a name="logging-for-deployed-models"></a>Loggning för distribuerade modeller

Om du vill hämta loggar från en tidigare distribuerad webbtjänst läser du in tjänsten och använder `get_logs()` funktionen. Loggarna kan innehålla detaljerad information om eventuella fel som uppstod under distributionen.

```python
from azureml.core.webservice import Webservice

# load existing web service
service = Webservice(name="service-name", workspace=ws)
logs = service.get_logs()
```

Du kan också logga anpassade stackspårningar för webbtjänsten genom att aktivera Application Insights, vilket gör att du kan övervaka begäran/svarstider, felfrekvenser och undantag. Anropa `update()` funktionen på en befintlig webbtjänst för att aktivera Application Insights.

```python
service.update(enable_app_insights=True)
```

Mer information finns i [Övervaka och samla in data från ML-webbtjänstslutpunkter](how-to-enable-app-insights.md).

## <a name="python-native-logging-settings"></a>Inställningarna för inbyggd Python-loggning

Vissa loggar i SDK kan innehålla ett fel som instruerar dig att ställa in loggningsnivån till DEBUG. Om du vill ange loggningsnivån lägger du till följande kod i skriptet.

```python
import logging
logging.basicConfig(level=logging.DEBUG)
```

## <a name="next-steps"></a>Nästa steg

* [Övervaka och samla in data från ML-webbtjänstslutpunkter](how-to-enable-app-insights.md)