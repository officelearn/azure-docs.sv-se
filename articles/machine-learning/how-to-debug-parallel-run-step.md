---
title: Felsöka och felsöka ParallelRunStep
titleSuffix: Azure Machine Learning
description: Felsöka och felsöka ParallelRunStep i machine learning-pipelines i Azure Machine Learning SDK för Python. Lär dig vanliga fallgropar för att utveckla med pipelines och tips som hjälper dig att felsöka skripten före och under fjärrkörning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: trbye, jmartens, larryfr, vaidyas
ms.author: trmccorm
author: tmccrmck
ms.date: 01/15/2020
ms.openlocfilehash: ca50d70965d5edc4e31606e542ddf163fe3b0741
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76122969"
---
# <a name="debug-and-troubleshoot-parallelrunstep"></a>Felsöka och felsöka ParallelRunStep
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här artikeln får du lära dig hur du felsöker och felsöker [klassen ParallelRunStep](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallel_run_step.parallelrunstep?view=azure-ml-py) från [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

## <a name="testing-scripts-locally"></a>Testa skript lokalt

Se [avsnittet Testa skript lokalt](how-to-debug-pipelines.md#testing-scripts-locally) för pipelines för maskininlärning. Din ParallelRunStep körs som ett steg i ML-pipelines så samma svar gäller för båda.

## <a name="debugging-scripts-from-remote-context"></a>Felsöka skript från fjärrkontext

Övergången från att felsöka ett bedömningsskript lokalt till att felsöka ett bedömningsskript i en verklig pipeline kan vara ett svårt steg. Information om hur du hittar loggarna i portalen finns [avsnittet machine learning pipelines om felsökning av skript från en fjärrkontext](how-to-debug-pipelines.md#debugging-scripts-from-remote-context). Informationen i det avsnittet gäller även för en parallell stegkörning.

Loggfilen `70_driver_log.txt` innehåller till exempel information från styrenheten som startar stegkod för parallell körning.

På grund av den distribuerade karaktären av parallella körningsjobb finns det loggar från flera olika källor. Två konsoliderade filer skapas dock som ger information på hög nivå:

- `~/logs/overview.txt`: Den här filen ger en information på hög nivå om antalet minibatchar (kallas även uppgifter) som skapats hittills och antalet minibatchar som bearbetats hittills. I detta syfte visar det resultatet av jobbet. Om jobbet misslyckades visas felmeddelandet och var felsökningen ska börja.

- `~/logs/sys/master.txt`: Den här filen tillhandahåller huvudnoden (kallas även orchestrator) för det löpjobbet. Inkluderar skapande av uppgifter, förloppsövervakning, körningsresultatet.

Loggar som genereras från inmatningsskriptet med EntryScript.logger och utskriftssatser finns i följande filer:

- `~/logs/user/<ip_address>/Process-*.txt`: Den här filen innehåller loggar skrivna från entry_script med EntryScript.logger. Den innehåller också utskriftsuttryck (stdout) från entry_script.

När du behöver en fullständig förståelse för hur varje nod körde poängskriptet, titta på de enskilda processloggarna för varje nod. Processloggarna finns i `sys/worker` mappen, grupperade efter arbetsnoder:

- `~/logs/sys/worker/<ip_address>/Process-*.txt`: Den här filen innehåller detaljerad information om varje minibatch när den plockas upp eller slutförs av en arbetare. För varje minibatch innehåller den här filen:

    - IP-adressen och ARBETSPROCESSENs PID. 
    - Det totala antalet artiklar, bearbetade objekt och antal misslyckade objekt.
    - Starttid, varaktighet, processtid och körningsmetodtid.

Du kan också hitta information om resursanvändningen för processerna för varje anställd. Denna information är i CSV-format och finns på `~/logs/sys/perf/<ip_address>/`. För en enskild nod är jobbfiler tillgängliga under `~logs/sys/perf`. När du till exempel söker efter resursutnyttjande kan du titta på följande filer:

- `Process-*.csv`: Resursanvändning per arbetsprocess. 
- `sys.csv`: Per nodlogg.

### <a name="how-do-i-log-from-my-user-script-from-a-remote-context"></a>Hur loggar jag från mitt användarskript från en fjärrkontext?
Du kan hämta en logger från EntryScript som visas i under exempelkoden för att göra loggarna visas i **loggar / användarmapp** i portalen.

**Ett exempel på ett inmatningsskript med loggern:**
```python
from entry_script import EntryScript

def init():
    """ Initialize the node."""
    entry_script = EntryScript()
    logger = entry_script.logger
    logger.debug("This will show up in files under logs/user on the Azure portal.")


def run(mini_batch):
    """ Accept and return the list back."""
    # This class is in singleton pattern and will return same instance as the one in init()
    entry_script = EntryScript()
    logger = entry_script.logger
    logger.debug(f"{__file__}: {mini_batch}.")
    ...

    return mini_batch
```

### <a name="how-could-i-pass-a-side-input-such-as-a-file-or-files-containing-a-lookup-table-to-all-my-workers"></a>Hur kan jag skicka en sidoinmatning, till exempel en fil eller fil som innehåller en uppslagstabell, till alla mina arbetare?

Konstruera ett [datauppsättningsobjekt](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) som innehåller sidoindata och registrera med arbetsytan. Efter att du kan komma åt den i din inferens skript (till exempel i din init() metod) enligt följande:

```python
from azureml.core.run import Run
from azureml.core.dataset import Dataset

ws = Run.get_context().experiment.workspace
lookup_ds = Dataset.get_by_name(ws, "<registered-name>")
lookup_ds.download(target_path='.', overwrite=True)
```

## <a name="next-steps"></a>Nästa steg

* Se SDK-referensen för hjälp med [azureml-contrib-pipeline-steg-paketet](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps?view=azure-ml-py) och [dokumentationen](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallelrunstep?view=azure-ml-py) för Klassen ParallelRunStep.

* Följ den [avancerade självstudien](tutorial-pipeline-batch-scoring-classification.md) om hur du använder pipelines med steg för parallell körning.
