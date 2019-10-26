---
title: Versions hantering för data uppsättning
titleSuffix: Azure Machine Learning service
description: Lär dig det bästa sättet att version av data uppsättningar och hur versions hantering fungerar med maskin inlärnings pipeliner
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: sihhu
ms.reviewer: nibaccam
ms.date: 10/25/2019
ms.custom: ''
ms.openlocfilehash: a361800623796cb3bc26a47c4f8f532503836124
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2019
ms.locfileid: "72902007"
---
# <a name="version-and-track-datasets-in-experiments"></a>Version och spårning av data uppsättningar i experiment

I den här instruktionen får du lära dig hur du använder version och spår Azure Machine Learning data uppsättningar för reproducerbarhet. Versions hantering av data uppsättningar är ett sätt att ange data uppsättningens tillstånd så att du kan använda en speciell version av data uppsättningen för framtida experiment.

Typiska scenarier att överväga för versions hantering:

* När nya data är tillgängliga för omträning.
* När du använder olika metoder för data förberedelse eller funktions teknik.

## <a name="prerequisites"></a>Krav

För den här instruktionen behöver du:

- [Azure Machine Learning SDK för python installerat](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), som innehåller paketet [azureml-DataSets](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset?view=azure-ml-py) .
    
- En [Azure Machine Learning-arbetsyta](concept-workspace.md). Hämta en befintlig med följande kod eller [skapa en ny arbets yta](how-to-manage-workspace.md).

    ```Python
    import azureml.core
    from azureml.core import Workspace
    
    ws = Workspace.from_config()
    ```
- En [Azure Machine Learning data uppsättning](how-to-create-register-datasets.md).

<a name="register"></a>

## <a name="register-and-retrieve-dataset-versions"></a>Registrera och hämta data uppsättnings versioner

Genom att registrera en data uppsättning kan du version, åter användning och dela den mellan experiment och med kollegor. Du kan registrera flera data uppsättningar med samma namn och hämta en speciell version efter namn och versions nummer.

### <a name="register-a-dataset-version"></a>Registrera en data uppsättnings version

Följande kod registrerar en ny version av data uppsättningen, `titanic_ds`genom att ange parametern `create_new_version` till `True`. Om det inte finns några befintliga `titanic_ds` som är registrerade i arbets ytan skapas en ny data uppsättning med namnet `titanic_ds` och ställer in dess version till 1.

```Python
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'titanic training data',
                                 create_new_version = True)
```

### <a name="retrieve-a-dataset-by-name"></a>Hämta en data uppsättning efter namn

Som standard returnerar metoden [get_by_name ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--) i klassen `Dataset` den senaste versionen av data uppsättningen som är registrerad i arbets ytan. 

Följande kod hämtar version 1 av `titanic_ds`-datauppsättningen.

```Python
from azureml.core import Dataset
# Get a dataset by name and version number
titanic_ds = Dataset.get_by_name(workspace = workspace,
                                 name = 'titanic_ds', 
                                 version = 1)
```

<a name="best-practice"></a>

## <a name="versioning-best-practice"></a>Metod tips för versions hantering

När du skapar en data uppsättnings version skapar du **inte** en extra kopia av data med arbets ytan. Data uppsättningar är referenser till data i lagrings tjänsten, så du kan bara ha en enda källa med sanningen som hanteras av lagrings tjänsten. 

>[!IMPORTANT]
> Om data som data mängden refererar till skrivs över eller tas bort, kan en angiven version av data uppsättningen inte återställas om du anropar en angiven version av data uppsättningen. 

Vid inläsning av data från en data uppsättning kommer det alltid att läsa in det aktuella data innehåll som data uppsättningen refererar till. Om du vill säkerställa reproducerbarheten för varje data uppsättnings version rekommenderar vi att du inte ändrar data innehåll som data uppsättnings versionen refererar till. När nya data kommer in, sparar du nya datafiler i en separat datamapp och skapar en ny data uppsättnings version som innehåller data från den nya datamappen.

Följande bild och exempel kod visar det rekommenderade sättet att strukturera datamapparna och skapa data uppsättnings versioner som refererar till dessa mappar.

![Mappstruktur](media/how-to-version-datasets/folder-image.png)

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

## <a name="version-a-pipeline-output-dataset"></a>Version en data uppsättning för pipeline-utdata

Du kan använda data uppsättning som indata och utdata för varje pipeline-steg för Machine Learning (ML). När du kör pipelines igen registreras utdata för varje pipeline-steg som en ny data uppsättnings version. 

Eftersom ML-pipeliner fyller utdata från varje steg i en ny mapp varje gång pipelinen körs på nytt, blir de versioner av data uppsättningarna som är i drift att återproduceras.

```Python
from azureml.core import Dataset
from azureml.pipeline.steps import PythonScriptStep
from azureml.pipeline.core import Pipeline, PipelineData

# get input dataset 
input_ds = Dataset.get_by_name(workspace, 'weather_ds')

# register pipeline output as dataset
output_ds = PipelineData('prepared_weather_ds', datastore=datastore).as_dataset()
output_ds = output_ds.register(name='prepared_weather_ds', create_new_version=True)

# configure pipeline step to use dataset as the input and output
prep_step = PythonScriptStep(script_name="prepare.py",
                             inputs=[input_ds.as_named_input('weather_ds')],
                             outputs=[output_ds],
                             compute_target=compute_target,
                             source_directory=project_folder)
```

<a name="track"></a>

## <a name="track-datasets-in-experiments"></a>Spåra data uppsättningar i experiment

För varje Machine Learning-experiment kan du enkelt spåra de data uppsättningar som används som indata via den registrerade modellens `Run`-objekt.

Använd följande kod för att registrera modeller med data uppsättningar.

```Python
model = run.register_model(model_name='keras-mlp-mnist',
                           model_path=model_path,
                           datasets =[('training data',train_dataset)])
```

Efter registreringen kan du se listan över modeller som har registrerats med data uppsättningen med hjälp av python eller [landnings sidan för arbets ytan](https://ml.azure.com/).

I följande kod används metoden [`get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-details--) för att spåra vilka indata-datauppsättningar som användes för att köra experimentet.

```Python
# get input datasets
inputs = run.get_details()['inputDatasets']
train_dataset = inputs[0]['dataset']

# list the files referenced by train_dataset
train_dataset.to_path()
```

Du kan också hitta `input_datasets` från experiment med hjälp av [sidan landning av arbets ytor (för hands version)](https://ml.azure.com/). 

Följande bild visar var du hittar indata-datauppsättningen för ett experiment på sidans landnings sida för arbets ytan. I det här exemplet går du till fönstret **experiment** och öppnar fliken **Egenskaper** för en speciell körning av experimentet `keras-mnist`. 

![Indata-datauppsättningar](media/how-to-version-datasets/input-datasets.png)

Du kan också hitta modeller som använde din data uppsättning med landnings sidan för arbets ytan. Följande vy kommer från bladet data uppsättningar under till gångar. Välj data uppsättningen och navigera till fliken modeller för att visa en lista över modeller som använder den data uppsättningen. 

![Data uppsättnings modeller för indata](media/how-to-version-datasets/dataset-models.png)

## <a name="next-steps"></a>Nästa steg

* [Träna med data uppsättningar](how-to-train-with-datasets.md).
* [Fler exempel på bärbara datorer av data uppsättningar](https://aka.ms/dataset-tutorial).
