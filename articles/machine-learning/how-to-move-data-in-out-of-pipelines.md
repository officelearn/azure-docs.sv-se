---
title: In- och utdata från ML-rörledningar
titleSuffix: Azure Machine Learning
description: Förbereda, använda och generera data i Azure Machine Learning-pipelines
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 04/01/2020
ms.openlocfilehash: 3dd1a82bf7fad1f201f5c0f52af944ef44a3fdf9
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879770"
---
# <a name="moving-data-into-and-between-ml-pipeline-steps-python"></a>Flytta data till och mellan ML-pipelinesteg (Python)

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Data är centrala för machine learning-pipelines. Den här artikeln innehåller kod för import, omvandling och flyttning av data mellan steg i en Azure Machine Learning-pipeline. En översikt över hur data fungerar i Azure Machine Learning finns [i Access-data i Azure storage services](how-to-access-data.md). Fördelar och struktur med Azure Machine Learning-pipelines finns i [Vad är Azure Machine Learning-pipelines?](concept-ml-pipelines.md).

Den här artikeln visar hur du:

- Använda `Dataset` objekt för befintliga data
- Komma åt data i dina steg
- Dela `Dataset` upp data i delmängder, till exempel utbildnings- och valideringsundergrupper
- Skapa `PipelineData` objekt för att överföra data till nästa pipeline-steg
- Använda `PipelineData` objekt som indata till pipeline-steg
- Skapa `Dataset` nya `PipelineData` objekt från att du vill behålla

## <a name="prerequisites"></a>Krav

Du behöver:

- En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnadsfria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree).

