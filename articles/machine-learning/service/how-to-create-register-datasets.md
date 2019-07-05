---
title: Skapa datauppsättningar för att komma åt data med azureml-datauppsättningar
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
ms.date: 05/21/2019
ms.openlocfilehash: a879fa17244977277dab3e2e66c5888a44759764
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444035"
---
# <a name="create-and-access-datasets-preview-in-azure-machine-learning"></a>Skapa och komma åt datauppsättningar (förhandsversion) i Azure Machine Learning

I den här artikeln får lära du dig hur du skapar datauppsättningar för Azure Machine Learning (förhandsversion) och hur du kommer åt data från lokala och fjärranslutna experiment.

Med hanterade datauppsättningar kan du: 
* **Enkelt komma åt data under modellträning** utan återansluter till underliggande datalager

* **Se till att datakonsekvens och reproducerbarhet** med hjälp av samma pekaren över experiment: bärbara datorer, automatiserade ml, pipelines, visuella gränssnittet

* **Dela data & samarbeta** med andra användare

* **Utforska data** & Hantera livscykeln för ögonblicksbilder av data och versioner

* **Jämför data** i utbildning till produktion


## <a name="prerequisites"></a>Förutsättningar

Om du vill skapa och arbeta med datauppsättningar, behöver du:

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnadsfria versionen eller betalversionen av Azure Machine Learning-tjänsten](https://aka.ms/AMLFree) i dag.

* En [Azure Machine Learning-tjänstens arbetsyta](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace)

* Den [Azure Machine Learning-SDK för Python installerat](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), vilket inkluderar paketets azureml-datauppsättningar.

> [!Note]
> Vissa klasser för datauppsättningen (förhandsversion) är beroende av den [azureml-förberedelse av data](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) paketet (GA). De här klasserna stöds endast på följande distributioner för Linux-användare:  Red Hat Enterprise Linux, Ubuntu, Fedora och CentOS.

## <a name="data-formats"></a>Dataformat

Du kan skapa en Azure Machine Learning-datauppsättning från följande data:
+ [delimited](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset#from-delimited-files-path--separator------header--promoteheadersbehavior-all-files-have-same-headers--3---encoding--fileencoding-utf8--0---quoting-false--infer-column-types-true--skip-rows-0--skip-mode--skiplinesbehavior-no-rows--0---comment-none--include-path-false--archive-options-none-)
+ [binary](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-binary-files-path-)
+ [json](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-json-files-path--encoding--fileencoding-utf8--0---flatten-nested-arrays-false--include-path-false-)
+ [Excel](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-excel-files-path--sheet-name-none--use-column-headers-false--skip-rows-0--include-path-false--infer-column-types-true-)
+ [Parquet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-parquet-files-path--include-path-false-)
+ [Azure SQL Database](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-sql-query-data-source--query-)
+ [Azure Data Lake gen. 1](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-sql-query-data-source--query-)

## <a name="create-datasets"></a>Skapa datauppsättningar 

Du kan interagera med dina datauppsättningar med paketet azureml-datauppsättningar i den [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) , särskilt [den `Dataset` klass](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py).

### <a name="create-from-local-files"></a>Skapa från lokala filer

Läsa in filer från din lokala dator genom att ange sökvägen för filen eller mappen med den [ `auto_read_files()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#auto-read-files-path--include-path-false-) metod från den `Dataset` klass.  Den här metoden utför följande steg som kräver att du kan ange filtypen eller parsning argument:

* Procedurens och ställa in avgränsaren.
* Hoppar över tomma poster överst i filen.
* Procedurens och ange rubrikraden.
* Procedurens och konvertera datatyperna för kolumnen.

```Python
from azureml.core.dataset import Dataset

dataset = Dataset.auto_read_files('./data/crime.csv')
```

Alternativt använda fil-specifika funktioner att uttryckligen styra parsningen av din fil. 


### <a name="create-from-azure-datastores"></a>Skapa från Azure datalager

Skapa datauppsättningar från en Azure-datalager:

* Kontrollera att du har `contributor` eller `owner` åtkomst till registrerade Azure-databasen.

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

## <a name="register-datasets"></a>Registrera datauppsättningar

Slutför processen genom att registrera dina datauppsättningar med arbetsyta:

Använd den [ `register()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--visible-true--exist-ok-false--update-if-exist-false-) metod för att registrera datauppsättningar till arbetsytan så att de kan delas med andra och återanvändas i olika experiment.

```Python
dataset = dataset.register(workspace = workspace,
                           name = 'dataset_crime',
                           description = 'Training data',
                           exist_ok = False
                           )
```

>[!NOTE]
> Om `exist_ok = False` (standard), och du försöker registrera en datauppsättning med samma namn som en annan, ett fel inträffar. Ange `True` att skriva över befintliga.

## <a name="access-data-in-datasets"></a>Åtkomst till data i datauppsättningar

Registrerade datauppsättningar är tillgängliga och använda lokalt, via en fjärranslutning och på vilket beräkningsklustren som Azure Machine Learning-databearbetning. Om du vill återanvända din registrerade datauppsättning över experiment och compute miljöer, Använd följande kod för att hämta din arbetsyta och registrerade datauppsättning efter namn.

```Python
workspace = Workspace.from_config()

# See list of datasets registered in workspace.
Dataset.list(workspace)

# Get dataset by name
dataset = workspace.datasets['dataset_crime']
```

## <a name="next-steps"></a>Nästa steg

* [Utforska och förbereda datauppsättningar](how-to-explore-prepare-data.md).
* Ett exempel på hur du använder datauppsättningar finns i den [exempel anteckningsböcker](https://aka.ms/dataset-tutorial).
