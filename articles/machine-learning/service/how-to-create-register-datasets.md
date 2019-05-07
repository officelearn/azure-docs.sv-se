---
title: Skapa och registrera datauppsättningar i din arbetsyta
titleSuffix: Azure Machine Learning service
description: Lär dig hur du skapar datauppsättningar från olika källor och registrera datauppsättningar med din arbetsyta
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 05/02/19
ms.openlocfilehash: 65a861c647c2dc92e416fa356075821aa5060042
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65205044"
---
# <a name="create-and-register-azure-machine-learning-datasets-preview"></a>Skapa och registrera Azure Machine Learning datauppsättningar (förhandsversion)

I den här artikeln får du lära dig Azure Machine Learning-arbetsflöden för att skapa och registrera datauppsättningar och hur du kommer åt dem för användning i lokala och fjärranslutna experiment.

Azure Machine Learning datauppsättningar (förhandsversion) gör det enklare att komma åt och arbeta med dina data. Datauppsättningar hantera data i olika scenarier, till exempel träning och skapa en pipeline. Med hjälp av den [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py), du kan arbeta med data i populära format, få åtkomst till underliggande lagring, utforska och förbereda data, hantera livscykeln för olika datauppsättning definitioner och jämför datauppsättningar som används i utbildning och i produktion.

## <a name="prerequisites"></a>Nödvändiga komponenter