- [Azure Machine Learning SDK för Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)eller åtkomst till Azure Machine Learning [Studio](https://ml.azure.com/).

- En Azure Machine Learning-arbetsyta.
  
  Skapa [en Azure Machine Learning-arbetsyta](how-to-manage-workspace.md) eller använd en befintlig via Python SDK. Importera `Workspace` klassen `Datastore` och läs in prenumerationsinformationen `config.json` från `from_config()`filen med hjälp av funktionen . Den här funktionen söker efter JSON-filen i den aktuella katalogen som standard, `from_config(path="your/file/path")`men du kan också ange en sökvägsparameter som ska peka på filen med .

   ```python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```

- Vissa befintliga data. Den här artikeln visar kort användningen av en [Azure blob-behållare](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview).

- Valfritt: En befintlig pipeline för maskininlärning, till exempel den som beskrivs i [Skapa och köra machine learning-pipelines med Azure Machine Learning SDK](how-to-create-your-first-pipeline.md).

## <a name="use-dataset-objects-for-pre-existing-data"></a>Använda `Dataset` objekt för befintliga data 

Det bästa sättet att få in data i en pipeline är att använda ett [Dataset-objekt.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py) `Dataset`objekt representerar beständiga data som är tillgängliga på en arbetsyta.

Det finns många sätt `Dataset` att skapa och registrera objekt. Tabelldatauppsättningar är för avgränsade data som är tillgängliga i en eller flera filer. Fildatauppsättningar är för binära data (till exempel bilder) eller för data som du ska tolka. De enklaste programmatiska `Dataset` sätten att skapa objekt är att använda befintliga blobbar i arbetsytans lagring eller offentliga webbadresser:

```python
datastore = Datastore.get(workspace, 'training_data')
iris_dataset = Dataset.Tabular.from_delimited_files(DataPath(datastore, 'iris.csv'))

cats_dogs_dataset = Dataset.File.from_files(
    paths='https://download.microsoft.com/download/3/E/1/3E1C3F21-ECDB-4869-8368-6DEBA77B919F/kagglecatsanddogs_3367a.zip',
    archive_options=ArchiveOptions(archive_type=ArchiveType.ZIP, entry_glob='**/*.jpg')
)
```

Fler alternativ för att skapa datauppsättningar med olika alternativ och från olika källor, registrera dem och granska dem i Azure Machine Learning-användargränssnittet, förstå hur datastorlek interagerar med beräkningskapacitet och versionshantering av dem finns i [Skapa Azure Machine Learning-datauppsättningar](how-to-create-register-datasets.md). 

### <a name="pass-datasets-to-your-script"></a>Skicka datauppsättningar till skriptet

Om du vill skicka datauppsättningens sökväg `Dataset` till `as_named_input()` skriptet använder du objektets metod. Du kan antingen `DatasetConsumptionConfig` skicka det resulterande objektet till skriptet som ett argument eller, genom att använda `inputs` argumentet till pipeline-skriptet, du kan hämta datauppsättningen med `Run.get_context().input_datasets[]`.

När du har skapat en namngiven indata `as_mount()` kan `as_download()`du välja dess åtkomstläge: eller . Om skriptet bearbetar alla filer i datauppsättningen och disken på beräkningsresursen är tillräckligt stor för datauppsättningen är hämtningsläget det bättre valet. Hämtningsläget undviker omkostnaderna för att strömma data vid körning. Om skriptet kommer åt en delmängd av datauppsättningen eller om det är för stort för beräkningen använder du monteringsåtkomstläget. Mer information finns i [Mount vs. Download](https://docs.microsoft.com/azure/machine-learning/how-to-train-with-datasets#mount-vs-download)

Så här skickar du en datauppsättning till pipeline-steget:

1. Använda `TabularDataset.as_named_inputs()` `FileDataset.as_named_input()` eller (inga 's' i `DatasetConsumptionConfig` slutet) för att skapa ett objekt
1. Använda `as_mount()` `as_download()` eller ställa in åtkomstläget
1. Skicka datauppsättningarna till pipeline-stegen `arguments` med `inputs` hjälp av antingen argumentet eller argumentet

Följande kodavsnitt visar det gemensamma mönstret för `PythonScriptStep` att kombinera dessa steg i konstruktorn: 

```python

train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    inputs=[iris_dataset.as_named_inputs('iris').as_mount()]
)
```

Du kan också använda `random_split()` `take_sample()` metoder som och för att skapa flera indata eller minska mängden data som skickas till pipeline-steget:

```python
seed = 42 # PRNG seed
smaller_dataset = iris_dataset.take_sample(0.1, seed=seed) # 10%
train, test = smaller_dataset.random_split(percentage=0.8, seed=seed)

train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    inputs=[train.as_named_inputs('train').as_download(), test.as_named_inputs('test').as_download()]
)
```

### <a name="access-datasets-within-your-script"></a>Komma åt datauppsättningar i skriptet

Namngivna indata till pipelinestegsskriptet är `Run` tillgängliga som en ordlista i objektet. Hämta det `Run` aktiva `Run.get_context()` objektet med och hämta sedan `input_datasets`ordlistan med namngivna indata med . Om du `DatasetConsumptionConfig` skickade objektet `arguments` med argumentet `inputs` i stället för `ArgParser` argumentet kommer du åt data med hjälp av kod. Båda teknikerna visas i följande utdrag.

```python
# In pipeline definition script:
# Code for demonstration only: It would be very confusing to split datasets between `arguments` and `inputs`
train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    arguments=['--training-folder', train.as_named_inputs('train').as_download()]
    inputs=[test.as_named_inputs('test').as_download()]
)

# In pipeline script
parser = argparse.ArgumentParser()
parser.add_argument('--training-folder', type=str, dest='train_folder', help='training data folder mounting point')
args = parser.parse_args()
training_data_folder = args.train_folder

testing_data_folder = Run.get_context().input_datasets['test']
```

Det skickade värdet blir sökvägen till datauppsättningsfilerna.

Det är också möjligt att `Dataset` komma åt en registrerad direkt. Eftersom registrerade datauppsättningar är beständiga och delas över en arbetsyta kan du hämta dem direkt:

```python
run = Run.get_context()
ws = run.experiment.workspace
ds = Dataset.get_by_name(workspace=ws, name='mnist_opendataset')
```

## <a name="use-pipelinedata-for-intermediate-data"></a>Användning `PipelineData` för mellanliggande data

Objekt `Dataset` representerar beständiga data, men [PipelineData-objekt](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) används för tillfälliga data som matas ut från pipeline-steg. Eftersom ett `PipelineData` objekts livslängd är längre än ett enda pipeline-steg definierar du dem i pipelinedefinitionsskriptet. När du `PipelineData` skapar ett objekt måste du ange ett namn och ett datalager där data ska finnas. Skicka `PipelineData` dina objekt till `PythonScriptStep` dig med `arguments` _både_ och `outputs` argument:

```python
default_datastore = workspace.get_default_datastore()
dataprep_output = PipelineData("clean_data", datastore=default_datastore)

dataprep_step = PythonScriptStep(
    name="prep_data",
    script_name="dataprep.py",
    compute_target=cluster,
    arguments=["--output-path", dataprep_output]
    inputs=[Dataset.get_by_name(workspace, 'raw_data')],
    outputs=[dataprep_output]
)
```

Du kan välja `PipelineData` att skapa ditt objekt med hjälp av ett åtkomstläge som ger en omedelbar överföring. I så fall, när `PipelineData`du `upload_mode` skapar `"upload"` din `output_path_on_compute` , ange till och använda argumentet för att ange sökvägen som du ska skriva data:

```python
PipelineData("clean_data", datastore=def_blob_store, output_mode="upload", output_path_on_compute="clean_data_output/")
```

### <a name="use-pipelinedata-as-outputs-of-a-training-step"></a>Använd `PipelineData` som utdata för ett utbildningssteg

Inom pipelinen `PythonScriptStep`kan du hämta tillgängliga utdatasökvägar med hjälp av programmets argument. Om det här steget är det första steget och initierar utdata måste du skapa katalogen vid den angivna sökvägen. Du kan sedan skriva vilka filer du `PipelineData`vill ska finnas i .

```python
parser = argparse.ArgumentParser()
parser.add_argument('--output_path', dest='output_path', required=True)
args = parser.parse_args()

# Make directory for file
os.makedirs(os.path.dirname(args.output_path), exist_ok=True)
with open(args.output_path, 'w') as f:
    f.write("Step 1's output")
```

Om du `PipelineData` skapade `is_directory` din med `True`argumentet inställd på, skulle `os.makedirs()` det vara tillräckligt att bara utföra samtalet och då skulle du vara fri att skriva vilka filer du ville till sökvägen. Mer information finns i referensdokumentationen [för PipelineData.](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)

### <a name="read-pipelinedata-as-inputs-to-non-initial-steps"></a>Läsa `PipelineData` som indata till icke-inledande steg

När det första pipeline-steget skriver `PipelineData` vissa data till sökvägen och det blir en utdata för det första steget kan den användas som indata till ett senare steg:

```python
step1_output_data = PipelineData("processed_data", datastore=def_blob_store, output_mode="upload")

step1 = PythonScriptStep(
    name="generate_data",
    script_name="step1.py",
    runconfig = aml_run_config,
    arguments = ["--output_path", step1_output_data],
    inputs=[],
    outputs=[step1_output_data]
)

step2 = PythonScriptStep(
    name="read_pipeline_data",
    script_name="step2.py",
    compute_target=compute,
    runconfig = aml_run_config,
    arguments = ["--pd", step1_output_data],
    inputs=[step1_output_data]
)

pipeline = Pipeline(workspace=ws, steps=[step1, step2])
```

Värdet för `PipelineData` en indata är sökvägen till föregående utdata. Om det första steget, som tidigare visats tidigare, skrev en enda fil kan det se ut som att använda den: 

```python
parser = argparse.ArgumentParser()
parser.add_argument('--pd', dest='pd', required=True)
args = parser.parse_args()

with open(args.pd) as f:
    print(f.read())
```

## <a name="convert-pipelinedata-objects-to-datasets"></a>Konvertera `PipelineData` objekt `Dataset`till s

Om du vill göra `PipelineData` din tillgänglig längre än en körningslängd använder du `as_dataset()` `Dataset`dess funktion för att konvertera den till en . Du kan sedan `Dataset`registrera , vilket gör det till en förstklassig medborgare i din arbetsyta. Eftersom `PipelineData` objektet har en annan sökväg varje gång pipelinen körs `create_new_version` rekommenderar `True` vi starkt `Dataset` att `PipelineData` du ställer in på när du registrerar en skapad från ett objekt.

```python
step1_output_ds = step1_output_data.as_dataset()
step1_output_ds.register(name="processed_data", create_new_version=True)
```

## <a name="next-steps"></a>Nästa steg

* [Skapa en Azure machine learning-datauppsättning](how-to-create-register-datasets.md)
* [Skapa och kör pipelines för maskininlärning med Azure Machine Learning SDK](how-to-create-your-first-pipeline.md)
