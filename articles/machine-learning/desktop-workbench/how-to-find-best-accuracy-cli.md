---
title: Hitta körningar med bäst Precision och lägst varaktighet i Azure Machine Learning Workbench | Microsoft Docs
description: En slutpunkt till slutpunkt användningsfall hitta bäst noggrannhet via CLI med hjälp av Azure Machine Learning Workbench
services: machine-learning
author: totekp
ms.author: kefzhou
manager: akannava
ms.reviewer: akannava, haining, mldocs, jmartens, jasonwhowell
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 09/29/2017
ms.openlocfilehash: d2fe951a97b18c95e647b45d799843a982100367
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "35966624"
---
# <a name="find-runs-with-the-best-accuracy-and-lowest-duration"></a>Hitta körningar med bäst Precision och lägst varaktighet
Får flera körningar är användningsfall att hitta körningar med bäst noggrannhet. En metod är att använda kommandoradsgränssnittet (CLI) med en [JMESPath](http://jmespath.org/) fråga. Mer information om hur du använder JMESPath i Azure CLI finns i [använda JMESPath-frågor med Azure CLI](https://docs.microsoft.com/cli/azure/query-azure-cli?view=azure-cli-latest). I följande exempel skapas fyra körs med Precision värden 0, 0,98, 1 och 1. Körningar filtreras om de är i intervallet `[MaxAccuracy-Threshold, MaxAccuracy]` där `Threshold = .03`.

## <a name="sample-data"></a>Exempeldata
Om du inte har befintliga körs med ett `Accuracy` värde, följande anvisningarna skapas körningar för frågor.

Först skapar en Python-fil i Azure Machine Learning Workbench, ge den namnet `log_accuracy.py`, och klistra in följande kod:
```python
from azureml.logging import get_azureml_logger

logger = get_azureml_logger()

accuracy_value = 0.5

if len(sys.argv) > 1:
     accuracy_value = float(sys.argv[1])

logger.log("Accuracy", accuracy_value)
```

Skapa sedan en fil `run.py`, och klistra in följande kod:
```python
import os

accuracy_values = [0, 0.98, 1.0, 1.0]
for value in accuracy_values:
    os.system('az ml experiment submit -c local ./log_accuracy.py {}'.format(value))
```

Till sist öppna CLI via Workbench och kör kommandot `python run.py` att skicka fyra experiment. När skriptet har slutförts bör du se fyra fler körningar i den `Run History` fliken.

## <a name="query-the-run-history"></a>Fråga körningshistorik
Det första kommandot efter värdet för maximal precision.
```powershell
az ml history list --query '@[?Accuracy != null] | max_by(@, &Accuracy).Accuracy'
```

Med det här värdet för maximal precision av `1` och ett tröskelvärde för `0.03`, andra kommandot filtrerar körs med hjälp av `Accuracy` och sedan sorterar körs med `duration` stigande.
```powershell
az ml history list --query '@[?Accuracy >= sum(`[1, -0.03]`)] | sort_by(@, &duration)'
```
> [!NOTE]
> Om du vill att en strikt kontroll för övre gräns, är fråga-formatet ``@[?Accuracy >= sum(`[$max_accuracy_value, -$threshold]`) && Accuracy <= `$max_accuracy_value`]``

Om du använder PowerShell använder lokala variabler i följande kod för att lagra tröskelvärde och maximal Precision:
```powershell
$threshold = 0.03
$max_accuracy_value = az ml history list --query '@[?Accuracy != null] | max_by(@, &Accuracy).Accuracy'
$find_runs_query = '@[?Accuracy >= sum(`[{0}, -{1}]`)] | sort_by(@, &duration)' -f $max_accuracy_value, $threshold
az ml history list --query $find_runs_query
```

## <a name="next-steps"></a>Nästa steg
Mer information om loggning finns i [hur du använder körningshistorik och modellen mått i Azure Machine Learning Workbench](how-to-use-run-history-model-metrics.md).    
