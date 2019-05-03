---
title: Definitionen av datauppsättningen och versionshantering
titleSuffix: Azure Machine Learning service
description: Lär dig hur du uppdaterar datauppsättningen definitioner och hanterar livscykeln för definitioner
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: larryfr
ms.date: 05/02/2019
ms.openlocfilehash: 7c861c8cdc9985caa42bd2beb5236a4f4e93e4c7
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028687"
---
# <a name="update-and-manage-the-lifecycle-of-dataset-definitions"></a>Uppdatera och hantera livscykeln för datauppsättningen definitioner

Lär dig mer om att uppdatera och hantera datauppsättning definitioner med datauppsättningar för Azure Machine Learning (förhandsversion).

Definitionerna för datauppsättningen är recept som används för att förbereda dina datauppsättningar som omfattar både referensen till dina datakällor och transformering stegen som utförs. En datauppsättning kan ha många definitioner, och varje definition har sin egen livscykel.

Följande scenario är ett exempel på hur du använder datauppsättningen definitioner:

1. Du kan skapa en machine learning-pipeline som använder den aktuella definitionen av din datauppsättning.
1. Underliggande data ändras; nya attribut har lagts till.
1. Du skapar en ny version av din definition som lägger till extra transformationer för att hantera nya attribut.

I det här exemplet fortsätter befintliga pipelinen att använda den ursprungliga definitionsversionen. Den nya definitionsversionen kan användas för nya scenarier, till exempel träna en modell eller skapa en pipeline.

## <a name="prerequisites"></a>Nödvändiga komponenter

Du måste ha en Azure-prenumeration och en arbetsyta för att registrera din datauppsättning för att kunna hantera livscykeln för datauppsättningen definitioner.

