---
title: 'Snabbstart: Kom igång med Python'
titleSuffix: Azure Machine Learning service
description: Kom igång med Azure Machine Learning-tjänsten i Python. Använd Python SDK för att skapa en arbetsyta som är själva grunden i det moln som du använder för att experimentera, träna och distribuera maskininlärningsmodeller.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: quickstart
ms.reviewer: sgilley
author: hning86
ms.author: haining
ms.date: 01/22/2019
ms.custom: seodec18
ms.openlocfilehash: 1962cef85c5e663de640f296a6e8e9efd5a1f4d6
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/15/2019
ms.locfileid: "56310363"
---
# <a name="quickstart-use-the-python-sdk-to-get-started-with-azure-machine-learning"></a>Snabbstart: Kom igång med Azure Machine Learning med hjälp av Python-SDK:n

I den här artikeln använder du Azure Machine Learning SDK för Python 3 för att skapa och sedan använda en [arbetsyta](concept-azure-machine-learning-architecture.md) för Azure Machine Learning-tjänsten. Den här arbetsytan är själva grunden i det moln som du använder för att experimentera, träna och distribuera maskininlärningsmodeller med Machine Learning.

Du börjar med att konfigurera din egen Python-miljö och Jupyter Notebook Server. Information om hur du kan köra den utan installation finns i [Snabbstart: Använda Azure-portalen för att komma igång med Azure Machine Learning](quickstart-get-started.md). 

Visa en videoversion av den här snabbstarten:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2G9N6]

I den här snabbstarten kommer du att göra följande:

* Installera Python-SDK:n.
* Skapa en arbetsyta i din Azure-prenumeration.
* Skapa en konfigurationsfil för den arbetsytan för senare användning i andra notebooks och skript.
* Skriva kod som loggar värden i arbetsytan.
* Visa de loggade värdena på din arbetsyta.

Du skapar en arbetsyta och en konfigurationsfil som är nödvändiga komponenter för andra självstudier och instruktionsartiklar om Azure Machine Learning. Precis som med andra Azure-tjänster finns det vissa begränsningar och kvoter som är associerade med Machine Learning. [Läs mer om kvoter och hur du begär mer.](how-to-manage-quotas.md)

Följande Azure-resurser läggs automatiskt till din arbetsyta när de är regionalt tillgängliga:
 
