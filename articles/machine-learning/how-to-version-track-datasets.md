---
title: Versionshantering av datauppsättning
titleSuffix: Azure Machine Learning
description: Lär dig hur du bäst kan version av dina datauppsättningar och hur versionshantering fungerar med maskininlärningspipelor.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 03/09/2020
ms.custom: ''
ms.openlocfilehash: 5bd4436fc63fb570f052606ab557dbcf243cf5e7
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80476868"
---
# <a name="version-and-track-datasets-in-experiments"></a>Versions- och spåra datauppsättningar i experiment
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här artikeln får du lära dig hur du skapar och spårar Azure Machine Learning-datauppsättningar för reproducerbarhet. Datauppsättningsversion är ett sätt att bokmärka tillståndet för dina data så att du kan använda en viss version av datauppsättningen för framtida experiment.

Typiska versionsscenarier:

* När nya data finns tillgängliga för omskolning
* När du använder olika dataförberedelser eller funktionstekniker

## <a name="prerequisites"></a>Krav

Du behöver följande för den här självstudien:

- [Azure Machine Learning SDK för Python installerat](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py). Den här SDK:er innehåller [azureml-dataset-paketet.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset?view=azure-ml-py)
    
- En [Azure Machine Learning-arbetsyta](concept-workspace.md). Hämta en befintlig genom att köra följande kod eller [skapa en ny arbetsyta](how-to-manage-workspace.md).

    ```Python
    import azureml.core
    from azureml.core import Workspace
    
    ws = Workspace.from_config()
    ```
- En [Azure Machine Learning-datauppsättning](how-to-create-register-datasets.md).

<a name="register"></a>

## <a name="register-and-retrieve-dataset-versions"></a>Registrera och hämta datauppsättningsversioner

Genom att registrera en datauppsättning kan du göra en version, återanvändning och dela den över experiment och med kollegor. Du kan registrera flera datauppsättningar under samma namn och hämta en viss version efter namn och versionsnummer.

### <a name="register-a-dataset-version"></a>Registrera en datauppsättningsversion

Följande kod registrerar en ny `titanic_ds` version av datauppsättningen genom att ange parametern `create_new_version` till `True`. Om det inte `titanic_ds` finns någon befintlig datauppsättning registrerad med arbetsytan skapar koden en `titanic_ds` ny datauppsättning med namnet och anger dess version till 1.

```Python
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'titanic training data',
                                 create_new_version = True)
```

### <a name="retrieve-a-dataset-by-name"></a>Hämta en datauppsättning efter namn

Som standard returnerar [metoden get_by_name()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--) `Dataset` i klassen den senaste versionen av datauppsättningen som registrerats med arbetsytan. 

Följande kod får version 1 av datauppsättningen. `titanic_ds`

```Python
from azureml.core import Dataset
# Get a dataset by name and version number
titanic_ds = Dataset.get_by_name(workspace = workspace,
                                 name = 'titanic_ds', 
                                 version = 1)
```

<a name="best-practice"></a>

## <a name="versioning-best-practice"></a>Bästa praxis för versionshantering

När du skapar en datauppsättningsversion skapar du *inte* en extra kopia av data med arbetsytan. Eftersom datauppsättningar är referenser till data i lagringstjänsten har du en enda sanningskälla som hanteras av din lagringstjänst.

>[!IMPORTANT]
> Om de data som refereras av datauppsättningen skrivs över eller tas bort, återställs *inte* ändringen om ändringen om du anropar en viss version av datauppsättningen.

När du läser in data från en datauppsättning läses alltid det aktuella datainnehållet som refereras av datauppsättningen in. Om du vill vara säker på att varje datauppsättningsversion kan reproduceras rekommenderar vi att du inte ändrar datainnehåll som refereras av datauppsättningsversionen. När nya data kommer in sparar du nya datafiler i en separat datamapp och skapar sedan en ny datauppsättningsversion för att inkludera data från den nya mappen.

Följande bild- och exempelkod visar det rekommenderade sättet att strukturera datamapparna och skapa datauppsättningsversioner som refererar till dessa mappar:

![Mappstrukturen](./media/how-to-version-track-datasets/folder-image.png)

