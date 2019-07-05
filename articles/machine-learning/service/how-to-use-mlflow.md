---
title: Använda MLflow med Azure Machine Learning-tjänsten
titleSuffix: Azure Machine Learning service
description: Lär dig hur du loggar in mått och artefakter med MLflow-biblioteket till Azure Machine Learning-tjänsten
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.topic: conceptual
ms.date: 06/10/2019
ms.custom: seodec18
ms.openlocfilehash: d0bc4620d0c55d6e94a3d99c39ab405dab2743e5
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67461658"
---
# <a name="use-mlflow-with-azure-machine-learning-service-preview"></a>Använda MLflow med Azure Machine Learning-tjänsten (förhandsversion)

Den här artikeln visar hur du använder Mlflows spåra URI och loggning API, gemensamt kallas även MLflow spårning, med Azure Machine Learning-tjänsten för att spåra och logga dina experiment mätvärden och artefakter i din [Azure Machine Learning arbetsyta](https://docs.microsoft.com/azure/machine-learning/service/concept-azure-machine-learning-architecture#workspace). Om du redan använder MLflow spårning för dina experiment, tillhandahåller arbetsytan en centraliserad, säker och skalbar plats att lagra dina modeller och utbildning mått.

[MLflow](https://www.mlflow.org) är ett bibliotek med öppen källkod för att hantera livscykeln för din machine learning-experiment. [Spårning av MLFlow](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api) är en komponent i MLflow som loggar och spårar utbildning kör mått och modellen artefakter, oavsett om dina experiment körs lokalt, på en virtuell dator eller på en fjärransluten beräkningskluster.
![mlflow med azure machine learning-diagram](media/how-to-use-mlflow/mlflow-diagram.png)

## <a name="compare-mlflow-and-azure-machine-learning-clients"></a>Jämför MLflow och Azure Machine Learning-klienter

 I tabellen nedan sammanfattas de olika klienterna som kan använda Azure Machine Learning-tjänsten och deras respektive funktion-funktioner.

 Spårning av MLflow erbjuder mått loggning och artefakt storage-funktioner som annars bara är tillgängliga via den [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

| | MLflow spårning | Azure Machine Learning <br> Python SDK |  Azure Machine Learning <br> CLI | Azure Portal|
|-|-|-|-|-|
| Hantera arbetsyta |   | ✓ |  ✓ | ✓  |
| Använd datalager  |   | ✓ |  ✓ |    |
| Log-mått      | ✓ | ✓ |    |    |
| Ladda upp artefakter | ✓ | ✓ |    |    |
| Visa mått     | ✓ | ✓ | ✓  | ✓ |
| Hantera beräkning   |   | ✓ | ✓  | ✓ |
| Distribuera modeller    |   | ✓ |   ✓ | ✓ |

## <a name="prerequisites"></a>Förutsättningar

* [Installera MLflow.](https://mlflow.org/docs/latest/quickstart.html)
* [Installera Azure Machine Learning Python SDK på den lokala datorn och skapa en Azure Machine Learning-arbetsyta](setup-create-workspace.md#sdk). SDK innehåller anslutning för MLflow att få åtkomst till din arbetsyta.

## <a name="track-local-runs"></a>Spåra lokala körningar

Installera den `azureml-contrib-run` paket som ska användas MLflow spårning med Azure Machine Learning på dina experiment som körs lokalt i ett redigeringsprogram för Jupyter-anteckningsbok eller kod.

```shell
pip install azureml-contrib-run
```

>[!NOTE]
>Namnområdet azureml.contrib ändras ofta, när vi arbetar för att förbättra tjänsten. Därför ska någonting i det här namnområdet räknas som en förhandsversion, och stöds inte fullt ut av Microsoft.

Importera den `mlflow` och [ `Workspace` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) klasser för att komma åt MLflow spåra URI och konfigurera din arbetsyta.

I följande kod i `get_mlflow_tracking_uri()` metoden tilldelar en unik spårning URI-adress till arbetsytan, `ws`, och `set_tracking_uri()` pekar MLflow spåra URI till adressen.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
>Spårnings-URI är giltigt upp till en timme eller mindre. Om du startar om skriptet efter en inaktiv tid kan du använda get_mlflow_tracking_uri API för att få en ny URI.

Ange namnet på experimentet MLflow med `set_experiment()` och börja Utbilda dig köra med `start_run()`. Använd sedan `log_metric()` att aktivera loggning för MLflow API: et och börjar logga utbildning kör mått.

```Python
experiment_name = "experiment_with_mlflow"
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    mlflow.log_metric('alpha', 0.03)
```

## <a name="track-remote-runs"></a>Spåra remote körningar

Remote körs kan du träna modeller på mer kraftfulla beräkningar som grafikprocessoraktiverade virtuella datorer eller beräkning av Machine Learning-kluster. Se [konfigurera beräkningsmål för modellträning](how-to-set-up-training-targets.md) vill veta mer om olika beräkningsalternativ.

Konfigurera din beräknings- och kör miljö med den [ `Environment` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) klass. Inkludera `mlflow` och `azure-contrib-run` pip-paketen i miljöns [ `CondaDependencies` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) avsnittet. Sedan skapar [ `ScriptRunConfig` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) med din fjärranslutna beräkning som beräkningsmål.

```Python

from azureml.core import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core import ScriptRunConfig

exp = Experiment(workspace = "my_workspace",
                 name = "my_experiment")

mlflow_env = Environment(name="mlflow-env")

cd = CondaDependencies.create(pip_packages = ["mlflow", "azureml-contrib-run"])

mlflow_env.python.conda_dependencies=cd

src = ScriptRunConfig(source_directory="./my_script_location", script="my_training_script.py")

src.run_config.target = "my-remote-compute-compute"
src.run_config.environment = mlflow_env
```

I din utbildning-skriptet importerar `mlflow` att använda MLflow loggning API: er och börja logga dina kör mått.

```Python
import mlflow

with mlflow.start_run():
    mlflow.log_metric("example", 1.23)
```

Med den här beräknings- och utbildning som kör configuration, använda den `Experiment.submit("train.py")` metod för att skicka en körning. Detta anger MLflow spåra URI och automatiskt dirigerar loggning från MLflow till din arbetsyta.

```Python
run = exp.submit(src)
```

## <a name="view-metrics-and-artifacts-in-your-workspace"></a>Visa mått och artefakter i din arbetsyta

Mått och artefakter från MLflow loggning sparas på din arbetsyta på den [Azure-portalen](https://portal.azure.com). Om du vill visa dem när som helst, navigera till din arbetsyta och hitta experimentet efter namn.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte planerar att använda loggade mått och artefakter i din arbetsyta, är möjlighet att ta bort dem individuellt inte tillgänglig. I stället tar du bort resursgruppen som innehåller lagringskontot och arbetsytan så att du inte betala något:

1. I Azure-portalen väljer du **Resursgrupper** längst till vänster.

   ![Ta bort i Azure Portal](media/how-to-use-mlflow/delete-resources.png)

1. Välj den resursgrupp i listan som du har skapat.

1. Välj **Ta bort resursgrupp**.

1. Ange resursgruppsnamnet. Välj sedan **Ta bort**.

## <a name="example-notebooks"></a>Exempel-anteckningsböcker

Den [MLflow med Azure ML-anteckningsböcker](https://aka.ms/azureml-mlflow-examples) demonstrerar begreppen i den här artikeln.

## <a name="next-steps"></a>Nästa steg

* [Hur du distribuerar en modell](how-to-deploy-and-where.md).
