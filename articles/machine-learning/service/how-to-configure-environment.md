---
title: Konfigurera Python-miljön
titleSuffix: Azure Machine Learning service
description: 'Lär dig mer om att konfigurera en utvecklingsmiljö när du arbetar med Azure Machine Learning-tjänsten. I det här dokumentet lär du dig hur du använder Conda-miljöer, skapa konfigurationsfiler och konfigurera Jupyter Notebooks, Azure anteckningsböcker, IDE: er, kod redigerare och den virtuella datorn för datavetenskap.'
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.component: core
ms.reviewer: larryfr
manager: cgronlun
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 59f847dc38cddfd9185cfd169cf1ef34c744f8f7
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53192642"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>Konfigurera en utvecklingsmiljö för Azure Machine Learning

I det här dokumentet lär du dig hur du konfigurerar en utvecklingsmiljö med Azure Machine Learning-tjänsten. Azure Machine Learning-tjänsten är plattformsoberoende. De enda kraven för din utvecklingsmiljö är __Python 3__, __Conda__ (för isolerade miljöer), och en konfigurationsfil som innehåller din information för Azure Machine Learning-arbetsytan.

Det här dokumentet fokuserar på följande specifika miljöer och verktyg:

* [Azure anteckningsböcker](#aznotebooks): En Jupyter notebook-tjänst som finns i Azure-molnet. Det är __den enklaste__ sättet att komma igång, eftersom SDK: N för Azure Machine Learning är redan installerad.
* [Den virtuella datorn för datavetenskap](#dsvm): En __förkonfigurerade utvecklings-/ experimentmiljön__ i Azure-molnet som är __avsedd för data science fungerar__ och kan distribueras till antingen CPU endast instanser av virtuella datorer eller GPU-baserade instanser. Python 3, Conda, Jupyter-anteckningsböcker och SDK: N för Azure Machine Learning har redan installerats. Den virtuella datorn som levereras med populära ML / deep learning ramverk, verktyg och redigerare för utveckling av ML-lösningar. Det beror förmodligen på __de mest fullständiga__ utvecklingsmiljö för ML på Azure-plattformen.
* [Jupyter-anteckningsböcker](#jupyter): Om du redan använder Jupyter-anteckningsböcker, har vissa tillägg som du bör installera SDK: N.
* [Visual Studio Code](#vscode): Om du använder Visual Studio Code, finns det vissa användbara tillägg som du kan installera.
* [Azure Databricks](#aml-databricks): En populära analysplattform som bygger på Apache Spark. Lär dig hur du hämtar Azure Machine Learning-SDK till ditt kluster så att du kan distribuera modeller.

Om du redan har en Python 3-miljö eller bara vill ha de grundläggande stegen för att installera SDK: N, se den [lokal dator](#local) avsnittet.

## <a name="prerequisites"></a>Förutsättningar

- En arbetsyta för Azure Machine Learning-tjänsten. Följ stegen i [Kom igång med Azure Machine Learning-tjänsten](quickstart-get-started.md) att skapa en.

- Antingen den [jämför Anaconda](https://www.anaconda.com/download/) eller [Miniconda](https://conda.io/miniconda.html) Pakethanteraren.

    > [!IMPORTANT]
    > Anaconda och Miniconda krävs inte när du använder Azure-anteckningsböcker.

- På Linux eller Mac OS behöver du bash-gränssnittet.

    > [!TIP]
    > Om du är på Linux eller Mac OS och använder ett gränssnitt än bash (till exempel zsh) kan du få felmeddelanden när du kör vissa kommandon. Undvik problemet genom att använda den `bash` kommando för att starta en ny bash-gränssnitt och kör kommandon det.

- På Windows måste du Kommandotolken eller Anaconda kommandotolk (installerad som Anaconda och Miniconda).

## <a id="anotebooks"></a>Azure-anteckningsböcker

[Azure anteckningsböcker](https://notebooks.azure.com) (förhandsversion) är en interaktiv utvecklingsmiljö i Azure-molnet. Det är __den enklaste__ sättet att komma igång med Azure Machine Learning-utveckling.

* Azure Machine Learning SDK är __redan installerat__.
* När du har skapat en arbetsyta för Azure Machine Learning-tjänsten i Azure-portalen, kan du klicka för att automatiskt konfigurera din Azure-anteckningsbok miljö att arbeta med arbetsytan.

Börja utveckla med Azure-datorer genom att följa den [Kom igång med Azure Machine Learning-tjänsten](quickstart-get-started.md) dokumentet.

## <a id="dsvm"></a>Virtuell dator för datavetenskap

Den virtuella datorn på datavetenskap (DSVM) är en anpassad virtuell dator (VM)-avbildning **avsedd för data science fungerar** som är förkonfigurerad med:

  - Paket som till exempel Tensorflow, Pytorch, scikit-Läs Xgboost och Azure ML-SDK
  - Populära verktyg för datavetenskap som Spark fristående, ökad detaljnivå
  - Azure-verktyg, till exempel CLI, Azcopy och Storage explorer
  - Integrated development Environment (IDE: er) som Visual Studio Code, PyCharm och RStudio
  - Jupyter Notebook-Server 

Azure Machine Learning-SDK fungerar på antingen Ubuntu eller Windows-versionen av DSVM. Om du vill använda den virtuella datorn för datavetenskap som en utvecklingsmiljö, använder du följande steg:

1. Använd någon av följande metoder för att skapa en virtuell dator för datavetenskap:

    * Med Azure Portal:

        * [Skapa en __Ubuntu__ virtuell dator för datavetenskap](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)

        * [Skapa en __Windows__ virtuell dator för datavetenskap](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/provision-vm)

    * Med hjälp av Azure CLI:

        > [!IMPORTANT]
        > När du använder Azure CLI, du måste först logga in på din Azure-prenumeration med hjälp av den `az login` kommando.
        >
        > När du använder kommandona i det här steget, måste du ange ett Resursgruppsnamn, ett namn för den virtuella datorn, ett användarnamn och ett lösenord.

        * Skapa en __Ubuntu__ Data Science Virtual Machine, använder du följande kommando:

            ```azurecli
            # create a Ubuntu DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            # If you need to create a new resource group use: "az group create --name YOUR-RESOURCE-GROUP-NAME --location YOUR-REGION (For example: westus2)"
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:linux-data-science-vm-ubuntu:linuxdsvmubuntu:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --generate-ssh-keys --authentication-type password
            ```

        * Skapa en __Windows__ Data Science Virtual Machine, använder du följande kommando:

            ```azurecli
            # create a Windows Server 2016 DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:dsvm-windows:server-2016:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --authentication-type password
            ```    

2. Azure Machine Learning SDK är **redan installerat** på DSVM. Använd någon av följande kommandon om du vill använda den Conda-miljö som innehåller SDK: N:

    * På __Ubuntu__ DSVM, Använd det här kommandot:

        ```shell
        conda activate py36
        ```

    * På __Windows__ DSVM, Använd det här kommandot:

        ```shell
        conda activate AzureML
        ```

1. Använd följande Python-kod för att kontrollera att du kan använda SDK: N och kontrollera vilken version:

    ```python
    import azureml.core
    print(azureml.core.VERSION)
    ```

1. För att konfigurera DSVM för att använda Azure Machine Learning-tjänsten arbetsytan se den [konfigurera arbetsyta](#workspace) avsnittet.

Läs mer på den virtuella datorer för datavetenskap, [virtuella datorer för datavetenskap](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/).

## <a id="local"></a>Lokal dator

När du använder en lokal dator (som kanske en fjärransluten virtuell dator), Använd följande steg för att skapa en conda-miljö och installera SDK:

1. Öppna en kommandotolk eller shell.

1. Skapa en conda-miljö med följande kommandon:

    ```shell
    # create a new conda environment with Python 3.6, numpy, and cython
    conda create -n myenv Python=3.6 cython numpy

    # activate the conda environment
    conda activate myenv

    # On Mac OS run
    source activate myenv
    ```

    Det kan ta flera minuter att skapa miljön om Python 3.6 och andra komponenter måste hämtas.

1. Installera Azure Machine Learning-SDK med anteckningsboken tillägg och förberedelse av Data-SDK med hjälp av följande kommando:

     ```shell
    pip install --upgrade azureml-sdk[notebooks,automl] azureml-dataprep
    ```

   > [!NOTE]
   > Om du får ett meddelande om att `PyYAML` får inte vara avinstallerats klickar du i stället använder du följande kommando:
   >
   > `pip install --upgrade azureml-sdk[notebooks,automl] azureml-dataprep --ignore-installed PyYAML`

   Det kan ta flera minuter att installera SDK.

1. Installera paket för din machine learning-experimentering. Använd följande kommando och Ersätt `<new package>` till paketet som du vill installera:

    ```shell
    conda install <new package>
    ```

1. Om du vill kontrollera att SDK: N är i installerad, följande Python-kod:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

### <a id="jupyter"></a>Jupyter-anteckningsböcker

Jupyter Notebooks är en del av den [Jupyter projekt](https://jupyter.org/). De ger en interaktiv kodningserfarenheter där du skapar dokument som blandar live-koden med löpande text och grafik. Jupyter Notebooks är också ett bra sätt att dela dina resultat med andra, som du kan spara utdata från din kodavsnitt i dokumentet. Du kan installera Jupyter Notebooks på en rad olika plattformar.

Stegen i den [lokal dator](#local) avsnittet Installera valfria komponenter för Jupyter-anteckningsböcker. Om du vill aktivera de här komponenterna i din miljö för Jupyter-anteckningsbok, använder du följande steg:

1. Öppna en kommandotolk eller shell.

1. Installera en conda-medveten Jupyter Notebook-server med följande kommando:

    ```shell
    # install Jupyter
    conda install nb_conda
    ```

1. Öppna Jupyter-anteckningsbok med följande kommando:

    ```shell
    jupyter notebook
    ```

1. Öppna en ny anteckningsbok för att verifiera att Jupyter-anteckningsbok kan använda SDK: N, och välj ”myenv” som din kernel. Kör sedan följande kommando i en anteckningsbok cell:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. För att konfigurera Jupyter-anteckningsboken för att använda Azure Machine Learning-tjänsten arbetsytan se den [konfigurera arbetsyta](#workspace) avsnittet.

### <a id="vscode"></a>Visual Studio Code

Visual Studio Code är en plattformsoberoende Kodredigerare. Den förlitar sig på en lokal installation av Python 3 och Conda för Python-support, men ger ytterligare verktyg för att arbeta med AI. Den innehåller också stöd för att välja Conda-miljö från Kodredigeraren.

Om du vill använda Visual Studio Code för utveckling, använder du följande steg:

1. Läs hur du använder Visual Studio Code för Python-utveckling i den [Kom igång med Python i VSCode](https://code.visualstudio.com/docs/python/python-tutorial) dokumentet.

1. Välj Conda-miljö genom att öppna VS Code och sedan använda __Ctrl-Shift-P__ (Linux och Windows) eller __kommandot SKIFT P__ (Mac) för att hämta den __kommandot utbud__. Ange __Python: Välj tolk__ och välj sedan den conda-miljön.

1. Skapa en ny Python-fil (.py) som innehåller följande kod för att verifiera att du kan använda SDK: N. Kör filen:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. Om du vill installera Azure Machine Learning-tillägget för Visual Studio Code finns i den [Tools för AI](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai) sidan.

    Mer information finns i [med hjälp av Azure Machine Learning för Visual Studio Code](how-to-vscode-tools.md).

<a name="aml-databricks"></a>

## <a name="azure-databricks"></a>Azure Databricks

Du kan använda en anpassad version av Azure Machine Learning-SDK för Azure Databricks för slutpunkt till slutpunkt anpassade machine learning. Eller, träna din modell i Databricks och Använd [Visual Studio Code](how-to-vscode-train-deploy.md#deploy-your-service-from-vs-code) att distribuera modellen

Förbereda din Databricks-klustret och får exempelanteckningsböcker:

1. Skapa en [Databricks-klustret](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) med en Databricks-körningsversion av 4.x (hög samtidighet önskade) med **Python 3**. 

1. Skapa ett bibliotek för [installera och bifoga](https://docs.databricks.com/user-guide/libraries.html#create-a-library) Azure Machine Learning-SDK för Python `azureml-sdk[databricks]` PyPi paket till ditt kluster. När du är klar visas i biblioteket som ansluten som visas i den här avbildningen. Tänk på dessa [vanliga problem med Databricks](resource-known-issues.md#databricks).

   ![SDK är installerat på Databricks ](./media/how-to-azure-machine-learning-on-databricks/sdk-installed-on-databricks.jpg)

   Om det här steget misslyckas, startar du om klustret:
   1. Välj `Clusters`i den vänstra rutan. Välj klusternamnet i tabellen. 
   1. På den `Libraries` fliken `Restart`.

1. Ladda ned den [Azure Databricks / Azure Machine Learning SDK notebook arkivera filen](https://github.com/Azure/MachineLearningNotebooks/blob/master/databricks/Databricks_AMLSDK_github.dbc).

   >[!Warning]
   > Många exempelanteckningsböcker som är tillgängliga för användning med Azure Machine Learning-tjänsten. Endast dessa exempelanteckningsböcker som fungerar med Azure Databricks: https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks

1.  [Importera den här arkivfilen](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-an-archive) till din Databricks-klustret och börja utforska som [som beskrivs här](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks).


## <a id="workspace"></a>Skapa en konfigurationsfil för arbetsyta

Konfigurationsfilen för arbetsytan är ett JSON-dokument som talar om för SDK kommunicera med din arbetsyta för Azure Machine Learning-tjänsten. Filen heter `config.json` och har följande format:

```json
{
    "subscription_id": "<subscription-id>",
    "resource_group": "<resource-group>",
    "workspace_name": "<workspace-name>"
}
```

Den här filen måste vara i mappstrukturen som innehåller ett Python-skript- eller Jupyter-anteckningsböcker. Det kan antingen vara i samma katalog, en undermapp som heter `aml_config`, eller i en överordnad katalog.

Om du vill använda den här filen från din kod använder `ws=Workspace.from_config()`. Den här koden läser in informationen från filen och ansluter till din arbetsyta.

Det finns tre sätt att skapa konfigurationsfilen:

* Om du följer den [Snabbstart för Azure Machine Learning](quickstart-get-started.md), ett `config.json` fil skapas i biblioteket Azure anteckningsböcker. Den här filen innehåller konfigurationsinformation för arbetsytan. Du kan ladda ned eller kopiera detta `config.json` till andra utvecklingsmiljöer.

* Du kan **manuellt skapa filen** med hjälp av en textredigerare. Du kan hitta värdena för som ingår i konfigurationsfilen genom att gå till din arbetsyta i den [Azure-portalen](https://portal.azure.com). Kopiera den __Arbetsytenamn__, __resursgrupp__, och __prenumerations-ID__ standardvärden och använda dem i konfigurationsfilen.
        ![Azure Portal](./media/how-to-configure-environment/configure.png)

* Du kan **programmässigt skapa filen**. Följande kodfragment visar hur du ansluter till en arbetsyta genom att ange prenumerations-ID, resursgrupp och namn på arbetsyta. Det sparar arbetsytans konfiguration i filen:

    ```python
    from azureml.core import Workspace

    subscription_id = '<subscription-id>'
    resource_group  = '<resource-group>'
    workspace_name  = '<workspace-name>'

    try:
        ws = Workspace(subscription_id = subscription_id, resource_group = resource_group, workspace_name = workspace_name)
        ws.write_config()
        print('Library configuration succeeded')
    except:
        print('Workspace not found')
    ```

    Den här koden skriver konfigurationsfilen för att den `aml_config/config.json` filen.

## <a name="next-steps"></a>Nästa steg

- [Träna en modell i Azure Machine Learning med MNIST-datauppsättning](tutorial-train-models-with-aml.md)
- [Azure Machine Learning-SDK för Python](https://aka.ms/aml-sdk)
- [Azure Machine Learning-SDK för dataförberedelse](https://aka.ms/data-prep-sdk)
