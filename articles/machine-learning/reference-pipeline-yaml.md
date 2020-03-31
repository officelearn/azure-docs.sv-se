---
title: Machine Learning pipeline YAML
titleSuffix: Azure Machine Learning
description: Lär dig hur du definierar en pipeline för maskininlärning med hjälp av en YAML-fil. YAML-pipelinedefinitioner används med maskininlärningstillägget för Azure CLI.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: sanpil
author: sanpil
ms.date: 11/11/2019
ms.openlocfilehash: a677aaa891e21f4c9eeda02eebcb94e9d79a55ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79368833"
---
# <a name="define-machine-learning-pipelines-in-yaml"></a>Definiera pipelines för maskininlärning i YAML

Lär dig hur du definierar dina pipelines för maskininlärning i [YAML](https://yaml.org/). När du använder maskininlärningstillägget för Azure CLI förväntar sig många av de pipeline-relaterade kommandona en YAML-fil som definierar pipelinen.

I följande tabell visas vad som stöds och stöds inte när en pipeline i YAML definieras:

| Stegtyp | Stöds? |
| ----- | :-----: |
| PythonScriptStep (Svenska) | Ja |
| AdlaStep (300) | Ja |
| AzureBatchStep | Ja |
| DatabricksStep (databricksStep) | Ja |
| DataTransferStep | Ja |
| AutoMLStep | Inga |
| HyperDriveStep | Inga |
| ModuleStep (ModulSteg) | Ja |
| MPIStep (30) | Inga |
| EstimatorStep | Inga |

## <a name="pipeline-definition"></a>Definition av pipeline

En pipelinedefinition använder följande nycklar, som motsvarar klassen [Pipelines:](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline.pipeline?view=azure-ml-py)

| YAML-nyckel | Beskrivning |
| ----- | ----- |
| `name` | Beskrivningen av rörledningen. |
| `parameters` | Parametrar till pipelinen. |
| `data_reference` | Definierar hur och var data ska göras tillgängliga i en körning. |
| `default_compute` | Standardberäkningsmål där alla steg i pipelinen körs. |
| `steps` | De steg som används i pipelinen. |

## <a name="parameters"></a>Parametrar

I `parameters` avsnittet används följande nycklar, som motsvarar klassen [PipelineParameter:](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelineparameter?view=azure-ml-py)

| YAML-nyckel | Beskrivning |
| ---- | ---- |
| `type` | Parameterns värdetyp. Giltiga typer `string` `int`är `float` `bool`, `datapath`, , eller . |
| `default` | Standardvärdet. |

Varje parameter namnges. Följande YAML-kodavsnitt definierar till exempel `NumIterationsParameter`tre `DataPathParameter`parametrar `NodeCountParameter`med namnet , och:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        NumIterationsParameter:
            type: int
            default: 40
        DataPathParameter:
            type: datapath
            default:
                datastore: workspaceblobstore
                path_on_datastore: sample2.txt
        NodeCountParameter:
            type: int
            default: 4
```

## <a name="data-reference"></a>Datareferens

I `data_references` avsnittet används följande nycklar, som motsvarar [DataReference:](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py)

| YAML-nyckel | Beskrivning |
| ----- | ----- |
| `datastore` | Datalagret att referera till. |
| `path_on_datastore` | Den relativa sökvägen i stödlagringen för datareferensen. |

Varje datareferens finns i en nyckel. Följande YAML-kodavsnitt definierar till exempel en datareferens `employee_data`som lagras i den namngivna nyckeln:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        employee_data:
            datastore: adftestadla
            path_on_datastore: "adla_sample/sample_input.csv"
```

## <a name="steps"></a>Steg

Steg definierar en beräkningsmiljö, tillsammans med de filer som ska köras på miljön. Om du vill definiera typen `type` av ett steg använder du nyckeln:

| Stegtyp | Beskrivning |
| ----- | ----- |
| `AdlaStep` | Kör ett U-SQL-skript med Azure Data Lake Analytics. Motsvarar klassen [AdlaStep.](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.adlastep?view=azure-ml-py) |
| `AzureBatchStep` | Kör jobb med Azure Batch. Motsvarar klassen [AzureBatchStep.](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.azurebatchstep?view=azure-ml-py) |
| `DatabricsStep` | Lägger till en Databricks-anteckningsbok, Python-skript eller JAR. Motsvarar klassen [DatabricksStep.](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricksstep?view=azure-ml-py) |
| `DataTransferStep` | Överför data mellan lagringsalternativ. Motsvarar klassen [DataTransferStep.](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py) |
| `PythonScriptStep` | Kör ett Python-skript. Motsvarar klassen [PythonScriptStep.](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py) |

### <a name="adla-step"></a>ADLA-steg

| YAML-nyckel | Beskrivning |
| ----- | ----- |
| `script_name` | Namnet på U-SQL-skriptet (i förhållande till `source_directory`). |
| `compute_target` | Beräkningsmålet för Azure Data Lake som ska användas för det här steget. |
| `parameters` | [Parametrar](#parameters) till pipelinen. |
| `inputs` | Indata kan vara [InputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py), [DataReference](#data-reference), [PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), [Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py), [DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)eller [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py). |
| `outputs` | Utdata kan vara antingen [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) eller [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py). |
| `source_directory` | Katalog som innehåller skript, sammansättningar, etc. |
| `priority` | Prioritetsvärdet som ska användas för det aktuella jobbet. |
| `params` | Ordlista med namn-värde-par. |
| `degree_of_parallelism` | Graden av parallellism att använda för detta jobb. |
| `runtime_version` | Körningsversionen av Data Lake Analytics-motorn. |
| `allow_reuse` | Avgör om steget ska återanvända tidigare resultat när det körs igen med samma inställningar. |

Följande exempel innehåller en ADLA-stegdefinition:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        employee_data:
            datastore: adftestadla
            path_on_datastore: "adla_sample/sample_input.csv"
    default_compute: adlacomp
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "AdlaStep"
            name: "MyAdlaStep"
            script_name: "sample_script.usql"
            source_directory: "D:\\scripts\\Adla"
            inputs:
                employee_data:
                    source: employee_data
            outputs:
                OutputData:
                    destination: Output4
                    datastore: adftestadla
                    bind_mode: mount
```

### <a name="azure-batch-step"></a>Azure Batch-steg

| YAML-nyckel | Beskrivning |
| ----- | ----- |
| `compute_target` | Azure Batch-beräkningsmålet som ska användas för det här steget. |
| `inputs` | Indata kan vara [InputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py), [DataReference](#data-reference), [PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), [Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py), [DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)eller [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py). |
| `outputs` | Utdata kan vara antingen [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) eller [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py). |
| `source_directory` | Katalog som innehåller modulens binärfiler, körbara, sammansättningar etc. |
| `executable` | Namn på kommandot/körbara som ska köras som en del av det här jobbet. |
| `create_pool` | Boolesk flagga för att ange om poolen ska skapas innan du kör jobbet. |
| `delete_batch_job_after_finish` | Boolesk flagga för att ange om jobbet ska tas bort från batchkontot när det är klart. |
| `delete_batch_pool_after_finish` | Boolesk flagga för att ange om poolen ska tas bort när jobbet är klart. |
| `is_positive_exit_code_failure` | Boolesk flagga för att ange om jobbet misslyckas om aktiviteten avslutas med en positiv kod. |
| `vm_image_urn` | Om `create_pool` `True`är , `VirtualMachineConfiguration`och VM använder . |
| `pool_id` | ID:t för poolen där jobbet ska köras. |
| `allow_reuse` | Avgör om steget ska återanvända tidigare resultat när det körs igen med samma inställningar. |

Följande exempel innehåller en Stegdefinition för Azure Batch:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        input:
            datastore: workspaceblobstore
            path_on_datastore: "input.txt"
    default_compute: testbatch
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "AzureBatchStep"
            name: "MyAzureBatchStep"
            pool_id: "MyPoolName"
            create_pool: true
            executable: "azurebatch.cmd"
            source_directory: "D:\\scripts\\AureBatch"
            allow_reuse: false
            inputs:
                input:
                    source: input
            outputs:
                output:
                    destination: output
                    datastore: workspaceblobstore
```

### <a name="databricks-step"></a>Databricks steg

| YAML-nyckel | Beskrivning |
| ----- | ----- |
| `compute_target` | Azure Databricks-beräkningsmålet som ska användas för det här steget. |
| `inputs` | Indata kan vara [InputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py), [DataReference](#data-reference), [PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), [Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py), [DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)eller [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py). |
| `outputs` | Utdata kan vara antingen [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) eller [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py). |
| `run_name` | Namnet i Databricks för den här körningen. |
| `source_directory` | Katalog som innehåller skriptet och andra filer. |
| `num_workers` | Det statiska antalet arbetare för Databricks kör klustret. |
| `runconfig` | Sökvägen till `.runconfig` en fil. Den här filen är en YAML-representation av klassen [RunConfiguration.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py) Mer information om filens struktur finns i [runconfigschema.json](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json). |
| `allow_reuse` | Avgör om steget ska återanvända tidigare resultat när det körs igen med samma inställningar. |

Följande exempel innehåller ett Databricks-steg:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        adls_test_data:
            datastore: adftestadla
            path_on_datastore: "testdata"
        blob_test_data:
            datastore: workspaceblobstore
            path_on_datastore: "dbtest"
    default_compute: mydatabricks
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "DatabricksStep"
            name: "MyDatabrickStep"
            run_name: "DatabricksRun"
            python_script_name: "train-db-local.py"
            source_directory: "D:\\scripts\\Databricks"
            num_workers: 1
            allow_reuse: true
            inputs:
                blob_test_data:
                    source: blob_test_data
            outputs:
                OutputData:
                    destination: Output4
                    datastore: workspaceblobstore
                    bind_mode: mount
```

### <a name="data-transfer-step"></a>Steg för dataöverföring

| YAML-nyckel | Beskrivning |
| ----- | ----- |
| `compute_target` | Beräkningsmålet för Azure Data Factory som ska användas för det här steget. |
| `source_data_reference` | Indataanslutning som fungerar som källa för dataöverföringsåtgärder. Värden som stöds är [InputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py), [DataReference](#data-reference), [PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), [Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py), [DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)eller [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py). |
| `destination_data_reference` | Indataanslutning som fungerar som mål för dataöverföringsåtgärder. Värden som stöds är [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) och [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py). |
| `allow_reuse` | Avgör om steget ska återanvända tidigare resultat när det körs igen med samma inställningar. |

Följande exempel innehåller ett dataöverföringssteg:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        adls_test_data:
            datastore: adftestadla
            path_on_datastore: "testdata"
        blob_test_data:
            datastore: workspaceblobstore
            path_on_datastore: "testdata"
    default_compute: adftest
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "DataTransferStep"
            name: "MyDataTransferStep"
            adla_compute_name: adftest
            source_data_reference:
                adls_test_data:
                    source: adls_test_data
            destination_data_reference:
                blob_test_data:
                    source: blob_test_data
```

### <a name="python-script-step"></a>Python-skriptsteg

| YAML-nyckel | Beskrivning |
| ----- | ----- |
| `compute_target` | Beräkningsmålet som ska användas för det här steget. Beräkningsmålet kan vara en Azure Machine Learning Compute, Virtual Machine (till exempel data science-datorn) eller HDInsight. |
| `inputs` | Indata kan vara [InputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py), [DataReference](#data-reference), [PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), [Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py), [DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)eller [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py). |
| `outputs` | Utdata kan vara antingen [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) eller [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py). |
| `script_name` | Namnet på Python-skriptet `source_directory`(i förhållande till ). |
| `source_directory` | Katalog som innehåller skriptet, Conda-miljön, etc. |
| `runconfig` | Sökvägen till `.runconfig` en fil. Den här filen är en YAML-representation av klassen [RunConfiguration.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py) Mer information om filens struktur finns i [runconfig.json](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json). |
| `allow_reuse` | Avgör om steget ska återanvända tidigare resultat när det körs igen med samma inställningar. |

Följande exempel innehåller ett Python-skriptsteg:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        DataReference1:
            datastore: workspaceblobstore
            path_on_datastore: testfolder/sample.txt
    default_compute: cpu-cluster
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "PythonScriptStep"
            name: "MyPythonScriptStep"
            script_name: "train.py"
            allow_reuse: True
            source_directory: "D:\\scripts\\PythonScript"
            inputs:
                InputData:
                    source: DataReference1
            outputs:
                OutputData:
                    destination: Output4
                    datastore: workspaceblobstore
                    bind_mode: mount
```

## <a name="schedules"></a>Scheman

När du definierar schemat för en pipeline kan det antingen vara datalagerutlöst eller återkommande baserat på ett tidsintervall. Följande är de nycklar som används för att definiera ett schema:

| YAML-nyckel | Beskrivning |
| ----- | ----- |
| `description` | En beskrivning av schemat. |
| `recurrence` | Innehåller återkommande inställningar, om schemat är återkommande. |
| `pipeline_parameters` | Alla parametrar som krävs av pipelinen. |
| `wait_for_provisioning` | Om du vill vänta på att systemet ska slutföras. |
| `wait_timeout` | Antalet sekunder att vänta innan du går ut. |
| `datastore_name` | Datalagret som ska övervakas för ändrade/tillagda blobbar. |
| `polling_interval` | Hur länge, i minuter, mellan avsökning för ändrade/tillagda blobbar. Standardvärde: 5 minuter. Stöds endast för datalagerscheman. |
| `data_path_parameter_name` | Namnet på den datasökvägspipelineparameter som ska anges med den ändrade blob-sökvägen. Stöds endast för datalagerscheman. |
| `continue_on_step_failure` | Om du vill fortsätta körningen av andra steg i den skickade PipelineRun om ett steg misslyckas. Om det anges `continue_on_step_failure` åsidosätter inställningen av pipelinen.
| `path_on_datastore` | Valfri. Sökvägen i datalagret som ska övervakas för ändrade/tillagda blobbar. Sökvägen finns under behållaren för datalagret, så den faktiska`path_on_datastore`sökvägen som schemaövervakaren är container/ . Om ingen är det så att datalagerbehållaren övervakas. Tillägg/ändringar som görs i en undermapp till `path_on_datastore` övervakas inte. Stöds endast för datalagerscheman. |

Följande exempel innehåller definitionen för ett datalagerutlöst schema:

```yaml
Schedule: 
      description: "Test create with datastore" 
      recurrence: ~ 
      pipeline_parameters: {} 
      wait_for_provisioning: True 
      wait_timeout: 3600 
      datastore_name: "workspaceblobstore" 
      polling_interval: 5 
      data_path_parameter_name: "input_data" 
      continue_on_step_failure: None 
      path_on_datastore: "file/path" 
```

När du definierar ett **återkommande**schema `recurrence`använder du följande tangenter under:

| YAML-nyckel | Beskrivning |
| ----- | ----- |
| `frequency` | Hur ofta schemat återkommer. Giltiga värden `"Minute"` `"Hour"`är `"Day"` `"Week"`, `"Month"`, , eller . |
| `interval` | Hur ofta schemat bränder. Heltalsvärdet är antalet tidsenheter som ska vänta tills schemat utlöses igen. |
| `start_time` | Starttiden för schemat. Strängformatet för värdet `YYYY-MM-DDThh:mm:ss`är . Om ingen starttid anges körs den första arbetsbelastningen direkt och framtida arbetsbelastningar körs baserat på schemat. Om starttiden är tidigare körs den första arbetsbelastningen vid nästa beräknade körningstid. |
| `time_zone` | Tidszonen för starttiden. Om ingen tidszon anges används UTC. |
| `hours` | Om `frequency` `"Day"` är `"Week"`eller kan du ange ett eller flera heltal från 0 till 23, avgränsade med kommatecken, som de timmar på dagen då pipelinen ska köras. Endast `time_of_day` `hours` eller `minutes` och kan användas. |
| `minutes` | Om `frequency` `"Day"` är `"Week"`eller kan du ange ett eller flera heltal från 0 till 59, avgränsade med kommatecken, som minuterna i timmen när pipelinen ska köras. Endast `time_of_day` `hours` eller `minutes` och kan användas. |
| `time_of_day` | Om `frequency` `"Day"` är `"Week"`eller kan du ange en tid på dagen för schemat som ska köras. Strängformatet för värdet `hh:mm`är . Endast `time_of_day` `hours` eller `minutes` och kan användas. |
| `week_days` | Om `frequency` `"Week"`är , kan du ange en eller flera dagar, avgränsade med kommatecken, när schemat ska köras. Giltiga värden `"Monday"` `"Tuesday"`är `"Wednesday"` `"Thursday"`, `"Friday"` `"Saturday"`, `"Sunday"`, , , och . |

Följande exempel innehåller definitionen för ett återkommande schema:

```yaml
Schedule: 
    description: "Test create with recurrence" 
    recurrence: 
        frequency: Week # Can be "Minute", "Hour", "Day", "Week", or "Month". 
        interval: 1 # how often fires 
        start_time: 2019-06-07T10:50:00 
        time_zone: UTC 
        hours: 
        - 1 
        minutes: 
        - 0 
        time_of_day: null 
        week_days: 
        - Friday 
    pipeline_parameters: 
        'a': 1 
    wait_for_provisioning: True 
    wait_timeout: 3600 
    datastore_name: ~ 
    polling_interval: ~ 
    data_path_parameter_name: ~ 
    continue_on_step_failure: None 
    path_on_datastore: ~ 
```

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [använder CLI-tillägget för Azure Machine Learning](reference-azure-machine-learning-cli.md).
