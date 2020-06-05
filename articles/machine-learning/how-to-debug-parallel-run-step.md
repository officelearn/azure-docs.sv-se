---
title: Felsök och felsöka ParallelRunStep
titleSuffix: Azure Machine Learning
description: Felsöka och Felsök ParallelRunStep i Machine Learning-pipeliner i Azure Machine Learning SDK för python. Lär dig vanliga fall GRO par för utveckling med pipelines och tips som hjälper dig att felsöka skript före och under fjärrkörning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: troubleshooting
ms.reviewer: trbye, jmartens, larryfr, vaidyas
ms.author: trmccorm
author: tmccrmck
ms.date: 01/15/2020
ms.openlocfilehash: 7f05133f15f1df39a61c34b43f18828ee494b735
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2020
ms.locfileid: "84433449"
---
# <a name="debug-and-troubleshoot-parallelrunstep"></a>Felsök och felsöka ParallelRunStep
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här artikeln får du lära dig att felsöka och felsöka klassen [ParallelRunStep](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallel_run_step.parallelrunstep?view=azure-ml-py) från [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

## <a name="testing-scripts-locally"></a>Testa skript lokalt

Se [avsnittet testa skript lokalt](how-to-debug-pipelines.md#testing-scripts-locally) för Machine Learning-pipeliner. Din ParallelRunStep körs som ett steg i ML-pipelines så att samma svar gäller båda.

## <a name="debugging-scripts-from-remote-context"></a>Felsöka skript från fjärr kontext

Över gången från att felsöka ett bedömnings skript lokalt för att felsöka ett bedömnings skript i en faktisk pipeline kan vara ett svårt kliv. Information om hur du hittar dina loggar i portalen finns i [avsnittet Machine Learning-pipeline för att felsöka skript från en fjärran sluten kontext](how-to-debug-pipelines.md#debugging-scripts-from-remote-context). Informationen i avsnittet gäller även för en ParallelRunStep.

Logg filen innehåller till exempel `70_driver_log.txt` information från den kontrollant som startar ParallelRunStep-koden.

På grund av den distribuerade typen av ParallelRunStep-jobb finns det loggar från flera olika källor. Två konsoliderade filer skapas dock som tillhandahåller information på hög nivå:

- `~/logs/overview.txt`: Den här filen innehåller en information på hög nivå om antalet mini-batchar (även kallade aktiviteter) som skapats hittills och antalet mini-batchar som bearbetats hittills. I det här slutet visas resultatet av jobbet. Om jobbet misslyckades visas fel meddelandet och var du ska starta fel sökningen.

- `~/logs/sys/master.txt`: Den här filen ger huvudnoden (även kallat Orchestrator) vyn för jobbet som körs. Innehåller skapande av aktiviteter, förlopps övervakning, körnings resultat.

Loggar som genereras från ett Entry-skript med hjälp av EntryScript-och Print-instruktioner finns i följande filer:

- `~/logs/user/<ip_address>/<node_name>.log.txt`: Dessa loggar skrivs från entry_script med hjälp av EntryScript-hjälpen. Innehåller också ett Skriv uttryck (STDOUT) från entry_script.

En kortfattad förståelse för fel i skriptet finns i:

- `~/logs/user/error.txt`: Den här filen kommer att försöka sammanfatta felen i skriptet.

För mer information om fel i skriptet, finns det:

- `~/logs/user/error/`: Innehåller alla fel som har utlösts och fullständiga stack spårningar ordnade efter nod.

När du behöver en fullständig förståelse för hur varje nod kör Poäng skriptet kan du titta på de enskilda process loggarna för varje nod. Process loggarna finns i `sys/node` mappen grupperade efter arbetsnoder:

- `~/logs/sys/node/<node_name>.txt`: Den här filen innehåller detaljerad information om varje mini-batch som den hämtas eller slutförs av en anställd. För varje mini-batch innehåller den här filen:

    - IP-adressen och PID för arbets processen. 
    - Det totala antalet objekt, antalet behandlade objekt och antalet misslyckade objekt.
    - Tid för start tid, varaktighet, bearbetnings tid och körnings metod.

Du kan också hitta information om resursanvändningen för processerna för varje arbets tagare. Den här informationen är i CSV-format och finns på `~/logs/sys/perf/overview.csv` . Information om varje process finns i `~logs/sys/processes.csv` .

### <a name="how-do-i-log-from-my-user-script-from-a-remote-context"></a>Hur gör jag för att logg från mitt användar skript från en fjärran sluten kontext?
Du kan hämta en loggare från EntryScript så som visas i nedanstående exempel kod för att visa loggarna i **loggar/User** -mappen i portalen.

**Ett exempel på ett post skript med hjälp av loggarna:**
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

### <a name="how-could-i-pass-a-side-input-such-as-a-file-or-files-containing-a-lookup-table-to-all-my-workers"></a>Hur kan jag skicka in en indata, till exempel en eller flera filer som innehåller en uppslags tabell, till alla mina anställda?

Skapa en [data uppsättning](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) som innehåller indata från sidan och registrera den med din arbets yta. Skicka den till- `side_input` parametern för din `ParallelRunStep` . Dessutom kan du lägga till dess sökväg i `arguments` avsnittet för att enkelt komma åt den monterade sökvägen:

```python
label_config = label_ds.as_named_input("labels_input")
batch_score_step = ParallelRunStep(
    name=parallel_step_name,
    inputs=[input_images.as_named_input("input_images")],
    output=output_dir,
    arguments=["--labels_dir", label_config],
    side_inputs=[label_config],
    parallel_run_config=parallel_run_config,
)
```

När du har åtkomst till den i ditt härlednings skript (till exempel i metoden init ()) enligt följande:

```python
parser = argparse.ArgumentParser()
parser.add_argument('--labels_dir', dest="labels_dir", required=True)
args, _ = parser.parse_known_args()

labels_path = args.labels_dir
```

## <a name="next-steps"></a>Nästa steg

* Se SDK-referensen för hjälp med paketet [azureml-contrib-pipeline-Step](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps?view=azure-ml-py) och [dokumentationen](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallelrunstep?view=azure-ml-py) för ParallelRunStep-klassen.

* Följ den [avancerade självstudien](tutorial-pipeline-batch-scoring-classification.md) om hur du använder pipelines med ParallelRunStep och ett exempel på att skicka en annan fil som indata. 
