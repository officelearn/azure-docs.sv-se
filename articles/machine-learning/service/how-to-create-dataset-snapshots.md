---
title: Jämför & återskapa data över tid med ögonblicksbilder
titleSuffix: Azure Machine Learning service
description: Lär dig hur du jämför data över tid och se till att reproducerbarhet med ögonblicksbilder av datauppsättning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.date: 05/02/2019
ms.openlocfilehash: 51d0dcfc543834e9a8725d11fa82b566a5132a6b
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65205006"
---
# <a name="compare-data-and-ensure-reproducibility-with-snapshots-preview"></a>Jämförelse av data och se till att reproducerbarhet med ögonblicksbilder (förhandsversion)

I den här artikeln får du lära dig att skapa och hantera ögonblicksbilder av din [Azure Machine Learning datauppsättningar](how-to-create-register-datasets.md) (datauppsättningar) så att du kan avbilda eller jämföra data över tid. Datauppsättningar som gör det enklare att komma åt och arbeta med dina data i molnet i olika scenarier.

**Datauppsättningen ögonblicksbilder** lagra en profil (sammanfattande statistik) av data när den har skapats. Du kan välja att även spara en kopia av data i ögonblicksbilden för reproducerbarhet.

>[!Important]
> Ögonblicksbilder debiteras lagringskostnader. Lagrar en kopia av data i ögonblicksbilden kräver ännu mer lagring. Använd [ `dataset.delete_snapshot()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#delete-snapshot-snapshot-name-) när de inte längre behövs.

## <a name="when-to-use-snapshots"></a>När du ska använda ögonblicksbilder

Det finns tre huvudsakliga användningsområden för ögonblicksbilder:

+ **Modellera verifiering**: Jämför dataprofil olika ögonblicksbilder mellan träningskörningar eller mot produktionsdata.

+ **Modellera reproducerbarhet**: Återskapa dina resultat genom att anropa en ögonblicksbild som innehåller data vid träning.

+ **Spåra data över tid**: Se hur datauppsättningen har utvecklats av [jämföra profiler](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_snapshot.datasetsnapshot?view=azure-ml-py#compare-profiles-rhs-dataset-snapshot--include-columns-none--exclude-columns-none--histogram-compare-method--histogramcomparemethod-wasserstein--0--)
  
## <a name="prerequisites"></a>Nödvändiga komponenter

Du behöver en registrerad datauppsättning för Azure Machine Learning för att skapa datauppsättningen ögonblicksbilder. Om du inte har någon [skapa och registrera datauppsättningar](how-to-create-register-datasets.md).

## <a name="create-dataset-snapshots"></a>Skapa datauppsättning ögonblicksbilder

Du kan skapa en ögonblicksbild av en datauppsättning med [ `dataset.create_snapshot()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?#create-snapshot-snapshot-name--compute-target-none--create-data-snapshot-false--target-datastore-none-) från Azure Machine Learning SDK.

Som standard lagrar ögonblicksbilden profil (sammanfattande statistik) av data med senast [datauppsättningsdefinitionen](how-to-manage-dataset-definitions.md) tillämpas. Definitionen av datauppsättningen innehåller en post för alla transformeringssteg som definierats för data. Det är ett bra sätt att göra din data prep fungerar reproducerbar.

Du kan också ta en kopia av data i ögonblicksbilden genom att lägga till `create_data_snapshot = True`.  Dessa data kan vara användbart för reproducerbarhet.

Det här exemplet används [exempeldata crime](https://dprepdata.blob.core.windows.net/dataset-sample-files/crime.csv) och en datauppsättning som kallas `dataset_crime` skapats med hjälp av artikeln [”skapa och registrera datauppsättningar”](how-to-create-register-datasets.md).

```Python
from azureml.core.workspace import Workspace
from azureml.core.dataset import Dataset
from azureml.data.dataset_snapshot import DatasetSnapshot
import datetime

# get existing workspace
workspace = Workspace.from_config()

# get existing, named dataset:
dataset = workspace.datasets['dataset_crime']

# assign name to snapshot
snapshot_name = 'snapshot_' + datetime.datetime.today().strftime('%Y%m%d%H%M%S')

# create snapshot to store profile from a remote environment
# and include copy of data
snapshot = dataset.create_snapshot(snapshot_name = snapshot_name,
                                   compute_target = remote_compute_target,
                                   create_data_snapshot = True)
```

Eftersom ögonblicksbilder skapas asynkront, använda den [ `wait_for_completion()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_snapshot.datasetsnapshot?view=azure-ml-py#wait-for-completion-show-output-true--status-update-frequency-10-) metod för att övervaka processen.

```Python
# monitor process every 10 seconds
snapshot.wait_for_completion(show_output=True, status_update_frequency=10)

# return snapshot just created
dataset.get_snapshot(snapshot_name)
```

Utdata:

```
Action status: Running
Action status: Running
Action status: Completed


DatasetSnapshot(Name: snapshot_20190411165420,
Dataset Id: 66cd0830-2f72-41e5-b677-d2d953f54821,
Workspace: "your_workspace_name",
Dataset definition version: 1,
Snapshot created date: 2019-05-11 17:24:00+00:00)
```

Använd [ `dataset.delete_snapshot()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#delete-snapshot-snapshot-name-) när de inte längre behövs.

## <a name="find-snapshots"></a>Hitta ögonblicksbilder

Använd för att hämta en befintlig ögonblicksbild [ `get_snapshot()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#get-snapshot-snapshot-name-).

Hämta en lista över dina sparade ögonblicksbilder av den angivna datauppsättningen [ `get_all_snapshots()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#get-all-snapshots--).

```Python
# Get named snapshot for this dataset
dataset.get_snapshot(snapshot_name)

# Get all snapshots for this dataset
dataset.get_all_snapshots()
```

## <a name="get-data-and-profiles-from-snapshots"></a>Hämta och profiler från ögonblicksbilder

Varje ögonblicksbild genererar en profil för datauppsättningen, vilket inkluderar sammanfattande statistik och ger dig möjlighet att spara en kopia av dina data.

### <a name="get-the-data-profile"></a>Hämta dataprofil

Använd [ `get_profile()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#get-profile-arguments-none--generate-if-not-exist-true--workspace-none--compute-target-none-) till profilen för dina data.  Du kan använda den här profilen för att jämföra utbildnings- och data, till exempel.

```Python
snapshot.get_profile()
```

||Typ|Min|Max|Antal|Antal saknas|Antal saknas inte|Procent saknas|Antal fel|Tomt antal|0,1 % kvantil|1 % kvantil|5 % kvantil|25 % kvantil|50 % kvantil|75 % kvantil|95 % kvantil|99 % kvantil|99,9 % kvantil|Medelvärde|Standardavvikelse|Varians|Snedhet|Toppighet
-|----|---|---|-----|-------------|-----------------|---------------|-----------|-----------|-------------|-----------|-----------|------------|------------|------------|------------|------------|--------------|----|------------------|--------|--------|--------
ID|FieldType.INTEGER|1.04986e + 07|1.05351e + 07|10.0|0.0|10.0|0.0|0.0|0.0|1.04986e + 07|1.04992e+07|1.04986e + 07|1.05166e + 07|1.05209e + 07|1.05259e + 07|1.05351e + 07|1.05351e + 07|1.05351e + 07|1.05195e + 07|12302.7|1.51358e + 08|-0.495701|-1.02814
Ärendenummer|FieldType.STRING|HZ239907|HZ278872|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Date|FieldType.DATE|2016-04-04 23:56:00+00:00|2016-04-15 17:00:00+00:00|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Blockera|FieldType.STRING|004XX S KILBOURN PARA|113XX S PRAIRIE PARA|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
IUCR|FieldType.INTEGER|810|1154|10.0|0.0|10.0|0.0|0.0|0.0|810|850|810|890|1136|1153|1154|1154|1154|1058.5|137.285|18847.2|-0.785501|-1.3543
Primär typ|FieldType.STRING|BEDRÄGERIFÖRSÖK|STÖLD|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Beskrivning|FieldType.STRING|MUSENHETEN KONTROLL|ÖVER 500 USD|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Platsbeskrivning|FieldType.STRING||SKOLAN, OFFENTLIG, ATT SKAPA|10.0|0.0|10.0|0.0|0.0|1.0||||||||||||||
Kvarhållande|FieldType.BOOLEAN|False|False|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Inrikes|FieldType.BOOLEAN|False|False|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Beat|FieldType.INTEGER|531|2433|10.0|0.0|10.0|0.0|0.0|0.0|531|531|531|614|1318.5|1911|2433|2433|2433|1371.1|692.094|478994|0.105418|-1.60684
Distrikt|FieldType.INTEGER|5|24|10.0|0.0|10.0|0.0|0.0|0.0|5|5|5|6|13|19|24|24|24|13.5|6.94822|48.2778|0.0930109|-1.62325
Ward|FieldType.INTEGER|1|48|10.0|0.0|10.0|0.0|0.0|0,0|1|5|1|9|22.5|40|48|48|48|24.5|16.2635|264.5|0.173723|-1.51271
Community-området|FieldType.INTEGER|4|77|10.0|0.0|10.0|0.0|0.0|0.0|4|8.5|4|24|37.5|71|77|77|77|41.2|26.6366|709.511|0.112157|-1.73379

### <a name="get-the-data-from-the-snapshot"></a>Hämta data från ögonblicksbilden

För att få en kopia av data som sparas i en datauppsättning ögonblicksbild kan generera en pandas-DataFrame med den [ `to_pandas_dataframe()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#to-pandas-dataframe--) metod.

Den här metoden misslyckas om en kopia av informationen som inte begärdes när ögonblicksbilder skapas.

```Python
snapshot.to_pandas_dataframe().head(3)
```

||ID|Ärendenummer|Date|Blockera|IUCR|Primär typ|Beskrivning|Platsbeskrivning|Kvarhållande|Inrikes|...|Ward|Community-området|FBI: S kod|X-koordinat|Y-koordinaten|År|Uppdaterad den|Latitud|Longitud|Plats
-|--|-----------|----|-----|----|------------|-----------|--------------------|------|--------|---|----|--------------|--------|------------|------------|----|----------|--------|---------|--------
0|10498554|HZ239907|2016-04-04 23:56:00|007XX E 111TH ST|1153|BEDRÄGERIFÖRSÖK|FINANSIELLA IDENTITETSSTÖLDER ÖVER 300 USD|ANDRA|False|False|...|9|50|11|1183356.0|1831503.0|2016|2016-05-11 15:48:00|41.692834|-87.604319|(41.692833841, -87.60431945)
1|10516598|HZ258664|2016-04-15 17:00:00|082XX S MARSHFIELD AVE|890|STÖLD|FRÅN ATT BYGGA|BOSATT|False|False|...|21|71|6|1166776.0|1850053.0|2016|2016-05-12 15:48:00|41.744107|-87.664494|(41.744106973, -87.664494285)
2|10519196|HZ261252|2016-04-15 10:00:00|104XX S SACRAMENTO PARA|1154|BEDRÄGERIFÖRSÖK|FINANSIELLA IDENTITETSSTÖLDER 300 USD OCH UNDER|BOSATT|False|False|...|19|74|11|NaN|NaN|2016|2016-05-12 15:50:00|NaN|NaN|

## <a name="next-steps"></a>Nästa steg

* Finns i SDK [översikt](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) designmönster och användningsexempel.

* Ett exempel på hur du använder datauppsättningen ögonblicksbilder finns i den [exempel anteckningsböcker](https://aka.ms/dataset-tutorial).