Exempelfil som används i exemplen i det här dokumentet finns på [ https://dprepdata.blob.core.windows.net/dataset-sample-files/crime.csv ](https://dprepdata.blob.core.windows.net/dataset-sample-files/crime.csv).

## <a name="update-dataset-definitions"></a>Uppdatera definitioner för datauppsättning

Vi först skapa och registrera en datauppsättning med din arbetsyta.

```python
from azureml.core import Workspace, Datastore, Dataset

# change the configuration for workspace and Datastore
subscription_id = 'your subscription id'
resource_group = 'your resource group name'
workspace_name = 'your workspace name'
datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace(subscription_id, resource_group, workspace_name)

# get a Datastore that has already been created in the workspace
dstore = Datastore.get(workspace, datastore_name)

# create an in-memory Dataset from Datastore
datapath = dstore.path('data/src/crime.csv')
dataset = Dataset.from_delimited_files(datapath)

# register the Dataset with the workspace. if a Dataset with the same name already exists, retrieve it by turning `exist_ok = True`
dataset_name = 'crime dataset'
dataset = dataset.register(workspace = workspace, 
                 name = dataset_name, 
                 description = 'crime dataset for demo', 
                 tags = {'year':'2019', 'month':'Apr'},
                 exist_ok = True)
```

Definitionen för första datamängden (`version_id` = 1) skapas när datauppsättningen skapas. Varje gång du uppdaterar din datauppsättningsdefinitionen sedan tilldelas en ny version_id till de senaste definitionerna.

```python
# get the Dataset from the workspace by name
dataset = workspace.datasets['dataset_name']

# get the latest Dataset definition
ds_def = dataset.get_definition()

# update the Dataset definition to select only the columns I'm interested in
ds_def = ds_def.keep_columns(['ID', 'Arrest', 'Latitude', 'Longitude'])

# with this update, the latest definition for dataset now has `version_id = 2`
dataset = dataset.update_definition(ds_def, 'select useful column')

dataset = workspace.datasets['dataset_name']
dataset.head(5)
```
<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>ID</th>
      <th>Kvarhållande</th>
      <th>Latitud</th>
      <th>Longitud</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>10498554</td>
      <td>FALSKT</td>
      <td>41.69283384</td>
      <td>-87.60431945</td>
    </tr>
    <tr>
      <th>1</th>
      <td>10516598</td>
      <td>FALSKT</td>
      <td>41.74410697</td>
      <td>-87.66449429</td>
    </tr>
    <tr>
      <th>2</th>
      <td>10519196</td>
      <td>FALSKT</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>3</th>
      <td>10519591</td>
      <td>FALSKT</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>4</th>
      <td>10534446</td>
      <td>FALSKT</td>
      <td></td>
      <td></td>
    </tr>
  </tbody>
</table>
</div>

Den här Definitionsuppdatering raderas inte den tidigare datauppsättningen definitioner. Du kan fortfarande komma åt och använda tidigare definitioner.

```python
# specify `version_id` to get a previous definition 
ds_def_old = dataset.get_definition(version_id = 1)
ds_def_old.head(5)
```
<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>ID</th>
      <th>Ärendenummer</th>
      <th>Date</th>
      <th>Blockera</th>
      <th>IUCR</th>
      <th>Primär typ</th>
      <th>Beskrivning</th>
      <th>Platsbeskrivning</th>
      <th>Kvarhållande</th>
      <th>Inrikes</th>
      <th>...</th>
      <th>Ward</th>
      <th>Community-området</th>
      <th>FBI: S kod</th>
      <th>X-koordinat</th>
      <th>Y-koordinaten</th>
      <th>År</th>
      <th>Uppdaterad den</th>
      <th>Latitud</th>
      <th>Longitud</th>
      <th>Plats</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>10498554</td>
      <td>HZ239907</td>
      <td>4/4/2016 23:56</td>
      <td>007XX E 111TH ST</td>
      <td>1153</td>
      <td>BEDRÄGERIFÖRSÖK</td>
      <td>FINANSIELLA IDENTITETSSTÖLDER ÖVER 300 USD</td>
      <td>ANDRA</td>
      <td>FALSKT</td>
      <td>FALSKT</td>
      <td>...</td>
      <td>9</td>
      <td>50</td>
      <td>11</td>
      <td>1183356</td>
      <td>1831503</td>
      <td>2016</td>
      <td>5/11/2016 15:48</td>
      <td>41.69283384</td>
      <td>-87.60431945</td>
      <td>(41.692833841, -87.60431945)</td>
    </tr>
    <tr>
      <th>1</th>
      <td>10516598</td>
      <td>HZ258664</td>
      <td>4/15/2016 17:00</td>
      <td>082XX S MARSHFIELD AVE</td>
      <td>890</td>
      <td>STÖLD</td>
      <td>FRÅN ATT BYGGA</td>
      <td>BOSATT</td>
      <td>FALSKT</td>
      <td>FALSKT</td>
      <td>...</td>
      <td>21</td>
      <td>71</td>
      <td>6</td>
      <td>1166776</td>
      <td>1850053</td>
      <td>2016</td>
      <td>5/12/2016 15:48</td>
      <td>41.74410697</td>
      <td>-87.66449429</td>
      <td>(41.744106973, -87.664494285)</td>
    </tr>
    <tr>
      <th>2</th>
      <td>10519196</td>
      <td>HZ261252</td>
      <td>4/15/2016 10:00</td>
      <td>104XX S SACRAMENTO PARA</td>
      <td>1154</td>
      <td>BEDRÄGERIFÖRSÖK</td>
      <td>FINANSIELLA IDENTITETSSTÖLDER 300 USD OCH UNDER</td>
      <td>BOSATT</td>
      <td>FALSKT</td>
      <td>FALSKT</td>
      <td>...</td>
      <td>19</td>
      <td>74</td>
      <td>11</td>
      <td></td>
      <td></td>
      <td>2016</td>
      <td>5/12/2016 15:50</td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>3</th>
      <td>10519591</td>
      <td>HZ261534</td>
      <td>4/15/2016 9:00</td>
      <td>113XX S PRAIRIE PARA</td>
      <td>1120</td>
      <td>BEDRÄGERIFÖRSÖK</td>
      <td>FÖRFALSKNING</td>
      <td>BOSATT</td>
      <td>FALSKT</td>
      <td>FALSKT</td>
      <td>...</td>
      <td>9</td>
      <td>49</td>
      <td>10</td>
      <td></td>
      <td></td>
      <td>2016</td>
      <td>5/13/2016 15:51</td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>4</th>
      <td>10534446</td>
      <td>HZ277630</td>
      <td>4/15/2016 10:00</td>
      <td>055XX N KEDZIE AVE</td>
      <td>890</td>
      <td>STÖLD</td>
      <td>FRÅN ATT BYGGA</td>
      <td>SKOLAN, OFFENTLIG, ATT SKAPA</td>
      <td>FALSKT</td>
      <td>FALSKT</td>
      <td>...</td>
      <td>40</td>
      <td>13</td>
      <td>6</td>
      <td></td>
      <td></td>
      <td>2016</td>
      <td>5/25/2016 15:59</td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
  </tbody>
</table>
</div>

Som vi kan se resultatet kan ser den första versionen av datauppsättningsdefinitionen fortfarande till att alla kolumner från crime-data oavsett senare uppdatering. Till exempel om du har en maskininlärning modell för närvarande använder den första versionen av datauppsättningen och använder `Date` kolumn i datauppsättningen som en av funktionerna, kommer det inte uppstår fel med den senaste definitionsuppdateringen, vilket gör att endast `ID`, `Arrest`, `Latitude`, `Longitude` kolumner från crime-data.

## <a name="how-to-access-dataset-definitions"></a>Hur du kommer åt datauppsättningen definitioner

Hämta en lista över alla definitioner för `get_definitions()`. Hämta en specifik version av definitionen `get_definition()`. I följande exempel visar hämtar en lista över alla definitioner och sedan hämta version 1:

```python
# list all definitions for the dataset
dataset.get_definitions()
# get version ID 1
dataset.get_definition(version_id=1)
```

Utdata från `get_definitions()` liknar följande exempel:

```text
{'2': VersionID: 2, State: active, Created: 2019-04-19 16:43:52.439890+00:00, Modified: 2019-04-19 16:43:52.439890+00:00, Notes: select useful column,
 '1': VersionID: 1, State: active, Created: 2019-04-19 16:39:14.112046+00:00, Modified: 2019-04-19 16:44:12.912663+00:00, Notes: None}
```

När du har hämtat en definition kan använda du det med dina maskininlärningsmodeller eller i en machine learning-pipeline.

## <a name="manage-lifecycle-of-dataset-definitions"></a>Hantera livscykeln för datauppsättningen definitioner

Du kan hantera livscykeln för varje datauppsättningsdefinitionen oberoende av varandra. Det finns tre steg i livscykeln: aktiv, inaktuella eller arkiveras.

### <a name="active"></a>Active

När en ny datauppsättningsdefinitionen skapas, är den aktiv som standard. 

### <a name="deprecate"></a>Avverka

Datauppsättningen definitioner kan att bli inaktuella när användning inte längre rekommenderas, och en ersättning är tillgänglig. När en föråldrad datauppsättningsdefinitionen används i machine learning pipelines, ett varningsmeddelande returneras, men körningen blockeras inte.

När du avvecklar ett datauppsättningsdefinitionen, ange Datauppsättnings-ID och datauppsättningen definition versions-ID för ersättning definition. Den här informationen lagras och används i varningsmeddelandet när du försöker använda en föråldrad datauppsättningsdefinitionen.

```python
# get the definition that you want to deprecate
ds_def = dataset.get_definition(version_id = 1)

# deprecate it by providing the information for the replacement definition, which is recommended to be used in machine learning scenarios
ds_def.deprecate(deprecate_by_dataset_id=dataset.id, deprecated_by_definition_version=2)
```

### <a name="archive"></a>Arkiv

Datauppsättningen definitioner kan arkiveras definitioner är inte avsedda för att användas av någon anledning (till exempel underliggande data inte längre tillgänglig). När en arkiverad datauppsättningsdefinitionen används i machine learning pipelines, blockeras körning med fel.

```python
# archive the definition with `version_id = 1`
ds_def = dataset.get_definition(version_id = 1)
ds_def.archive()
```

### <a name="reactivate"></a>Återaktivera

Du kan återaktivera alla inaktuella eller arkiverade datauppsättningsdefinitionen enkelt.

```python
# reactivate Dataset definition with `version_id =1` which was archived in the previous step
ds_def = dataset.get_definition(version_id = 1)
ds_def.reactivate()
```

## <a name="next-steps"></a>Nästa steg

Läs mer om hur du arbetar med datauppsättningar, [skapa och registrera Azure Machine Learning datauppsättningar](how-to-create-register-datasets.md).

Ett exempel på hur du använder datauppsättningar finns i den [exempel anteckningsböcker](https://aka.ms/dataset-tutorial).