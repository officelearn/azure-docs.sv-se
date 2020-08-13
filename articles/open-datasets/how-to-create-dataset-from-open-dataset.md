---
title: Skapa data uppsättningar med Azure Open-datauppsättningar
titleSuffix: Azure Open Datasets
description: Lär dig hur du skapar en Azure Machine Learning data uppsättning från Azure Open DataSet.
ms.service: open-datasets
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.date: 08/05/2020
ms.custom: how-to, tracking-python
ms.openlocfilehash: e6f3a541f1e9dbca2c9949fb0c5cde28cd43e8e5
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2020
ms.locfileid: "88183070"
---
# <a name="create-azure-machine-learning-datasets-from-azure-open-datasets"></a>Skapa Azure Machine Learning data uppsättningar från Azure Open data uppsättningar
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här artikeln får du lära dig hur du skapar en Azure Machine Learning data uppsättning från [Azure Open data uppsättningar](https://azure.microsoft.com/services/open-datasets/) för att komma åt data för lokala eller fjärranslutna experiment. 

Genom att skapa en [Azure Machine Learning data uppsättning](../machine-learning/how-to-create-register-datasets.md)skapar du en referens till data källans plats, tillsammans med en kopia av dess metadata. Eftersom data behålls på den befintliga platsen debiteras du ingen extra lagrings kostnad och riskerar inte att oavsiktligt ändra dina ursprungliga data källor. Data uppsättningar är också Lazy-utvärderade, vilket bidrar till prestanda hastigheter i arbets flödet.
 
Information om var data uppsättningar passar i Azure Machine Learning programmets övergripande data åtkomst arbets flöde finns i artikeln [säker åtkomst till data](../machine-learning/concept-data.md#data-workflow) .


Öppna data uppsättningar finns i molnet på Microsoft Azure och ingår i både [Azure Machine Learning python SDK](#create-datasets-with-the-sdk) och [Azure Machine Learning Studio](#create-datasets-with-the-studio).

## <a name="why-use-azure-open-datasets"></a>Varför ska jag använda Azure Open data uppsättningar? 

Azure Open-datauppsättningar är granskade offentliga data uppsättningar som du kan använda för att lägga till scenario-/regionsspecifika funktioner till maskin inlärnings lösningar för mer exakta modeller. Data uppsättningar omfattar data från offentliga domäner för väder, räkning, helger, offentlig säkerhet och plats som hjälper dig att träna maskin inlärnings modeller och utöka förutsägelse lösningar. 

Mer information finns i [Vad är öppna data uppsättningar?](overview-what-are-open-datasets.md)

## <a name="prerequisites"></a>Krav

Om du vill skapa och arbeta med data uppsättningar behöver du:

* En Azure-prenumeration. Om du inte har ett konto kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree).

* En [Azure Machine Learning-arbetsyta](../machine-learning/how-to-manage-workspace.md).

* [Azure Machine Learning SDK för python installerat](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), som innehåller paketet azureml-DataSets.

    * Skapa en [Azure Machine Learning beräknings instans](../machine-learning/concept-compute-instance.md#managing-a-compute-instance), som är en helt konfigurerad och hanterad utvecklings miljö som innehåller integrerade antecknings böcker och SDK redan har installerats.

    **ELLER**

    * Arbeta med din egen Jupyter-anteckningsbok och installera SDK: n själv med [de här anvisningarna](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

> [!NOTE]
> Vissa data uppsättnings klasser är beroende av [azureml-nu-](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) paketet, som endast är kompatibelt med 64-bitars python. För Linux-användare stöds dessa klasser endast för följande distributioner: Red Hat Enterprise Linux (7, 8), Ubuntu (14,04, 16,04, 18,04), Fedora (27, 28), Debian (8, 9) och CentOS (7).

## <a name="create-datasets-with-the-sdk"></a>Skapa data uppsättningar med SDK

Kontrol lera att du har installerat paketet med om du vill skapa data uppsättningar via öppna data uppsättnings klasser i Azure Machine Learning python SDK `pip install azureml-opendatasets` . Varje diskret data uppsättning representeras av sin egen klass i SDK och vissa klasser är tillgängliga som antingen en `TabularDataset` , `FileDataset` eller båda. En fullständig lista över klasser finns i [referens dokumentationen](https://docs.microsoft.com/python/api/azureml-opendatasets/azureml.opendatasets?view=azure-ml-py) .

Du kan hämta vissa klasser som antingen en `TabularDataset` eller `FileDataset` , vilket gör att du kan manipulera och/eller ladda ned filerna direkt. Andra klasser kan **bara** hämta en data uppsättning med hjälp av en `get_tabular_dataset()` eller- `get_file_dataset()` funktion. I följande kod exempel visas några exempel på dessa typer av klasser.

```python
from azureml.opendatasets import MNIST

# MNIST class can return either TabularDataset or FileDataset
tabular_dataset = MNIST.get_tabular_dataset()
file_dataset = MNIST.get_file_dataset()

from azureml.opendatasets import Diabetes

# Diabetes class can return ONLY TabularDataset and must be called from the static function
diabetes_tabular = Diabetes.get_tabular_dataset()
```

När du registrerar en data uppsättning som skapats från öppna data uppsättningar hämtas inga data direkt, men data kommer att kommas åt senare när de begärs (under utbildning, till exempel) från en central lagrings plats.

## <a name="create-datasets-with-the-studio"></a>Skapa data uppsättningar med Studio

Du kan också skapa data uppsättningar från öppna data uppsättningar med [Azure Machine Learning Studio](https://ml.azure.com).

1. I arbets ytan väljer du fliken **data uppsättningar** under **till gångar**. Välj **från öppna data uppsättningar**på list menyn **skapa data uppsättning** .

    ![Öppna data uppsättning med användar gränssnittet](./media/how-to-create-dataset-from-open-dataset/open-datasets-1.png)

1. Välj en data uppsättning genom att välja dess panel. (Du kan välja att filtrera med hjälp av Sök fältet.) Välj **Nästa**.

    ![Välj data uppsättning](./media/how-to-create-dataset-from-open-dataset/open-datasets-2.png)

1. Välj ett namn under vilket du vill registrera data uppsättningen och filtrera sedan data genom att använda de tillgängliga filtren. I det här fallet filtrerar du tids perioden till ett år och lands koden för den **offentliga helgdags** data uppsättningen till endast USA. Välj **Skapa**.

    ![Ange data uppsättnings parametrar och skapa data uppsättning](./media/how-to-create-dataset-from-open-dataset/open-datasets-3.png)

    Data uppsättningen är nu tillgänglig i din arbets yta under **data uppsättningar**. Du kan använda den på samma sätt som andra data uppsättningar som du har skapat.


## <a name="access-datasets-for-your-experiments"></a>Få åtkomst till data uppsättningar för dina experiment

Använd dina data uppsättningar i Machine Learning-experiment för att träna ML-modeller. [Lär dig mer om hur du tränar med data uppsättningar](../machine-learning/how-to-train-with-datasets.md).

## <a name="example-notebooks"></a>Exempelnotebook-filer

Exempel och demonstrationer av funktioner för öppen data uppsättningar finns i dessa [antecknings böcker](https://github.com/Azure/OpenDatasetsNotebooks/tree/master/tutorials).

## <a name="next-steps"></a>Nästa steg

* [Träna en modell med data uppsättningar](../machine-learning/how-to-train-with-datasets.md).

* [Skapa en Azure Machine Learning-datauppsättning](../machine-learning/how-to-create-register-datasets.md).



