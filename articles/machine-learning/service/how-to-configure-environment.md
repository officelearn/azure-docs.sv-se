---
title: Konfigurera en utvecklingsmiljö för Python
titleSuffix: Azure Machine Learning service
description: 'Lär dig mer om att konfigurera en utvecklingsmiljö när du arbetar med Azure Machine Learning-tjänsten. I den här artikeln får du lära dig hur du använder Conda-miljöer, skapa konfigurationsfiler och konfigurera Jupyter Notebooks, Azure anteckningsböcker, IDE: er, kod redigerare och Data Science Virtual Machine.'
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
ms.openlocfilehash: 46a1872d2ac5d1670620148edf7ee273580826d3
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/28/2018
ms.locfileid: "53811281"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>Konfigurera en utvecklingsmiljö för Azure Machine Learning

I den här artikeln får du lära dig hur du konfigurerar en utvecklingsmiljö med Azure Machine Learning-tjänsten. Machine Learning-tjänsten är plattformsoberoende. 

De enda kraven för din utvecklingsmiljö är Python 3, Conda (för isolerade miljöer) och en konfigurationsfil som innehåller din information för Azure Machine Learning-arbetsytan.

Den här artikeln fokuserar på följande miljöer och verktyg:

* [Azure anteckningsböcker](#aznotebooks): En Jupyter notebook-tjänst som finns i Azure-molnet. Det är det enklaste sättet att komma igång, eftersom SDK: N för Azure Machine Learning har installerats.

* [Den virtuella datorn för datavetenskap (DSVM)](#dsvm): En förinställd utvecklings- eller experimentering miljö i Azure-molnet som är avsedd för data science fungerar och kan distribueras till CPU endast VM-instanser eller GPU-baserade instanser. Python 3, Conda, Jupyter-anteckningsböcker och SDK: N för Azure Machine Learning har redan installerats. Den virtuella datorn levereras med populära machine learning och deep learning ramverk, verktyg och redigerare för att utveckla maskininlärningslösningar. Det är förmodligen mest omfattande utvecklingsmiljö för machine learning på Azure-plattformen.

* [Jupyter-anteckningsboken](#jupyter): Om du redan använder Jupyter-anteckningsboken, har vissa tillägg som du bör installera SDK: N.

* [Visual Studio Code](#vscode): Om du använder Visual Studio Code har vissa användbara tillägg som du kan installera.

* [Azure Databricks](#aml-databricks): En populära analysplattform som baseras på Apache Spark. Lär dig hur du får Azure Machine Learning-SDK till ditt kluster så att du kan distribuera modeller.

Om du redan har en Python 3-miljö eller bara vill ha de grundläggande stegen för att installera SDK: N, se den [lokal dator](#local) avsnittet.

## <a name="prerequisites"></a>Förutsättningar

- En arbetsyta för Azure Machine Learning-tjänsten. Om du vill skapa arbetsytan [Kom igång med Azure Machine Learning-tjänsten](quickstart-get-started.md).

- Antingen den [jämför Anaconda](https://www.anaconda.com/download/) eller [Miniconda](https://conda.io/miniconda.html) Pakethanteraren.

    > [!IMPORTANT]
    > Anaconda och Miniconda krävs inte när du använder Azure-anteckningsböcker.

- På Linux eller macOS måste du bash-gränssnittet.

    > [!TIP]
    > Om du är på Linux eller macOS och Använd ett skal än bash (till exempel zsh) kan du få felmeddelanden när du kör vissa kommandon. Undvik problemet genom att använda den `bash` kommando för att starta en ny bash-gränssnitt och kör kommandon det.

- På Windows måste du Kommandotolken eller Anaconda kommandotolk (installerad som Anaconda och Miniconda).

## <a id="anotebooks"></a>Azure-anteckningsböcker

[Azure anteckningsböcker](https://notebooks.azure.com) (förhandsversion) är en interaktiv utvecklingsmiljö i Azure-molnet. Det är det enklaste sättet att komma igång med Azure Machine Learning-utveckling.

* SDK: N för Azure Machine Learning är redan installerad.
* När du skapar en arbetsyta för Azure Machine Learning-tjänsten i Azure-portalen klickar du på en knapp för att automatiskt konfigurera din Azure-anteckningsbok miljö att arbeta med arbetsytan.

Kom igång med att utveckla med Azure-datorer, se [Kom igång med Azure Machine Learning-tjänsten](quickstart-get-started.md).

## <a id="dsvm"></a>Virtuell dator för datavetenskap

DSVM är en anpassad virtuell dator (VM)-avbildning. Det är utformat för data science arbete som är förkonfigurerad med:

  - Paket som till exempel TensorFlow, PyTorch, lär du dig Scikit, XGBoost och Azure Machine Learning SDK.
  - Populära verktyg för datavetenskap, till exempel Spark fristående och test.
  - Azure-verktyg som Azure CLI, AzCopy och Storage Explorer.
  - Integrated development Environment (IDE: er), till exempel Visual Studio Code, pycharm med RStudio.
  - Jupyter Notebook-Server. 

Azure Machine Learning-SDK fungerar på antingen Ubuntu eller Windows-versionen av DSVM. Om du vill använda DSVM som en utvecklingsmiljö, gör du följande:

1. Skapa en DSVM i något av följande miljöer:

    * Azure-portalen:

        * [Skapa en Ubuntu Data Science-dator](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)

        * [Skapa en Windows virtuell dator för datavetenskap](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/provision-vm)

    * Azure CLI:

        > [!IMPORTANT]
        > * När du använder Azure CLI, du måste först logga in på din Azure-prenumeration med hjälp av den `az login` kommando.
        >
        > * När du använder kommandona i det här steget måste du ange ett Resursgruppsnamn, ett namn för den virtuella datorn, ett användarnamn och ett lösenord.

        * Om du vill skapa en Ubuntu Data Science Virtual Machine, använder du följande kommando:

            ```azurecli
            # create a Ubuntu DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            # If you need to create a new resource group use: "az group create --name YOUR-RESOURCE-GROUP-NAME --location YOUR-REGION (For example: westus2)"
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:linux-data-science-vm-ubuntu:linuxdsvmubuntu:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --generate-ssh-keys --authentication-type password
            ```

        * Om du vill skapa en Windows Data Science Virtual Machine, använder du följande kommando:

            ```azurecli
            # create a Windows Server 2016 DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:dsvm-windows:server-2016:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --authentication-type password
            ```    

2. SDK: N för Azure Machine Learning är redan installerad på DSVM. Använd någon av följande kommandon om du vill använda den Conda-miljö som innehåller SDK: N:

    * För Ubuntu DSVM:

        ```shell
        conda activate py36
        ```

    * För Windows DSVM:

        ```shell
        conda activate AzureML
        ```

1. Använd följande Python-kod för att kontrollera att du kan använda SDK: N och kontrollera vilken version:

    ```python
    import azureml.core
    print(azureml.core.VERSION)
    ```

1. För att konfigurera DSVM för att använda Azure Machine Learning-tjänsten arbetsytan se den [skapar en konfigurationsfil för arbetsytan](#workspace) avsnittet.

Mer information finns i [virtuella datorer för datavetenskap](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/).

## <a id="local"></a>Lokal dator

När du använder en lokal dator (som kanske en fjärransluten virtuell dator), skapa en Conda-miljö och installera SDK: N genom att göra följande:

1. Öppna en kommandotolk eller shell.

1. Skapa en Conda-miljö med följande kommandon:

    ```shell
    # create a new Conda environment with Python 3.6, NumPy, and Cython
    conda create -n myenv Python=3.6 cython numpy

    # activate the Conda environment
    conda activate myenv

    # On macOS run
    source activate myenv
    ```

    Det kan ta flera minuter att skapa miljön om Python 3.6 och andra komponenter måste hämtas.

1. Installera Azure Machine Learning-SDK med anteckningsboken tillägg och förberedelse av Data-SDK med hjälp av följande kommando:

     ```shell
    pip install --upgrade azureml-sdk[notebooks,automl] azureml-dataprep
    ```

   > [!NOTE]
   > Om du får ett meddelande om att det inte går att avinstallera PyYAML, använder du följande kommando:
   >
   > `pip install --upgrade azureml-sdk[notebooks,automl] azureml-dataprep --ignore-installed PyYAML`

   Det kan ta flera minuter att installera SDK.

1. Installera paket för din machine learning-experimentering. Använd följande kommando och Ersätt  *\<nytt paket >* till paketet som du vill installera:

    ```shell
    conda install <new package>
    ```

1. Använd följande Python-kod för att kontrollera att SDK: N är installerad:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

### <a id="jupyter"></a>Jupyter-anteckningsböcker

Jupyter Notebooks är en del av den [Jupyter projekt](https://jupyter.org/). De ger en interaktiv kodningserfarenheter där du skapar dokument som blandar live-koden med löpande text och grafik. Jupyter Notebooks är också ett bra sätt att dela dina resultat med andra, eftersom du kan spara utdata från din kodavsnitt i dokumentet. Du kan installera Jupyter Notebooks på en rad olika plattformar.

Anvisningarna i den [lokal dator](#local) avsnittet installerar valfria komponenter för Jupyter-anteckningsböcker. Om du vill aktivera de här komponenterna i din miljö för Jupyter-anteckningsbok, gör du följande:

1. Öppna en kommandotolk eller shell.

1. Om du vill installera en Conda-medvetna Jupyter Notebook-Server, använder du följande kommando:

    ```shell
    # install Jupyter
    conda install nb_conda
    ```

1. Öppna Jupyter-anteckningsbok med följande kommando:

    ```shell
    jupyter notebook
    ```

1. Om du vill kontrollera att Jupyter-anteckningsbok kan använda SDK: N genom att öppna en ny anteckningsbok, Välj **myenv** som kernel och kör sedan följande kommando i en anteckningsbok cell:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. Om du vill konfigurera Jupyter-anteckningsboken för att använda Azure Machine Learning-tjänsten arbetsytan, går du till den [skapar en konfigurationsfil för arbetsytan](#workspace) avsnittet.

### <a id="vscode"></a>Visual Studio Code

Visual Studio Code är en plattformsoberoende Kodredigerare. Den förlitar sig på en lokal installation av Python 3 och Conda för Python-support, men ger ytterligare verktyg för att arbeta med AI. Den innehåller också stöd för att välja Conda-miljö från Kodredigeraren.

Om du vill använda Visual Studio Code för utveckling, gör du följande:

1. Läs hur du använder Visual Studio Code för Python-utveckling i [Kom igång med Python i VSCode](https://code.visualstudio.com/docs/python/python-tutorial).

1. Öppna VS Code för att välja den Conda-miljön, och välj sedan Ctrl + Skift + P (Linux och Windows) eller kommando + SKIFT + P (Mac).  
    Den __kommandot utbud__ öppnas. 

1. Ange __Python: Välj tolk__, och välj sedan den Conda-miljön.

1. Verifiera att du kan använda SDK: N genom att skapa och kör sedan en ny Python-fil (.py) som innehåller följande kod:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. Om du vill installera Azure Machine Learning-tillägget för Visual Studio Code, se [Tools för AI](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai).

    Mer information finns i [Använd Azure Machine Learning för Visual Studio Code](how-to-vscode-tools.md).

<a name="aml-databricks"></a>

## <a name="azure-databricks"></a>Azure Databricks

Du kan använda en anpassad version av Azure Machine Learning-SDK för Azure Databricks för slutpunkt till slutpunkt anpassade machine learning. Eller så kan träna din modell i Databricks och distribuera med hjälp av [Visual Studio Code](how-to-vscode-train-deploy.md#deploy-your-service-from-vs-code).

Förbereda din Databricks-klustret och får exempelanteckningsböcker:

1. Skapa en [Databricks-klustret](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) med en Databricks-körningsversion av 4.x (hög samtidighet önskade) med Python 3. 

1. Installera och bifoga Azure Machine Learning-SDK för Python `azureml-sdk[databricks]` PyPi paket till ditt kluster [skapa ett bibliotek](https://docs.databricks.com/user-guide/libraries.html#create-a-library).  
    När du är klar bifogas i biblioteket som du ser i följande bild. Tänk på dessa [vanliga problem med Databricks](resource-known-issues.md#databricks).

   ![SDK är installerat på Databricks ](./media/how-to-azure-machine-learning-on-databricks/sdk-installed-on-databricks.jpg)

   Om det här steget misslyckas, startar du om ditt kluster genom att göra följande:

   a. I den vänstra rutan väljer **kluster**. 
   
   b. I tabellen, väljer du klusternamnet. 

   c. På den **bibliotek** fliken **starta om**.

1. Ladda ned den [arkivfil för Azure Databricks/Azure Machine Learning SDK notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/databricks/Databricks_AMLSDK_github.dbc).

   >[!Warning]
   > Många exempelanteckningsböcker som är tillgängliga för användning med Azure Machine Learning-tjänsten. Endast [dessa exempelanteckningsböcker](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks) fungerar med Azure Databricks.
   > 

1.  [Importera den här arkivfilen](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-an-archive) till din Databricks-klustret och börja utforska enligt beskrivningen på den [Machine Learning anteckningsböcker](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks) sidan.


## <a id="workspace"></a>Skapa en konfigurationsfil för arbetsyta

Konfigurationsfilen för arbetsytan är en JSON-fil som talar om för SDK kommunicera med din arbetsyta för Azure Machine Learning-tjänsten. Filen heter *config.json*, och har följande format:

```json
{
    "subscription_id": "<subscription-id>",
    "resource_group": "<resource-group>",
    "workspace_name": "<workspace-name>"
}
```

Det här JSON-filen måste vara i mappstrukturen som innehåller ett Python-skript- eller Jupyter-anteckningsböcker. Det kan vara i samma katalog, en undermapp som heter *aml_config*, eller i en överordnad katalog.

Om du vill använda den här filen från din kod använder `ws=Workspace.from_config()`. Den här koden läser in informationen från filen och ansluter till din arbetsyta.

Du kan skapa konfigurationsfilen på tre sätt:

* **Följ den [Snabbstart för Azure Machine Learning](quickstart-get-started.md)**: En *config.json* fil skapas i biblioteket Azure anteckningsböcker. Filen innehåller konfigurationsinformation för arbetsytan. Du kan ladda ned eller kopiera den *config.json* till andra utvecklingsmiljöer.

* **Skapa filen manuellt**: Med den här metoden kan använda du en textredigerare. Du kan hitta de värden som ingår i konfigurationsfilen genom att gå till din arbetsyta i den [Azure-portalen](https://portal.azure.com). Kopiera arbetsytans namn, resursgrupp och prenumeration ID-värden och Använd dem i konfigurationsfilen.

     ![Azure Portal](./media/how-to-configure-environment/configure.png)

* **Skapa filen programmässigt**: I följande kodavsnitt ansluter du till en arbetsyta genom att ange prenumerations-ID, resursgrupp och namn på arbetsyta. Därefter sparar arbetsytans konfiguration till filen:

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

    Den här koden skriver konfigurationsfilen för att den *aml_config/config.json* fil.

## <a name="next-steps"></a>Nästa steg

- [Träna en modell i Azure Machine Learning med MNIST-datauppsättning](tutorial-train-models-with-aml.md)
- [Azure Machine Learning-SDK för Python](https://aka.ms/aml-sdk)
- [Azure Machine Learning-SDK för dataförberedelse](https://aka.ms/data-prep-sdk)
