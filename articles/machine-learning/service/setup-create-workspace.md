---
title: Skapa en arbetsyta
titleSuffix: Azure Machine Learning service
description: 'Använd Azure-portalen, SDK: N, en mall eller CLI för att skapa din arbetsyta för Azure Machine Learning-tjänsten. Den här arbetsytan innehåller en centraliserad plats för att arbeta med alla artefakter som du skapar när du använder Azure Machine Learning-tjänsten.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.author: sgilley
author: sdgilley
ms.date: 04/19/2019
ms.openlocfilehash: ca43a6cff6a32a30d93e42f6a6624439b2fe83a7
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2019
ms.locfileid: "63766797"
---
# <a name="create-an-azure-machine-learning-service-workspace"></a>Skapa en arbetsyta för Azure Machine Learning-tjänsten

Med Azure Machine Learning-tjänsten måste du ha en [ **Azure Machine Learning-tjänstens arbetsyta**](concept-azure-machine-learning-architecture.md#workspace).  Den här arbetsytan är den översta resursen för tjänsten och ger dig en centraliserad plats för att arbeta med alla artefakter som du skapar. 

I den här artikeln får du lära dig hur du skapar en arbetsyta med hjälp av någon av följande metoder: 
* Den [Azure-portalen](#portal) gränssnitt
* Den [Azure Machine Learning-SDK för Python](#sdk)
* En Azure Resource Manager-mall
* Den [Azure Machine Learning CLI](#cli)

Arbetsytan som du skapar med hjälp av stegen här i kan användas som en förutsättning för att andra självstudier och instruktionsartiklar.

Om du vill använda ett skript för att konfigurera automatiserade machine learning i en lokal Python-miljö finns i den [Azure/MachineLearningNotebooks GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) anvisningar.  

När du skapar en arbetsyta läggs följande Azure-resurser automatiskt (om de är regionalt tillgängliga):
 
- [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
- [Azure Storage](https://azure.microsoft.com/services/storage/)
- [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) 
- [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)

>[!Note]
>Precis som med andra Azure-tjänster finns det vissa begränsningar och kvoter som är associerade med Machine Learning. [Läs mer om kvoter och hur du begär mer.](how-to-manage-quotas.md)


## <a name="prerequisites"></a>Nödvändiga komponenter
Du behöver en Azure-prenumeration för att skapa en arbetsyta. Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnadsfria versionen eller betalversionen av Azure Machine Learning-tjänsten](https://aka.ms/AMLFree) i dag.

## <a name="portal"></a> Azure-portalen

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

Oavsett hur den skapades, du kan visa din arbetsyta på den [Azure-portalen](https://portal.azure.com/).  Se [visa en arbetsyta](how-to-manage-workspace.md#view) mer information.

## <a name="sdk"></a> Python SDK

Skapa din arbetsyta med hjälp av Python-SDK. Du måste först installera SDK: N.

> [!IMPORTANT]
> Om du använder en Azure Data Science Virtual Machine eller Azure Databricks kan du hoppa över installationen av SDK: N.
> * Azure Data Science Virtual Machines som skapats efter det den 27 september 2018 levereras förinstallerade med Python-SDK:n. Hoppa över installationen och börja med [skapar en arbetsyta med SDK](#sdk-create).
> * I Azure Databricks-miljön följer du [Databricks-installationsstegen](how-to-configure-environment.md#azure-databricks) i stället.

>[!NOTE]
> Följ dessa instruktioner för att installera och använda SDK: N från din lokala dator. Om du vill använda Jupyter på en fjärransluten virtuell dator, konfigurera en fjärransluten dator eller X terminalsession.

Innan du installerar SDK:n rekommenderar vi att du skapar en isolerad Python-miljö. Även om den här artikeln använder [Miniconda](https://docs.conda.io/en/latest/miniconda.html) kan du även använda fullständiga [Anaconda](https://www.anaconda.com/) installerat eller [Python virtualenv](https://virtualenv.pypa.io/en/stable/).

Anvisningarna i den här artikeln kommer att installera de paket som du behöver köra anteckningsböcker för Snabbstart och självstudier.  Andra exempelanteckningsböcker kan kräva installation av ytterligare komponenter.  Mer information om dessa komponenter finns i avsnittet om att [installera Azure Machine Learning SDK för Python](https://docs.microsoft.com/python/api/overview/azure/ml/install).

### <a name="install-miniconda"></a>Installera Miniconda

[Ladda ned och installera Miniconda](https://docs.conda.io/en/latest/miniconda.html). Välj att installera versionen Python 3.7. Välj inte versionen Python 2.x.  

### <a name="create-an-isolated-python-environment"></a>Skapa en isolerad Python-miljö

1. Öppna Anaconda fråga och sedan skapa en ny conda-miljö med namnet *myenv* och installera Python 3.6.5. Azure Machine Learning-SDK:n fungerar med Python 3.5.2 eller senare, men de automatiserade maskininlärningskomponenterna är inte fullständigt funktionella på Python 3.7.  Det tar flera minuter att skapa miljön medan komponenter och paket laddas ned. 

    ```shell
    conda create -n myenv python=3.6.5
    ```

1. Aktivera miljön.

    ```shell
    conda activate myenv
    ```

1. Aktivera miljöspecifika ipython-kernels:

    ```shell
    conda install notebook ipykernel
    ```

    Skapa sedan kernel:

    ```shell
    ipython kernel install --user
    ```

### <a name="install-the-sdk"></a>Installera SDK:n

1. Installera kärnkomponenterna för Machine Learning-SDK:n med Jupyter Notebook-funktioner i den aktiverade conda-miljön. Installationen tar några minuter att slutföra beroende på datorns konfiguration.

    ```shell
    pip install --upgrade azureml-sdk[notebooks]
    ```

1. Om du vill använda den här miljön för Azure Machine Learning- självstudier installerar du dessa paket.

    ```shell
    conda install -y cython matplotlib pandas
    ```

1. Om du vill använda den här miljön för Azure Machine Learning- självstudier installerar du de automatiserade maskininlärningskomponenterna.

    ```shell
    pip install --upgrade azureml-sdk[automl]
    ```

> [!IMPORTANT]
> I vissa kommandoradsverktyg, kan du behöva lägga till citattecken enligt följande:
> *  'azureml-sdk[notebooks]'
> * 'azureml-sdk[automl]'
>

### <a name='sdk-create'></a> Skapa en arbetsyta med SDK

Skapa din arbetsyta i en Jupyter Notebook med hjälp av Python-SDK.

1. Skapa och/eller cd till katalogen som du vill använda för snabbstart och självstudier.

1. Du startar Jupyter Notebook genom att ange följande kommando:

    ```shell
    jupyter notebook
    ```

1. I webbläsarfönstret skapar du en ny notebook med hjälp av `Python 3`-standardkernel. 

1. Visa SDK-versionen genom att ange och sedan köra följande Python-kod i en notebook-cell:

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=import)]

1. Hitta ett värde för `<azure-subscription-id>`-parametern i [prenumerationslistan i Azure Portal](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Använda valfri prenumeration där din roll är ägare eller deltagare. Mer information om roller finns i [hantera åtkomst till en Azure Machine Learning-arbetsyta](how-to-assign-roles.md) artikeln.

   ```python
   from azureml.core import Workspace
   ws = Workspace.create(name='myworkspace',
                         subscription_id='<azure-subscription-id>', 
                         resource_group='myresourcegroup',
                         create_resource_group=True,
                         location='eastus2' 
                        )
   ```

   När du kör koden kan du bli uppmanad att logga in på ditt Azure-konto. När du har loggat in cachelagras autentiseringstoken lokalt.

1. Om du vill visa information om arbetsytan, t.ex. associerad lagring, containerregister eller nyckelvalv, anger du följande kod:

    [!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=getDetails)]


### <a name="write-a-configuration-file"></a>Skriva en konfigurationsfil

Spara informationen om arbetsytan i en konfigurationsfil i den aktuella katalogen. Den här filen kallas *.azureml/config.json*.  

Den här konfigurationsfilen för arbetsyta gör det enkelt att läsa in samma arbetsyta senare. Du kan läsa in den med andra bärbara datorer och skript i samma katalog eller en underkatalog med hjälp av kod `ws=Workspace.from_config()` . 

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=writeConfig)]

API-anropet `write_config()` skapar konfigurationsfilen i den aktuella katalogen. Filen *config.json* innehåller följande:

```json
{
    "subscription_id": "<azure-subscription-id>",
    "resource_group": "myresourcegroup",
    "workspace_name": "myworkspace"
}
```

> [!TIP]
> Om du vill använda din arbetsyta i Python-skript eller Jupyter-anteckningsböcker som finns i andra kataloger, kopiera den här filen till katalogen. Filen kan vara i samma katalog, en undermapp som heter *.azureml*, eller i en överordnad katalog.

## <a name="resource-manager-template"></a>Resource manager-mall

För att skapa en arbetsyta med en mall, se [skapa en arbetsyta för Azure Machine Learning-tjänsten med hjälp av en mall](how-to-create-workspace-template.md)

<a name="cli"></a>
## <a name="command-line-interface"></a>Kommandoradsgränssnitt

För att skapa en arbetsyta med CLI, se [använder CLI-tillägget för Azure Machine Learning-tjänsten](reference-azure-machine-learning-cli.md).

## <a name="clean-up-resources"></a>Rensa resurser 

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

* Oavsett hur den skapades, du kan visa din arbetsyta på den [Azure-portalen](https://portal.azure.com/).  Se [visa en arbetsyta](how-to-manage-workspace.md#view) mer information.

* Testa din arbetsyta med dessa snabbstarter och självstudier.

    * Snabbstart: [Kör Jupyter notebook i molnet](quickstart-run-cloud-notebook.md).
    * Snabbstart: [Kör Jupyter notebook på din server](quickstart-run-local-notebook.md).
    * Självstudiekurs i två delar: [Träna](tutorial-train-models-with-aml.md) och [distribuera](tutorial-deploy-models-with-aml.md) ett läge för klassificering av avbildning.
    * Självstudiekurs i två delar: [Förbereda data](tutorial-data-prep.md) och [använder automatiska machine learning](tutorial-auto-train-models.md) att skapa en regressionsmodell.

* Mer information om hur du [konfigurera en utvecklingsmiljö](how-to-configure-environment.md).

* Läs mer om den [Azure Machine Learning-SDK för Python](https://aka.ms/aml-sdk).