Skapa och registrera datauppsättningar som du behöver:

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnadsfria versionen eller betalversionen av Azure Machine Learning-tjänsten](https://aka.ms/AMLFree) i dag.

* En arbetsyta för Azure Machine Learning-tjänsten. Se [skapa en arbetsyta för Azure Machine Learning-tjänsten](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace).

* Azure Machine Learning SDK för Python. Om du vill installera eller uppdatera till den senaste versionen av SDK, se [installera eller uppdatera SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

## <a name="create-datasets-from-local-files"></a>Skapa datauppsättningar från lokala filer

Läsa in filer från din lokala dator genom att ange sökvägen för filen eller mappen med den [ `auto_read_files()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#auto-read-files-path--include-path-false-) metod från den `Dataset` klass.  Den här metoden utför följande steg som kräver att du kan ange filtypen eller parsning argument:

* Procedurens och ställa in avgränsaren.
* Hoppar över tomma poster överst i filen.
* Procedurens och ange rubrikraden.
* Procedurens och konvertera datatyperna för kolumnen.

```Python
from azureml.core.dataset import Dataset

dataset = Dataset.auto_read_files('./data/crime.csv')
```

Alternativt använda fil-specifika funktioner att uttryckligen styra parsningen av din fil. Datasets SDK stöder för närvarande [avgränsade](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-delimited-files-path--separator------header--promoteheadersbehavior-allfileshavesameheaders--3---encoding--fileencoding-utf8--0---quoting-false--infer-column-types-true--skip-rows-0--skip-mode--skiplinesbehavior-norows--0---comment-none--include-path-false--archive-options-none-), [Excel](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-excel-files-path--sheet-name-none--use-column-headers-false--skip-rows-0--include-path-false--infer-column-types-true-), [Parquet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-parquet-files-path--include-path-false-), [binära](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-binary-files-path-), och [json](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-json-files-path--encoding--fileencoding-utf8--0---flatten-nested-arrays-false--include-path-false-) filformat.

## <a name="create-datasets-from-azure-datastores"></a>Skapa datauppsättningar från Azure datalager

Om du vill skapa datauppsättningar från en Azure-datalager, måste du kontrollera att:

* Kontrollera att du har deltagare eller ägare som har åtkomst till registrerade Azure-databasen.

* Importera den [ `Workspace` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) och [ `Datastore` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#definition) och `Dataset` paket från SDK.

```Python
from azureml.core.workspace import Workspace
from azureml.core.datastore import Datastore
from azureml.core.dataset import Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()
```

 Den `get()` metoden hämtar ett befintligt datalager på arbetsytan.

```
dstore = Datastore.get(workspace, datastore_name)
```

Använd den `from_delimited_files()` metod för att läsa i avgränsade filer och skapa en oregistrerad datauppsättning.

```Python
# create an in-memory Dataset on your local machine
datapath = dstore.path('data/src/crime.csv')
dataset = Dataset.from_delimited_files(datapath)

# returns the first 5 rows of the Dataset as a pandas Dataframe.
dataset.head(5)
```

||ID|Ärendenummer|Date|Blockera|IUCR|Primär typ|Beskrivning|Platsbeskrivning|Kvarhållande|Inrikes|...|Ward|Community-området|FBI: S kod|X-koordinat|Y-koordinaten|År|Uppdaterad den|Latitud|Longitud|Plats|
|--|--|---|---|---|---|----|------|-------|------|-----|---|----|----|-----|-----|------|----|-----|----|----|-----
|0|10498554|HZ239907|4/4/2016 23:56|007XX E 111TH ST|1153|BEDRÄGERIFÖRSÖK|FINANSIELLA IDENTITETSSTÖLDER ÖVER 300 USD|ANDRA|FALSKT|FALSKT|...|9|50|11|1183356|1831503|2016|5/11/2016 15:48|41.69283384|-87.60431945|(41.692833841, -87.60431945)|
1|10516598|HZ258664|4/15/2016 17:00|082XX S MARSHFIELD AVE|890|STÖLD| FRÅN ATT BYGGA|BOSATT|FALSKT|FALSKT|...|21|71|6|1166776|1850053|2016|5/12/2016 15:48|41.74410697|-87.66449429|(41.744106973, -87.664494285)
2|10519196|HZ261252|4/15/2016 10:00|104XX S SACRAMENTO PARA|1154|BEDRÄGERIFÖRSÖK|FINANSIELLA IDENTITETSSTÖLDER 300 USD OCH UNDER|BOSATT|FALSKT|FALSKT|...|19|74|11|||2016|5/12/2016 15:50
3|10519591|HZ261534|4/15/2016 9:00|113XX S PRAIRIE PARA|1120|BEDRÄGERIFÖRSÖK|FÖRFALSKNING|BOSATT|FALSKT|FALSKT|...|9|49|10|||2016|5/13/2016 15:51
4|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE AVE|890|STÖLD|FRÅN ATT BYGGA|SKOLAN, OFFENTLIG, ATT SKAPA|FALSKT|FALSKT|...|40|13|6|||2016|5/25/2016 15:59|

## <a name="register-your-datasets-with-workspace"></a>Registrera dina datauppsättningar med arbetsytan

Använd den [ `register()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--visible-true--exist-ok-false--update-if-exist-false-) metod för att registrera datauppsättningar till din arbetsyta för att dela och återanvända inom din organisation och mellan olika experiment.

```Python
dataset = dataset.register(workspace = workspace,
                           name = 'dataset_crime',

                           description = 'Training data',
                           exist_ok = False
                           )
```

>[!NOTE]
> Standardinställningen för parametern för `register()` är `exist_ok = False`. Om du försöker registrera en datauppsättning med samma namn utan att ändra den här inställningen resulterar i ett fel.

Den `register()` returnerar metoden redan registrerade datauppsättningen med parameterinställningen `exist_ok = True`.

```Python
dataset = dataset.register(workspace = workspace,
                           name = 'dataset_crime',
```

Använd `list()` att se alla registrerade datauppsättningar i din arbetsyta.

```Python
Dataset.list(workspace_name)
```

Föregående kod resulterar i följande:

```Python
[Dataset(Name: dataset_crime,
         Workspace: workspace_name)]
```

## <a name="access-datasets-in-workspace"></a>Åtkomst till datauppsättningar i arbetsyta

Registrerade datauppsättningar är tillgängliga och använda lokalt, via en fjärranslutning och på vilket beräkningsklustren som Azure Machine Learning-databearbetning. Om du vill återanvända din registrerade datauppsättning över experiment och compute miljöer, Använd följande kod för att hämta din arbetsyta och registrerade datauppsättning efter namn.

```Python
workspace = Workspace.from_config()

dataset = workspace.datasets['dataset_crime']
```

## <a name="next-steps"></a>Nästa steg

* [Utforska och förbereda datauppsättningar](how-to-explore-prepare-data.md).
* [Hantera livscykeln för datauppsättningen definitioner](how-to-manage-dataset-definitions.md).
* Ett exempel på hur du använder datauppsättningar finns i den [exempel anteckningsböcker](https://aka.ms/dataset-tutorial).
