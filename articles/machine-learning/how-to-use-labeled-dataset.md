---
title: Skapa och utforska data uppsättningar med etiketter
titleSuffix: Azure Machine Learning
description: Lär dig hur du exporterar data etiketter från Azure Machine Learning att märka projekt och använda dem för Machine Learning-uppgifter.
author: nibaccam
ms.author: nibaccam
ms.service: machine-learning
ms.topic: how-to
ms.date: 01/21/2020
ms.openlocfilehash: 5138109de3f80d405ce95b605714b511480563f5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "76549494"
---
# <a name="create-and-explore-azure-machine-learning-dataset-with-labels"></a>Skapa och utforska Azure Machine Learning data uppsättning med etiketter

I den här artikeln får du lära dig hur du exporterar data etiketter från ett Azure Machine Learning data märknings projekt och läser in dem i populära format, till exempel en Pandas-dataframe för data utforskning eller en Torchvision-datauppsättning för avbildnings omvandling. 

## <a name="what-are-datasets-with-labels"></a>Vad är data uppsättningar med etiketter 

Azure Machine Learning data uppsättningar med etiketter är [TabularDatasets](how-to-create-register-datasets.md#dataset-types) med en etikett egenskap, kommer vi att referera till dem som etiketterade data uppsättningar. De här typerna av TabularDatasets skapas bara som utdata från projekt med Azure Machine Learning data etiketter. Skapa ett projekt med data etiketter med [de här stegen](how-to-create-labeling-projects.md). Machine Learning stöder data märknings projekt för bild klassificering, antingen flera etiketter eller flera klasser och objekt identifiering tillsammans med gränser rutor.

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://aka.ms/AMLFree) innan du börjar.
* [Azure Machine Learning SDK för python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)eller åtkomst till [Azure Machine Learning Studio](https://ml.azure.com/).
    * Installera [Azure-contrib-dataset-](https://docs.microsoft.com/python/api/azureml-contrib-dataset/?view=azure-ml-py) paketet
* En Machine Learning-arbetsyta. Se [skapa en Azure Machine Learning-arbetsyta](how-to-manage-workspace.md).
* Åtkomst till ett projekt med Azure Machine Learning data etiketter. Om du inte har ett etikett projekt kan du skapa ett med [de här stegen](how-to-create-labeling-projects.md).

## <a name="export-data-labels"></a>Exportera data etiketter 

När du har slutfört ett data etiketts projekt kan du exportera etikett data från ett etikett projekt. På så sätt kan du samla både referensen till data och dess etiketter och exportera dem i [Coco-format](http://cocodataset.org/#format-data) eller som en Azure Machine Learning data uppsättning. Använd knappen **Exportera** på sidan **projekt information** i ditt projekt med etiketter.

### <a name="coco"></a>COCO 

 COCO-filen skapas i standard-BLOB-arkivet för Azure Machine Learning arbets ytan i en mapp i *export-/Coco*. 

### <a name="azure-machine-learning-dataset"></a>Azure Machine Learning data uppsättning

Du kan komma åt den exporterade Azure Machine Learning data uppsättningen i avsnittet **data uppsättningar** i Azure Machine Learning Studio. På sidan data uppsättnings **information** finns också exempel kod för att få åtkomst till dina etiketter från python.

![Exporterad data uppsättning](./media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="explore-labeled-datasets"></a>Utforska etiketterade data uppsättningar

Läs in dina märkta data uppsättningar i en Pandas dataframe-eller Torchvision-datauppsättning för att dra nytta av populära bibliotek med öppen källkod för data utforskning, samt PyTorch tillhandahållna bibliotek för avbildnings omvandling och utbildning.

### <a name="pandas-dataframe"></a>Pandas dataframe

Du kan läsa in märkta data uppsättningar i en Pandas-dataframe med [`to_pandas_dataframe()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) metoden från- `azureml-contrib-dataset` klassen. Installera-klassen med följande Shell-kommando: 

```shell
pip install azureml-contrib-dataset
```

>[!NOTE]
>Namn rymden azureml. contrib ändras ofta, medan vi arbetar för att förbättra tjänsten. Därför bör allt i det här namn området betraktas som en för hands version och stöds inte fullt ut av Microsoft.

Vi erbjuder följande fil hanterings alternativ för fil strömmar vid konvertering till en Pandas-dataframe.
* Hämta: Ladda ned dina datafiler till en lokal sökväg.
* Montera: montera dina datafiler till en monterings punkt. Monteringen fungerar bara för Linux-baserad beräkning, inklusive Azure Machine Learning VM för bärbara datorer och Azure Machine Learning Compute.

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

### <a name="torchvision-datasets"></a>Torchvision data uppsättningar

Du kan läsa in märkta data uppsättningar i Torchvision data uppsättning med metoden [to_torchvision ()](https://docs.microsoft.com/python/api/azureml-contrib-dataset/azureml.contrib.dataset.tabulardataset?view=azure-ml-py#to-torchvision--) även från `azureml-contrib-dataset` klassen. Om du vill använda den här metoden måste du ha [PyTorch](https://pytorch.org/) installerat. 

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

* Se [data uppsättningen with Labels Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/labeled-datasets/labeled-datasets.ipynb) for Complete Training-exemplet.
