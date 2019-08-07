---
title: Skapa en arbetsyta
titleSuffix: Azure Machine Learning service
description: Använd Azure Portal, SDK, en mall eller CLI för att skapa Azure Machine Learning service-arbetsytan. Den här arbets ytan är en central plats där du kan arbeta med alla artefakter som du skapar när du använder Azure Machine Learning-tjänsten.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.author: sgilley
author: sdgilley
ms.date: 05/21/2019
ms.openlocfilehash: 4d689b51a53a27a0e85a52724752d959c4c2506d
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2019
ms.locfileid: "68775051"
---
# <a name="create-an-azure-machine-learning-service-workspace"></a>Skapa en Azure Machine Learning service-arbetsyta

Om du vill använda Azure Machine Learning tjänst behöver du en [**Azure Machine Learning service-arbetsyta**](concept-workspace.md).  Den här arbets ytan är resursen på den översta nivån för tjänsten och ger dig en central plats för att arbeta med alla artefakter som du skapar. 

I den här artikeln får du lära dig hur du skapar en arbets yta med någon av följande metoder: 
* [Azure Portal](#portal) -gränssnittet
* [Azure Machine Learning SDK för python](#sdk)
* En Azure Resource Manager mall
* [Azure Machine Learning CLI](#cli)

Arbets ytan du skapar med hjälp av stegen här – i kan användas som en förutsättning för andra själv studie kurser och instruktions artiklar.

Om du vill använda ett skript för att konfigurera Automatisk maskin inlärning i en lokal python-miljö, se [Azure/MachineLearningNotebooks-GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) för instruktioner.  

När du skapar en arbets yta läggs följande Azure-resurser till automatiskt (om de är tillgängliga i båda regionala):
 
- [Azure Container Registry](https://azure.microsoft.com/services/container-registry/): För att minimera kostnaderna är ACR en **Lazy-inläst** tills distributions avbildningar skapas.
- [Azure Storage](https://azure.microsoft.com/services/storage/)
- [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) 
- [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)

>[!Note]
>Precis som med andra Azure-tjänster finns det vissa begränsningar och kvoter som är associerade med Machine Learning. [Läs mer om kvoter och hur du begär mer.](how-to-manage-quotas.md)


## <a name="prerequisites"></a>Förutsättningar
Du behöver en Azure-prenumeration för att skapa en arbetsyta. Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnadsfria versionen eller betalversionen av Azure Machine Learning-tjänsten](https://aka.ms/AMLFree) i dag.

## <a name="portal"></a>Azure Portal

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

Oavsett hur det skapades kan du Visa din arbets yta i [Azure Portal](https://portal.azure.com/).  Se [Visa en arbets yta](how-to-manage-workspace.md#view) för mer information.

## <a name="sdk"></a> Python SDK

Skapa din arbets yta med python SDK. Först måste du installera SDK.

> [!IMPORTANT]
> Hoppa över installationen av SDK om du använder en Azure-Data Science Virtual Machine eller Azure Databricks.
> * Azure Data Science Virtual Machines som skapats efter det den 27 september 2018 levereras förinstallerade med Python-SDK:n. Hoppa över installationen och börja med [skapa en arbets yta med SDK](#sdk-create).
> * I Azure Databricks-miljön följer du [Databricks-installationsstegen](how-to-configure-environment.md#azure-databricks) i stället.

>[!NOTE]
> Använd de här anvisningarna för att installera och använda SDK från den lokala datorn. Om du vill använda Jupyter på en virtuell dator för virtuella datorer konfigurerar du en fjärr skrivbord-eller X-terminalserversession.

Innan du installerar SDK:n rekommenderar vi att du skapar en isolerad Python-miljö. Även om den här artikeln använder [Miniconda](https://docs.conda.io/en/latest/miniconda.html) kan du även använda fullständiga [Anaconda](https://www.anaconda.com/) installerat eller [Python virtualenv](https://virtualenv.pypa.io/en/stable/).

Anvisningarna i den här artikeln installerar alla paket som du behöver för att köra snabb starten och själv studie antecknings böckerna.  Andra exempelanteckningsböcker kan kräva installation av ytterligare komponenter.  Mer information om dessa komponenter finns i avsnittet om att [installera Azure Machine Learning SDK för Python](https://docs.microsoft.com/python/api/overview/azure/ml/install).

### <a name="install-miniconda"></a>Installera Miniconda

[Ladda ned och installera Miniconda](https://docs.conda.io/en/latest/miniconda.html). Välj att installera versionen Python 3.7. Välj inte versionen Python 2.x.  

### <a name="create-an-isolated-python-environment"></a>Skapa en isolerad Python-miljö

1. Öppna Anaconda-prompt och skapa sedan en ny Conda-miljö med namnet *myenv* och installera python 3.6.5. Azure Machine Learning-SDK:n fungerar med Python 3.5.2 eller senare, men de automatiserade maskininlärningskomponenterna är inte fullständigt funktionella på Python 3.7.  Det tar flera minuter att skapa miljön medan komponenter och paket laddas ned. 

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
> I vissa kommando rads verktyg kan du behöva lägga till citat tecken enligt följande:
> *  'azureml-sdk[notebooks]'
> * 'azureml-sdk[automl]'
>

### <a name='sdk-create'></a>Skapa en arbets yta med SDK

Skapa din arbetsyta i en Jupyter Notebook med hjälp av Python-SDK.

1. Skapa och/eller cd till katalogen som du vill använda för snabbstart och självstudier.

1. Du startar Jupyter Notebook genom att ange följande kommando:

    ```shell
    jupyter notebook
    ```

1. I webbläsarfönstret skapar du en ny notebook med hjälp av `Python 3`-standardkernel. 

1. Visa SDK-versionen genom att ange och sedan köra följande Python-kod i en notebook-cell:

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=import)]

1. Hitta ett värde för `<azure-subscription-id>`-parametern i [prenumerationslistan i Azure Portal](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Använda valfri prenumeration där din roll är ägare eller deltagare. Mer information om roller finns i [Hantera åtkomst till en artikel i Azure Machine Learning](how-to-assign-roles.md) -arbetsytan.

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

Spara informationen om arbetsytan i en konfigurationsfil i den aktuella katalogen. Den här filen kallas *. azureml/config. JSON*.  

Den här konfigurationsfilen för arbetsyta gör det enkelt att läsa in samma arbetsyta senare. Du kan läsa in den med andra antecknings böcker och skript i samma katalog eller i en under katalog `ws=Workspace.from_config()` med hjälp av koden. 

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=writeConfig)]

API-anropet `write_config()` skapar konfigurationsfilen i den aktuella katalogen. Filen *. azureml/config. JSON* innehåller följande:

```json
{
    "subscription_id": "<azure-subscription-id>",
    "resource_group": "myresourcegroup",
    "workspace_name": "myworkspace"
}
```

> [!TIP]
> Om du vill använda arbets ytan i Python-skript eller Jupyter antecknings böcker som finns i andra kataloger kopierar du filen till den katalogen. Filen kan finnas i samma katalog, i en under katalog med namnet *. azureml*eller i en överordnad katalog.

## <a name="resource-manager-template"></a>Resource Manager-mall

Information om hur du skapar en arbets yta med en mall finns i [skapa en Azure Machine Learning service-arbetsyta med hjälp av en mall](how-to-create-workspace-template.md)

<a name="cli"></a>
## <a name="command-line-interface"></a>Kommando rads gränssnitt

Information om hur du skapar en arbets yta med CLI finns i [använda CLI-tillägget för Azure Machine Learning-tjänsten](reference-azure-machine-learning-cli.md).

## <a name="clean-up-resources"></a>Rensa resurser 

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

* Oavsett hur det skapades kan du Visa din arbets yta i [Azure Portal](https://portal.azure.com/).  Se [Visa en arbets yta](how-to-manage-workspace.md#view) för mer information.

* Prova din arbets yta med de här självstudierna.

    * Själv studie kurs i två delar: [Konfigurera miljön och arbets ytan](tutorial-1st-experiment-sdk-setup.md) och [träna din första modell](tutorial-1st-experiment-sdk-train.md).
    * Själv studie kurs i två delar: [Träna](tutorial-train-models-with-aml.md) och [distribuera](tutorial-deploy-models-with-aml.md) ett bild klassificerings läge.
    * Själv studie kurs i två delar: [Förbered data](tutorial-data-prep.md) och [Använd automatisk maskin inlärning](tutorial-auto-train-models.md) för att skapa en Regressions modell.

* Läs mer om hur du [konfigurerar en utvecklings miljö](how-to-configure-environment.md).

* Läs mer om [Azure Machine Learning SDK för python](https://aka.ms/aml-sdk).
