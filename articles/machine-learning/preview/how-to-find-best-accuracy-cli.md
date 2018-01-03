---
title: "Hitta körs med bästa riktigheten och lägsta varaktighet i Azure Machine Learning arbetsstationen | Microsoft Docs"
description: "En slutpunkt till slutpunkt användningsfall hitta bästa noggrannhet via CLI med Azure Machine Learning arbetsstationen"
services: machine-learning
author: totekp
ms.author: kefzhou
manager: akannava
ms.reviewer: akannava, haining, mldocs, garyericson, jasonwhowell
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/29/2017
ms.openlocfilehash: 40e066fe602e8c4680043158f1d401a884e07c19
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2017
---
# <a name="find-runs-with-the-best-accuracy-and-lowest-duration"></a>Hitta körs med bästa riktigheten och lägsta varaktighet
Flera körs är, användningsfall att hitta körs med bästa noggrannhet. En metod är att använda kommandoradsgränssnittet (CLI) med en [JMESPath](http://jmespath.org/) frågan. Mer information om hur du använder JMESPath i Azure CLI finns [använder JMESPath frågor med Azure CLI 2.0](https://docs.microsoft.com/cli/azure/query-azure-cli?view=azure-cli-latest). I följande exempel skapas fyra körs med korrekta värden 0, 0,98, 1 och 1. Kör filtreras om de är i intervallet `[MaxAccuracy-Threshold, MaxAccuracy]` där `Threshold = .03`.

## <a name="sample-data"></a>Exempeldata
Om du inte har befintliga körs med en `Accuracy` värdet följande steg generera körs för frågor.

Först skapar en Python-fil i Azure Machine Learning-arbetsstationen, ge den namnet `log_accuracy.py`, och klistra in följande kod:
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

Slutligen, öppna CLI via arbetsstationen och kör kommandot `python run.py` att skicka fyra experiment. När skriptet har slutförts, bör du se fyra mer körs i den `Run History` fliken.

## <a name="query-the-run-history"></a>Frågan körningshistoriken
Det första kommandot hittar värdet för max noggrannhet.
```powershell
az ml history list --query '@[?Accuracy != null] | max_by(@, &Accuracy).Accuracy'
```

Med hjälp av värdet för maximal Precision `1` och ett tröskelvärde för `0.03`, andra kommandot filtrerar körs med hjälp av `Accuracy` och sedan sorterar körs av `duration` stigande.
```powershell
az ml history list --query '@[?Accuracy >= sum(`[1, -0.03]`)] | sort_by(@, &duration)'
```
> [!NOTE]
> Om du vill att en strikt övre gräns-kontroll, är fråga-formatet``@[?Accuracy >= sum(`[$max_accuracy_value, -$threshold]`) && Accuracy <= `$max_accuracy_value`]``

Följande kod använder lokala variabler för att lagra tröskelvärde och maximala Precision om du använder PowerShell:
```powershell
$threshold = 0.03
$max_accuracy_value = az ml history list --query '@[?Accuracy != null] | max_by(@, &Accuracy).Accuracy'
$find_runs_query = '@[?Accuracy >= sum(`[{0}, -{1}]`)] | sort_by(@, &duration)' -f $max_accuracy_value, $threshold
az ml history list --query $find_runs_query
```

## <a name="next-steps"></a>Nästa steg
Mer information om loggning finns [hur du använder körningshistorik och modellen mått i Azure Machine Learning arbetsstationen](how-to-use-run-history-model-metrics.md).    