```Python
from azureml.core import Dataset

# get the default datastore of the workspace
datastore = workspace.get_default_datastore()

# create & register weather_ds version 1 pointing to all files in the folder of week 27
datastore_path1 = [(datastore, 'Weather/week 27')]
dataset1 = Dataset.File.from_files(path=datastore_path1)
dataset1.register(workspace = workspace,
                  name = 'weather_ds',
                  description = 'weather data in week 27',
                  create_new_version = True)

# create & register weather_ds version 2 pointing to all files in the folder of week 27 and 28
datastore_path2 = [(datastore, 'Weather/week 27'), (datastore, 'Weather/week 28')]
dataset2 = Dataset.File.from_files(path = datastore_path2)
dataset2.register(workspace = workspace,
                  name = 'weather_ds',
                  description = 'weather data in week 27, 28',
                  create_new_version = True)

```

<a name="pipeline"></a>

## <a name="version-a-pipeline-output-dataset"></a>Version av en pipeline-utdatauppsättning

Du kan använda en datauppsättning som indata och utdata för varje Machine Learning-pipelinesteg. När du kör pipelines igen registreras utdata för varje pipeline-steg som en ny datauppsättningsversion.

Eftersom Machine Learning-pipelines fyller i utdata för varje steg i en ny mapp varje gång pipelinen körs igen, kan de versionsutgångsdatauppsättningar reproduceras. Läs mer om [datauppsättningar i pipelines](how-to-create-your-first-pipeline.md#steps).

```Python
from azureml.core import Dataset
from azureml.pipeline.steps import PythonScriptStep
from azureml.pipeline.core import Pipeline, PipelineData
from azureml.core. runconfig import CondaDependencies, RunConfiguration

# get input dataset 
input_ds = Dataset.get_by_name(workspace, 'weather_ds')

# register pipeline output as dataset
output_ds = PipelineData('prepared_weather_ds', datastore=datastore).as_dataset()
output_ds = output_ds.register(name='prepared_weather_ds', create_new_version=True)

conda = CondaDependencies.create(
    pip_packages=['azureml-defaults', 'azureml-dataprep[fuse,pandas]'], 
    pin_sdk_version=False)

run_config = RunConfiguration()
run_config.environment.docker.enabled = True
run_config.environment.python.conda_dependencies = conda

# configure pipeline step to use dataset as the input and output
prep_step = PythonScriptStep(script_name="prepare.py",
                             inputs=[input_ds.as_named_input('weather_ds')],
                             outputs=[output_ds],
                             runconfig=run_config,
                             compute_target=compute_target,
                             source_directory=project_folder)
```

<a name="track"></a>

## <a name="track-datasets-in-experiments"></a>Spåra datauppsättningar i experiment

För varje Machine Learning-experiment kan du enkelt spåra de datauppsättningar som används som indata genom experimentobjektet. `Run`

Följande kod använder [`get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-details--) metoden för att spåra vilka indatauppsättningar som användes i experimentkörningen:

```Python
# get input datasets
inputs = run.get_details()['inputDatasets']
input_dataset = inputs[0]['dataset']

# list the files referenced by input_dataset
input_dataset.to_path()
```

Du kan också `input_datasets` hitta från https://ml.azure.com/experiment med hjälp av . 

Följande bild visar var du hittar indatauppsättningen för ett experiment i Azure Machine Learning studio. I det här exemplet går du till fönstret **Experiment** och öppnar fliken `keras-mnist` **Egenskaper** för en viss körning av experimentet.

![Indatauppsättningar](./media/how-to-version-track-datasets/input-datasets.png)

Använd följande kod för att registrera modeller med datauppsättningar:

```Python
model = run.register_model(model_name='keras-mlp-mnist',
                           model_path=model_path,
                           datasets =[('training data',train_dataset)])
```

Efter registreringen kan du se listan över modeller som registrerats https://ml.azure.com/med datauppsättningen med hjälp av Python eller gå till .

Följande vy kommer från fönstret **Datauppsättningar** under **Tillgångar**. Välj datauppsättningen och välj sedan fliken **Modeller** för en lista över de modeller som är registrerade med datauppsättningen. 

![Modeller för indatamängder](./media/how-to-version-track-datasets/dataset-models.png)

## <a name="next-steps"></a>Nästa steg

* [Träna med datauppsättningar](how-to-train-with-datasets.md)
* [Fler exempel på anteckningsböcker för datauppsättning](https://aka.ms/dataset-tutorial)
