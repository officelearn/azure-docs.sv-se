---
title: Aktivera loggning i Azure Machine Learning-tjänsten
titleSuffix: Azure Machine Learning service
description: Lär dig hur du aktiverar loggning i Azure Machine Learning-tjänsten med både standard Python-paketet för loggning, samt med hjälp av SDK-specifika funktioner.
ms.author: trbye
author: trevorbye
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: trbye
ms.date: 02/20/2019
ms.openlocfilehash: 08e83cdcadabdcf7234d0bbd0fb7e6d103c8369c
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/20/2019
ms.locfileid: "56447539"
---
# <a name="enable-logging-in-azure-machine-learning-service"></a>Aktivera loggning i Azure Machine Learning-tjänsten

Azure Machine Learning Python SDK kan du aktivera loggning med hjälp av båda Standardloggning Python-paketet, samt med hjälp av SDK-specifika funktioner både för lokala logga och logga till din arbetsyta i portalen. Loggar ger utvecklare tillgång realtidsinformation om programmets tillstånd och kan hjälpa till med att diagnostisera fel eller varningar. I den här artikeln får du lära dig olika sätt för att aktivera loggning inom följande områden:

> [!div class="checklist"]
> * Modeller för utbildning och beräkningsmål
> * Skapa avbildningar
> * Distribuerade modeller
> * Python `logging` inställningar

Använd den [guide](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) att installera SDK, och [börjar](https://docs.microsoft.com/azure/machine-learning/service/quickstart-create-workspace-with-python) med SDK för att skapa en arbetsyta i Azure Portal.

## <a name="training-models-and-compute-target-logging"></a>Modeller för utbildning och beräkning target loggning

Det finns flera sätt att aktivera loggning under modellen utbildning och exemplen illustrerar vanliga designmönster. Du kan enkelt logga kör-relaterade data till din arbetsyta i molnet med hjälp av den `start_logging` fungera på den `Experiment` klass.

```python
from azureml.core import Experiment

exp = Experiment(workspace=ws, name='test_experiment')
run = exp.start_logging()
run.log("test-val", 10)
```

Finns i referensen [dokumentation](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py) för den `Run` klass för ytterligare loggningsfunktioner.

Aktivera lokal loggning av programtillstånd under utbildning förloppet genom att använda den `show_output` parametern. Aktivera utförlig loggning kan du visa information från träningsprocess samt information om alla fjärranslutna resurser eller beräkningsmål. Använd följande kod för att aktivera loggning när experiment.

```python
from azureml.core import Experiment

experiment = Experiment(ws, experiment_name)
run = experiment.submit(config=run_config_object, show_output=True)
```

Du kan också använda samma parameter i den `wait_for_completion` funktionen på den resulterande körningen.

```python
run.wait_for_completion(show_output=True)
```

SDK stöder också med hjälp av standard python loggning paketet i vissa scenarion för utbildning. I följande exempel aktiveras ett loggningsnivån för `INFO` i en `AutoMLConfig` objekt.

```python
from azureml.train.automl import AutoMLConfig
import logging

automated_ml_config = AutoMLConfig(task = 'regression',
                                   verbosity=logging.INFO,
                                   X=your_training_features,
                                   y=your_training_labels,
                                   iterations=30,
                                   iteration_timeout_minutes=5,
                                   primary_metric="spearman_correlation")
```

Du kan också använda den `show_output` parameter när du skapar en beständig beräkningsmål. Ange parametern i den `wait_for_completion` funktionen för att aktivera loggning när beräkning target skapas.

```python
from azureml.core.compute import ComputeTarget

compute_target = ComputeTarget.attach(workspace=ws, name="example", attach_configuration=config)
compute.wait_for_completion(show_output=True)
```

## <a name="logging-during-image-creation"></a>Loggningen under Skapa avbildningar

Att aktivera loggning under Skapa avbildningar kan du se några fel under skapandeprocessen. Ange den `show_output` param på den `wait_for_deployment()` funktion.

```python
from azureml.core.webservice import Webservice

service = Webservice.deploy_from_image(deployment_config=your_config,
                                            image=image,
                                            name="example-image",
                                            workspace=ws)

service.wait_for_deployment(show_output=True)
```

## <a name="logging-for-deployed-models"></a>Loggning för distribuerade modeller

Läsa in tjänsten om du vill hämta loggar från en tidigare distribuerad webbtjänst, och använda den `get_logs()` funktion. Loggarna kan innehålla detaljerad information om eventuella fel som uppstod under distributionen.

```python
from azureml.core.webservice import Webservice

# load existing web service
service = Webservice(name="service-name", workspace=ws)
logs = service.get_logs()
```

Du kan också logga anpassade Stacka spårningar för webbtjänsten genom att aktivera Application Insights, vilket gör att du kan övervaka begäran/svarstider, Felfrekvens och undantag. Anropa den `update()` funktionen på en befintlig webbtjänst för att aktivera Application Insights.

```python
service.update(enable_app_insights=True)
```

Se den [how-to](how-to-enable-app-insights.md#enable-and-disable-in-the-portal) mer information om hur du arbetar med Application Insights i Azure-portalen.

## <a name="python-native-logging-settings"></a>Python interna loggningsinställningar

Vissa loggar i SDK: N kan innehålla ett fel där du uppmanas att ange loggningsnivån till DEBUG. Ange loggningsnivån genom att lägga till följande kod i skriptet.

```python
import logging
logging.basicConfig(level=logging.DEBUG)
```