- [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
- [Azure Storage](https://azure.microsoft.com/services/storage/)
- [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) 
- [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)

>[!NOTE]
> Koden i den här artikeln kräver Azure Machine Learning SDK version 1.0.2 eller senare och testades med version 1.0.8.


Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnadsfria eller betalversionen av Azure Machine Learning-tjänsten](http://aka.ms/AMLFree) i dag.

## <a name="install-the-sdk"></a>Installera SDK:n

> [!IMPORTANT]
> Hoppa över det här avsnittet om du använder Azure Data Science Virtual Machine eller Azure Databricks.
> * Azure Data Science Virtual Machines som skapats efter det den 27 september 2018 levereras förinstallerade med Python-SDK:n.
> * I Azure Databricks-miljön följer du [Databricks-installationsstegen](how-to-configure-environment.md#azure-databricks) i stället.

Innan du installerar SDK:n rekommenderar vi att du skapar en isolerad Python-miljö. Även om den här artikeln använder [Miniconda](https://docs.conda.io/en/latest/miniconda.html) kan du även använda fullständiga [Anaconda](https://www.anaconda.com/) installerat eller [Python virtualenv](https://virtualenv.pypa.io/en/stable/).

### <a name="install-miniconda"></a>Installera Miniconda

[Ladda ned och installera Miniconda](https://docs.conda.io/en/latest/miniconda.html). Välj Python 3.7 eller senare för att installera. Välj inte versionen Python 2.x.  

### <a name="create-an-isolated-python-environment"></a>Skapa en isolerad Python-miljö

1. Öppna ett kommandoradsfönster och skapa sedan en ny conda-miljö med namnet *myenv* och installera Python 3.6. Azure Machine Learning-SDK:n fungerar med Python 3.5.2 eller senare, men de automatiserade maskininlärningskomponenterna är inte fullständigt funktionella på Python 3.7.

    ```shell
    conda create -n myenv -y Python=3.6
    ```

1. Aktivera miljön.

    ```shell
    conda activate myenv
    ```

### <a name="install-the-sdk"></a>Installera SDK:n

1. Installera kärnkomponenterna för Machine Learning-SDK:n med Jupyter Notebook-funktioner i den aktiverade conda-miljön.  Installationen tar några minuter att slutföra beroende på datorns konfiguration.

  ```shell
    pip install --upgrade azureml-sdk[notebooks]
    ```

1. Installera en Jupyter Notebook-server i conda-miljön.

  ```shell
    conda install -y nb_conda
    ```

1. Om du vill använda den här miljön för Azure Machine Learning- självstudier installerar du dessa paket.

    ```shell
    conda install -y cython matplotlib pandas
    ```

1. Om du vill använda den här miljön för Azure Machine Learning- självstudier installerar du de automatiserade maskininlärningskomponenterna.

    ```shell
    pip install --upgrade azureml-sdk[automl]
    ```

## <a name="create-a-workspace"></a>Skapa en arbetsyta

Skapa din arbetsyta i en Jupyter Notebook med hjälp av Python-SDK.

1. Skapa och/eller cd till katalogen som du vill använda för snabbstart och självstudier.

1. Du startar Jupyter Notebook genom att ange följande kommando:

    ```shell
    jupyter notebook
    ```

1. I webbläsarfönstret skapar du en ny notebook med hjälp av `Python 3`-standardkernel. 

1. Visa SDK-versionen genom att ange och sedan köra följande Python-kod i en notebook-cell:

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=import)]

1. Hitta ett värde för `<azure-subscription-id>`-parametern i [prenumerationslistan i Azure Portal](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Använda valfri prenumeration där din roll är ägare eller deltagare.

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


## <a name="write-a-configuration-file"></a>Skriva en konfigurationsfil

Spara informationen om arbetsytan i en konfigurationsfil i den aktuella katalogen. Den här filen kallas *aml_config\config.json*.  

Den här konfigurationsfilen för arbetsyta gör det enkelt att läsa in samma arbetsyta senare. Du kan läsa in den med andra notebooks och skript i samma katalog eller en underkatalog.  

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=writeConfig)]

API-anropet `write_config()` skapar konfigurationsfilen i den aktuella katalogen. Filen *config.json* innehåller följande:

```json
{
    "subscription_id": "<azure-subscription-id>",
    "resource_group": "myresourcegroup",
    "workspace_name": "myworkspace"
}
```

## <a name="use-the-workspace"></a>Använda arbetsytan

Kör en del kod som använder de grundläggande API:erna i SDK:n för att spåra experimentkörningar:

1. Skapa ett experiment på arbetsytan.
1. Logga ett värde i experimentet.
1. Logga en lista över värden i experimentet.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=useWs)]

## <a name="view-logged-results"></a>Visa loggade resultat
När körningen är klar kan du visa experimentkörningen på Azure-portalen. Om du vill skriva ut en URL som navigerar till resultatet för den senaste körningen använder du följande kod:

```python
print(run.get_portal_url())
```

Använd länken för att visa de loggade värdena på Azure-portalen i webbläsaren.

![Loggade värden i Azure Portal](./media/quickstart-create-workspace-with-python/logged-values.png)

## <a name="clean-up-resources"></a>Rensa resurser 
>[!IMPORTANT]
>Du kan använda resurserna som du har skapat här som förhandskrav för andra självstudier och instruktionsartiklar om Machine Learning.

Om du inte planerar att använda de resurser som du skapade i den här artikeln kan du ta bort dem så att du undviker eventuella kostnader.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=delete)]

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig att skapa de resurser som du behöver för att experimentera med och distribuera modeller. Du har kört kod i en notebook och du har utforskat körningshistoriken eller koden på arbetsytan i molnet.

> [!div class="nextstepaction"]
> [Självstudier: Träna en bildklassificeringsmodell](tutorial-train-models-with-aml.md)

Du kan även utforska [mer avancerade exempel på GitHub](https://aka.ms/aml-notebooks).
