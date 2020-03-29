---
title: Skapa och utforska datauppsättningar med etiketter
titleSuffix: Azure Machine Learning
description: Lär dig hur du exporterar dataetiketter från dina Azure Machine Learning-märkningsprojekt och använder dem för maskininlärningsuppgifter.
author: nibaccam
ms.author: nibaccam
ms.service: machine-learning
ms.topic: how-to
ms.date: 01/21/2020
ms.openlocfilehash: 5138109de3f80d405ce95b605714b511480563f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76549494"
---
# <a name="create-and-explore-azure-machine-learning-dataset-with-labels"></a>Skapa och utforska Azure Machine Learning-datauppsättning med etiketter

I den här artikeln får du lära dig hur du exporterar dataetiketterna från ett Azure Machine Learning-datamärkningsprojekt och läser in dem i populära format som en pandas-dataram för datautforskning eller en Torchvision-datauppsättning för bildomvandling. 

## <a name="what-are-datasets-with-labels"></a>Vad är datauppsättningar med etiketter 

Azure Machine Learning-datauppsättningar med etiketter är [TabularDatasets](how-to-create-register-datasets.md#dataset-types) med en etikettegenskap, vi refererar till dem som märkta datauppsättningar. Dessa specifika typer av TabularDatasets skapas endast som en utdata från Azure Machine Learning datamärkningsprojekt. Skapa ett datamärkningsprojekt med [de här stegen](how-to-create-labeling-projects.md). Machine Learning stöder datamärkningsprojekt för bildklassificering, antingen multietikett eller multiklass, och objektidentifiering tillsammans med avgränsade rutor.

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt konto](https://aka.ms/AMLFree) innan du börjar.
* [Azure Machine Learning SDK för Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)eller åtkomst till Azure Machine Learning [Studio](https://ml.azure.com/).
    * Installera [azure-contrib-dataset-paketet](https://docs.microsoft.com/python/api/azureml-contrib-dataset/?view=azure-ml-py)
* En arbetsyta för maskininlärning. Se [Skapa en Azure Machine Learning-arbetsyta](how-to-manage-workspace.md).
* Åtkomst till ett Azure Machine Learning-datamärkningsprojekt. Om du inte har ett etikettprojekt skapar du ett med [de här stegen](how-to-create-labeling-projects.md).

## <a name="export-data-labels"></a>Exportera dataetiketter 

När du har slutfört ett datamärkningsprojekt kan du exportera etikettdata från ett märkningsprojekt. På så sätt kan du samla in både referensen till data och dess etiketter och exportera dem i [COCO-format](http://cocodataset.org/#format-data) eller som en Azure Machine Learning-datauppsättning. Använd knappen **Exportera** på sidan **Projektinformation** i ditt etikettprojekt.

### <a name="coco"></a>Coco 

 COCO-filen skapas i standardblob-arkivet för Arbetsytan Azure Machine Learning i en mapp i *export/coco*. 

### <a name="azure-machine-learning-dataset"></a>Azure Machine Learning-datauppsättning

Du kan komma åt den exporterade Azure Machine Learning-datauppsättningen i avsnittet **Datauppsättningar** i din Azure Machine Learning-studio. Sidan Informationsuppsättning **innehåller** också exempelkod för att komma åt dina etiketter från Python.

![Exporterad datauppsättning](./media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="explore-labeled-datasets"></a>Utforska etiketterade datauppsättningar

Ladda dina märkta datauppsättningar i en pandas-dataram eller Torchvision-datauppsättning för att utnyttja populära bibliotek med öppen källkod för datautforskning, samt PyTorch som bibliotek för bildomvandling och utbildning.

### <a name="pandas-dataframe"></a>Pandas dataram

Du kan läsa in etiketterade datauppsättningar i [`to_pandas_dataframe()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) en `azureml-contrib-dataset` pandas-dataram med metoden från klassen. Installera klassen med följande skalkommando: 

```shell
pip install azureml-contrib-dataset
```

>[!NOTE]
>Namnområdet azureml.contrib ändras ofta när vi arbetar för att förbättra tjänsten. Som sådan bör allt i det här namnområdet betraktas som en förhandsgranskning och stöds inte fullt ut av Microsoft.

Vi erbjuder följande filhanteringsalternativ för filströmmar när du konverterar till en pandas dataframe.
* Ladda ned: Ladda ned dina datafiler till en lokal sökväg.
* Montera: Montera dina datafiler till en monteringspunkt. Mount fungerar bara för Linux-baserad beräkning, inklusive azure machine learning notebook VM och Azure Machine Learning Compute.

```Python
import azureml.contrib.dataset
from azureml.contrib.dataset import FileHandlingOption
animal_pd = animal_labels.to_pandas_dataframe(file_handling_option=FileHandlingOption.DOWNLOAD, target_path='./download/', overwrite_download=True)

import matplotlib.pyplot as plt
import matplotlib.image as mpimg

#read images from downloaded path
img = mpimg.imread(animal_pd.loc[0,'image_url'])
imgplot = plt.imshow(img)
```

### <a name="torchvision-datasets"></a>Torchvision-datauppsättningar

Du kan läsa in etiketterade datauppsättningar i Torchvision-datauppsättningen med `azureml-contrib-dataset` metoden [to_torchvision()](https://docs.microsoft.com/python/api/azureml-contrib-dataset/azureml.contrib.dataset.tabulardataset?view=azure-ml-py#to-torchvision--) även från klassen. Om du vill använda den här metoden måste du ha [PyTorch](https://pytorch.org/) installerat. 

```python
from torchvision.transforms import functional as F

# load animal_labels dataset into torchvision dataset
pytorch_dataset = animal_labels.to_torchvision()
img = pytorch_dataset[0][0]
print(type(img))

# use methods from torchvision to transform the img into grayscale
pil_image = F.to_pil_image(img)
gray_image = F.to_grayscale(pil_image, num_output_channels=3)

imgplot = plt.imshow(gray_image)
```

## <a name="next-steps"></a>Nästa steg

* Se [datauppsättningen med anteckningsbok för etiketter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/labeled-datasets/labeled-datasets.ipynb) för fullständigt träningsexempel.
