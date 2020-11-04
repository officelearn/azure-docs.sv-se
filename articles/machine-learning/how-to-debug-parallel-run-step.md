---
title: Felsöka ParallelRunStep
titleSuffix: Azure Machine Learning
description: Felsöka och Felsök ParallelRunStep i Machine Learning-pipeliner i Azure Machine Learning SDK för python. Lär dig vanliga fall GRO par för utveckling med pipelines och tips som hjälper dig att felsöka skript före och under fjärrkörning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: troubleshooting
ms.reviewer: jmartens, larryfr, vaidyas, laobri, tracych
ms.author: trmccorm
author: tmccrmck
ms.date: 09/23/2020
ms.openlocfilehash: 93997629b6b30f87769a0154e344215ca52c8ec4
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93308382"
---
# <a name="debug-and-troubleshoot-parallelrunstep"></a>Felsöka ParallelRunStep


I den här artikeln får du lära dig att felsöka och felsöka klassen [ParallelRunStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallel_run_step.parallelrunstep?preserve-view=true&view=azure-ml-py) från [Azure Machine Learning SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py).

## <a name="testing-scripts-locally"></a>Testa skript lokalt

Se [avsnittet testa skript lokalt](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-machine-learning-pipelines) för Machine Learning-pipeliner. Din ParallelRunStep körs som ett steg i ML-pipelines så att samma svar gäller båda.

## <a name="debugging-scripts-from-remote-context"></a>Felsöka skript från fjärr kontext

Över gången från att felsöka ett bedömnings skript lokalt för att felsöka ett bedömnings skript i en faktisk pipeline kan vara ett svårt kliv. Information om hur du hittar dina loggar i portalen finns i [avsnittet Machine Learning-pipeline för att felsöka skript från en fjärran sluten kontext](how-to-debug-pipelines.md). Informationen i avsnittet gäller även för en ParallelRunStep.

Logg filen innehåller till exempel `70_driver_log.txt` information från den kontrollant som startar ParallelRunStep-koden.

På grund av den distribuerade typen av ParallelRunStep-jobb finns det loggar från flera olika källor. Två konsoliderade filer skapas dock som tillhandahåller information på hög nivå:

- `~/logs/job_progress_overview.txt`: Den här filen innehåller en information på hög nivå om antalet mini-batchar (även kallade aktiviteter) som skapats hittills och antalet mini-batchar som bearbetats hittills. I det här slutet visas resultatet av jobbet. Om jobbet misslyckades visas fel meddelandet och var du ska starta fel sökningen.

- `~/logs/sys/master_role.txt`: Den här filen ger huvudnoden (kallas även Orchestrator) vyn för jobbet som körs. Innehåller skapande av aktiviteter, förlopps övervakning, körnings resultat.

Loggar som genereras från ett Entry-skript med hjälp av EntryScript-och Print-instruktioner finns i följande filer:

- `~/logs/user/entry_script_log/<ip_address>/<process_name>.log.txt`: De här filerna är loggarna som skrivs från entry_script med hjälp av EntryScript-hjälpen.

- `~/logs/user/stdout/<ip_address>/<process_name>.stdout.txt`: De här filerna är loggarna från STDOUT (t. ex. utskrifts uttryck) för entry_script.

- `~/logs/user/stderr/<ip_address>/<process_name>.stderr.txt`: De här filerna är loggarna från stderr av entry_script.

En kortfattad förståelse för fel i skriptet finns i:

- `~/logs/user/error.txt`: Den här filen kommer att försöka sammanfatta felen i skriptet.

För mer information om fel i skriptet, finns det:

- `~/logs/user/error/`: Innehåller fullständiga stack-spår för undantag som genereras vid inläsning och körning av Entry-skript.

När du behöver en fullständig förståelse för hur varje nod kör Poäng skriptet kan du titta på de enskilda process loggarna för varje nod. Process loggarna finns i `sys/node` mappen grupperade efter arbetsnoder:

- `~/logs/sys/node/<ip_address>/<process_name>.txt`: Den här filen innehåller detaljerad information om varje mini-batch när den hämtas eller slutförs av en anställd. För varje mini-batch innehåller den här filen:

    - IP-adressen och PID för arbets processen. 
    - Det totala antalet objekt, antalet behandlade objekt och antalet misslyckade objekt.
    - Tid för start tid, varaktighet, bearbetnings tid och körnings metod.

Du kan också hitta information om resursanvändningen för processerna för varje arbets tagare. Den här informationen är i CSV-format och finns på `~/logs/sys/perf/<ip_address>/node_resource_usage.csv` . Information om varje process finns tillgänglig under `~logs/sys/perf/<ip_address>/processes_resource_usage.csv` .

### <a name="how-do-i-log-from-my-user-script-from-a-remote-context"></a>Hur gör jag för att logg från mitt användar skript från en fjärran sluten kontext?
ParallelRunStep kan köra flera processer på en nod baserat på process_count_per_node. För att kunna organisera loggar från varje process på noden och kombinera print-och log-uttryck, rekommenderar vi att du använder ParallelRunStep-loggning som visas nedan. Du får en loggare från EntryScript och gör loggarna visas i **loggarna/mappen användare** i portalen.

**Ett exempel på ett post skript med hjälp av loggarna:**
```python
from azureml_user.parallel_run import EntryScript

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

Användaren kan skicka referens data till skript med hjälp av side_inputs parametern ParalleRunStep. Alla data uppsättningar som tillhandahålls som side_inputs kommer att monteras på varje arbetsnod. Användaren kan hämta platsen för montering genom att skicka argumentet.

Skapa en [data uppsättning](/python/api/azureml-core/azureml.core.dataset.dataset?preserve-view=true&view=azure-ml-py) som innehåller referens data och registrera den med din arbets yta. Skicka den till- `side_inputs` parametern för din `ParallelRunStep` . Dessutom kan du lägga till dess sökväg i `arguments` avsnittet för att enkelt få åtkomst till dess monterade sökväg:

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

### <a name="how-to-use-input-datasets-with-service-principal-authentication"></a>Hur använder jag indata-datauppsättningar med autentisering av tjänstens huvud namn?

Användaren kan skicka indata-datauppsättningar med autentisering av tjänstens huvud namn som används i arbets ytan. Om du använder sådan data uppsättning i ParallelRunStep måste data uppsättningen registreras för att kunna konstruera ParallelRunStep-konfigurationen.

```python
service_principal = ServicePrincipalAuthentication(
    tenant_id="**_",
    service_principal_id="_*_",
    service_principal_password="_*_")
 
ws = Workspace(
    subscription_id="_*_",
    resource_group="_*_",
    workspace_name="_*_",
    auth=service_principal
    )
 
default_blob_store = ws.get_default_datastore() # or Datastore(ws, '_*_datastore-name_*_') 
ds = Dataset.File.from_files(default_blob_store, '_*path**_')
registered_ds = ds.register(ws, '_*_dataset-name_*_', create_new_version=True)
```

## <a name="next-steps"></a>Nästa steg

_ Se dessa [Jupyter-anteckningsböcker som demonstrerar Azure Machine Learning pipelines](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines)

* Se SDK-referensen för hjälp med paketet [azureml-pipeline-steg](/python/api/azureml-pipeline-steps/azureml.pipeline.steps?preserve-view=true&view=azure-ml-py) . Visa referens [dokumentation](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallelrunstep?preserve-view=true&view=azure-ml-py) för ParallelRunStep-klassen.

* Följ den [avancerade självstudien](tutorial-pipeline-batch-scoring-classification.md) om hur du använder pipelines med ParallelRunStep. I självstudien visas hur du skickar en annan fil som indata.