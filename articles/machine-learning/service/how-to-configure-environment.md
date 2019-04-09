---
title: Konfigurera en utvecklingsmiljö för Python
titleSuffix: Azure Machine Learning service
description: 'Lär dig mer om att konfigurera en utvecklingsmiljö när du arbetar med Azure Machine Learning-tjänsten. I den här artikeln får du lära dig hur du använder Conda-miljöer, skapa konfigurationsfiler och konfigurera en egen molnbaserad notebook-server, Jupyter Notebooks, Azure Databricks, Azure anteckningsböcker, IDE: er, kod redigerare och Data Science Virtual Machine.'
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.topic: conceptual
ms.date: 02/24/2019
ms.custom: seodec18
ms.openlocfilehash: 4aabf15478a6f8e688ea591832ca325f53144df8
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59263204"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>Konfigurera en utvecklingsmiljö för Azure Machine Learning

I den här artikeln får du lära dig hur du konfigurerar en utvecklingsmiljö med Azure Machine Learning-tjänsten. Machine Learning-tjänsten är plattformsoberoende.

De enda kraven för din utvecklingsmiljö är Python 3, Anaconda (för isolerade miljöer) och en konfigurationsfil som innehåller din information för Azure Machine Learning-arbetsytan.

Den här artikeln fokuserar på följande miljöer och verktyg:

* Egna [molnbaserade anteckningsboksserver](#workstation): Använd en beräkningsresurs i din arbetsstation för att köra Jupyter-anteckningsböcker. Det är det enklaste sättet att komma igång, eftersom SDK: N för Azure Machine Learning har installerats.

* [Den virtuella datorn för datavetenskap (DSVM)](#dsvm): En förinställd utvecklings- eller experimentering miljö i Azure-molnet som är avsedd för data science fungerar och kan distribueras till CPU endast VM-instanser eller GPU-baserade instanser. Python 3, Conda, Jupyter-anteckningsböcker och SDK: N för Azure Machine Learning har redan installerats. Den virtuella datorn levereras med populära machine learning och deep learning ramverk, verktyg och redigerare för att utveckla maskininlärningslösningar. Det är förmodligen mest omfattande utvecklingsmiljö för machine learning på Azure-plattformen.

* [Jupyter-anteckningsboken](#jupyter): Om du redan använder Jupyter-anteckningsboken, har vissa tillägg som du bör installera SDK: N.

* [Visual Studio Code](#vscode): Om du använder Visual Studio Code har vissa användbara tillägg som du kan installera.

* [Azure Databricks](#aml-databricks): En populära analysplattform som baseras på Apache Spark. Lär dig hur du får Azure Machine Learning-SDK till ditt kluster så att du kan distribuera modeller.

* [Azure Notebooks](#aznotebooks): En Jupyter notebook-tjänst som finns i Azure-molnet. Även ett enkelt sätt att komma igång, eftersom SDK: N för Azure Machine Learning har installerats.  

Om du redan har en Python 3-miljö eller bara vill ha de grundläggande stegen för att installera SDK: N, se den [lokal dator](#local) avsnittet.

## <a name="prerequisites"></a>Förutsättningar

- En arbetsyta för Azure Machine Learning-tjänsten. Om du vill skapa arbetsytan [skapa en arbetsyta för Azure Machine Learning-tjänsten](setup-create-workspace.md).

En arbetsyta är allt du behöver för att komma igång med dina egna [molnbaserade anteckningsboksserver](#workstation), ett [DSVM](#dsvm), [Azure Databricks](#aml-databricks), eller [Azure anteckningsböcker](#aznotebooks).

Installera SDK-miljö för dina [lokala](#local), [Jupyter Notebook-server](#jupyter) eller [Visual Studio Code](#vscode) måste du också:

- Antingen den [Anaconda](https://www.anaconda.com/download/) eller [Miniconda](https://conda.io/miniconda.html) Pakethanteraren.

- På Linux eller macOS måste du bash-gränssnittet.

    > [!TIP]
    > Om du är på Linux eller macOS och Använd ett skal än bash (till exempel zsh) kan du få felmeddelanden när du kör vissa kommandon. Undvik problemet genom att använda den `bash` kommando för att starta en ny bash-gränssnitt och kör kommandon det.

- På Windows måste du Kommandotolken eller Anaconda kommandotolk (installerad som Anaconda och Miniconda).

## <a id="workstation"></a>En egen molnbaserad notebook-server

Skapa en notebook-server i Azure Machine Learning-arbetsytan för det enklaste sättet att komma igång med Azure Machine Learning-utveckling.

* SDK: N för Azure Machine Learning är redan installerad.
* Arbetsstationen miljö konfigureras automatiskt för att fungera med din arbetsyta.
* Resursen är tillgänglig och kan hanteras i din arbetsyta

Kom igång med att utveckla med din molnbaserade notebook-server, se [Kom igång med Azure Machine Learning-tjänsten](quickstart-run-cloud-notebook.md).


## <a id="dsvm"></a>Virtuell dator för datavetenskap

DSVM är en anpassad virtuell dator (VM)-avbildning. Det är utformat för data science arbete som är förkonfigurerad med:

  - Paket som till exempel TensorFlow, PyTorch, lär du dig Scikit, XGBoost och Azure Machine Learning SDK
  - Populära verktyg för datavetenskap som Spark fristående och ökad detaljnivå
  - Azure-verktyg som Azure CLI, AzCopy och Storage Explorer
  - Integrated development Environment (IDE: er), till exempel Visual Studio Code och PyCharm
  - Jupyter Notebook-Server

Azure Machine Learning-SDK fungerar på antingen Ubuntu eller Windows-versionen av DSVM. Men om du tänker använda DSVM som ett beräkningsmål endast Ubuntu stöds.

Om du vill använda DSVM som en utvecklingsmiljö, gör du följande:

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

När du använder en lokal dator (som kanske en fjärransluten virtuell dator), skapa en Anaconda-miljö och installera SDK: N genom att göra följande:

1. Ladda ned och installera [Anaconda](https://www.anaconda.com/distribution/#download-section) (Python 3.7 version) om du inte redan har den.

1. Öppna en Anaconda-kommandotolk och skapa en miljö med följande kommandon:

    Kör följande kommando för att skapa miljön.

    ```shell
    conda create -n myenv python=3.6.5
    ```

    Aktivera sedan miljön.

    ```shell
    conda activate myenv
    ```

    Det här exemplet skapar en miljö med hjälp av python 3.6.5, men några specifika subversions kan väljas. Kanske inte kan garanteras SDK kompatibilitet med vissa huvudversioner (3.5 + rekommenderas), och vi rekommenderar att du prova en annan version/subversion i miljön Anaconda om du stöter på fel. Det tar flera minuter att skapa miljön medan komponenter och paket laddas ned.

1. Kör följande kommandon i din nya miljö för att aktivera miljöspecifika ipython-kärnor. Detta säkerställer förväntade kernel och paketet importera beteende när du arbetar med Jupyter-anteckningsböcker i Anaconda miljöer:

    ```shell
    conda install notebook ipykernel
    ```

    Kör sedan följande kommando för att skapa kernel:

    ```shell
    ipython kernel install --user
    ```

1. Använd följande kommandon för att installera paket:

    Det här kommandot installerar grundläggande Azure Machine Learning-SDK med anteckningsbok och automl tillägg. Den `automl` extra är en stor installation och kan tas bort från hakparenteserna om du inte planerar att köra automatiserade machine learning-experiment. Den `automl` extra även Azure Machine Learning Data Prep SDK som standard som ett beroende.

     ```shell
    pip install azureml-sdk[notebooks,automl]
    ```

    Använd följande kommando för att installera Azure Machine Learning Data Prep SDK på egen hand:

    ```shell
    pip install azureml-dataprep
    ```

   > [!NOTE]
   > Om du får ett meddelande om att det inte går att avinstallera PyYAML, använder du följande kommando:
   >
   > `pip install --upgrade azureml-sdk[notebooks,automl] azureml-dataprep --ignore-installed PyYAML`

   Det tar flera minuter att installera SDK. Se den [installationsguide](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) mer information om installationsalternativ.

1. Installera andra paket för din machine learning-experimentering.

    Använd någon av följande kommandon och Ersätt  *\<nytt paket >* till paketet som du vill installera. Installera paket via `conda install` kräver att paketet är en del av de aktuella kanaler (nya kanaler kan läggas till i Anaconda molnet).

    ```shell
    conda install <new package>
    ```

    Du kan också installera paketen via `pip`.

    ```shell
    pip install <new package>
    ```

### <a id="jupyter"></a>Jupyter-anteckningsböcker

Jupyter Notebooks är en del av den [Jupyter projekt](https://jupyter.org/). De ger en interaktiv kodningserfarenheter där du skapar dokument som blandar live-koden med löpande text och grafik. Jupyter Notebooks är också ett bra sätt att dela dina resultat med andra, eftersom du kan spara utdata från din kodavsnitt i dokumentet. Du kan installera Jupyter Notebooks på en rad olika plattformar.

Anvisningarna i den [lokal dator](#local) avsnittet installerar nödvändiga komponenter för att köra Jupyter Notebooks i en Anaconda-miljö. Om du vill aktivera de här komponenterna i din miljö för Jupyter-anteckningsbok, gör du följande:

1. Öppna en kommandotolk Anaconda och aktivera din miljö.

    ```shell
    conda activate myenv
    ```

1. Starta Jupyter Notebook-server med följande kommando:

    ```shell
    jupyter notebook
    ```

1. Kontrollera att Jupyter-anteckningsbok kan använda SDK: N genom att skapa en **New** anteckningsboken väljer **Python 3** som kernel och kör sedan följande kommando i en anteckningsbok cell:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. Om du stöter på problem när du importerar moduler och får en `ModuleNotFoundError`, kontrollera Jupyter-kerneln är ansluten till rätt sökväg för din miljö genom att köra följande kod i en anteckningsbok cell.

    ```python
    import sys
    sys.path
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
Azure Databricks är en Apache Spark-baserad miljö i Azure-molnet. Det ger en samarbetsmiljö Notebook baserat med CPU eller GPU baserat beräkningskluster.

Hur fungerar Azure Databricks med Azure Machine Learning-tjänsten:
+ Du kan träna en modell med Spark MLlib och distribuera modellen till ACI/AKS från inom Azure Databricks. 
+ Du kan också använda [automatiserad maskininlärning](concept-automated-ml.md) funktioner i en särskild Azure ML-SDK med Azure Databricks.
+ Du kan använda Azure Databricks som beräkningsmål från en [Azure Machine Learning pipeline](concept-ml-pipelines.md). 

### <a name="set-up-your-databricks-cluster"></a>Konfigurera din Databricks-kluster

Skapa en [Databricks-klustret](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal). Vissa inställningar gäller endast om du installerar med SDK för automatiserade maskininlärning på Databricks.
**Det tar några minuter att skapa klustret.**

Använd de här inställningarna:

| Inställning |Gäller| Värde |
|----|---|---|
| Klusternamn |alltid| yourclustername |
| Databricks Runtime |alltid| Alla icke ML-körning (icke ML 4.x, 5.x) |
| Python-version |alltid| 3 |
| Arbetare |alltid| 2 eller högre |
| VM-typer för Worker-nod <br>(avgör max antal samtidiga iterationer) |Automatisk ML<br>endast| Minnesoptimerade virtuella datorer rekommenderas |
| Aktivera automatisk skalning |Automatisk ML<br>endast| Avmarkera |

Vänta tills klustret körs innan du fortsätter.

### <a name="install-the-correct-sdk-into-a-databricks-library"></a>Installera rätt SDK till ett Databricks-bibliotek
När klustret körs [skapa ett bibliotek](https://docs.databricks.com/user-guide/libraries.html#create-a-library) att bifoga lämplig Azure Machine Learning SDK-paketet i ditt kluster. 

1. Välj **endast en** alternativet (inga andra SDK-installation stöds)

   |SDK&nbsp;paketet&nbsp;tillägg|Källa|PyPi&nbsp;Name&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
   |----|---|---|
   |För Databricks| Ladda upp Python ägg eller PyPI | azureml-sdk[databricks]|
   |För Databricks - med-<br> automatiserad ML-kapacitet| Ladda upp Python ägg eller PyPI | azureml-sdk[automl_databricks]|

   > [!Warning]
   > Inga andra SDK-tillägg kan installeras. Välj endast en av föregående alternativ [databricks] eller [automl_databricks].

   * Markera inte **ansluta automatiskt till alla kluster**.
   * Välj **bifoga** bredvid klusternamnet.

1. Övervakare för fel tills status ändras till **anslutna**, vilket kan ta flera minuter.  Om det här steget misslyckas kan du kontrollera följande: 

   Försök att starta om ditt kluster genom att:
   1. I den vänstra rutan väljer **kluster**.
   1. I tabellen, väljer du klusternamnet.
   1. På den **bibliotek** fliken **starta om**.
      
   Även överväga:
   + I Automl konfigurationen när du använder Azure Databricks Lägg till följande parametrar:
    1. ```max_concurrent_iterations``` baserat på antalet arbetarnoder i klustret. 
    2. ```spark_context=sc``` #databricks/spark standard spark-kontext. 
   + Eller, om du har en äldre version av SDK, avmarkera den från klustrets installerade libs och flytta till Papperskorgen. Installera den nya versionen av SDK och starta om klustret. Om det finns ett problem efter det, frånkoppla eller återansluta ditt kluster.

Om installationen slutfördes korrekt visas importerade biblioteket bör se ut som något av följande:
   
SDK för Databricks **_utan_** automatiserad maskininlärning ![Azure Machine Learning-SDK för Databricks](./media/how-to-configure-environment/amlsdk-withoutautoml.jpg)

SDK för Databricks **WITH** automatiserad maskininlärning ![SDK med automatiserade maskininlärning som installerats på Databricks](./media/how-to-configure-environment/automlonadb.jpg)

### <a name="start-exploring"></a>Börja utforska

Prova:
+ Ladda ned den [notebook arkivfilen](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks/Databricks_AMLSDK_1-4_6.dbc) för Azure Databricks/Azure Machine Learning SDK och [importera arkivfilen](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-an-archive) i Databricks-klustret.  
  Även om många exempelanteckningsböcker som är tillgängliga, **endast [dessa exempelanteckningsböcker](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks) fungerar med Azure Databricks.**
  
+ Lär dig hur du [skapa en pipeline med Databricks som utbildning beräkning](how-to-create-your-first-pipeline.md).

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

* **Följ stegen i [skapa en arbetsyta för Azure Machine Learning-tjänsten](setup-create-workspace.md#sdk)**: En *config.json* fil skapas i biblioteket Azure anteckningsböcker. Filen innehåller konfigurationsinformation för arbetsytan. Du kan ladda ned eller kopiera den *config.json* till andra utvecklingsmiljöer.

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

## <a id="aznotebooks"></a>Azure-anteckningsböcker

[Azure anteckningsböcker](https://notebooks.azure.com) (förhandsversion) är en interaktiv utvecklingsmiljö i Azure-molnet. Det är ett enkelt sätt att komma igång med Azure Machine Learning-utveckling.

* SDK: N för Azure Machine Learning är redan installerad.
* När du skapar en arbetsyta för Azure Machine Learning-tjänsten i Azure-portalen klickar du på en knapp för att automatiskt konfigurera din Azure-anteckningsbok miljö att arbeta med arbetsytan.

Använd den [Azure-portalen](https://portal.azure.com) att komma igång med Azure-anteckningsböcker.  Öppna din arbetsyta och från den **översikt** väljer **Kom igång i Azure anteckningsböcker**.

Som standard använder en kostnadsfri tjänst-nivå som är begränsad till 4GB minne och 1GB data i Azure-anteckningsböcker. Du kan dock ta bort dessa begränsningar genom att koppla en virtuell dator för datavetenskap-instans i Azure-anteckningsböcker-projektet. Mer information finns i [hantera och konfigurera Azure anteckningsböcker projekt - Beräkningsnivån](/azure/notebooks/configure-manage-azure-notebooks-projects#compute-tier).

## <a name="next-steps"></a>Nästa steg

- [Träna en modell](tutorial-train-models-with-aml.md) i Azure Machine Learning med MNIST datauppsättningen
- Visa den [Azure Machine Learning-SDK för Python](https://aka.ms/aml-sdk) referens
- Lär dig mer om den [Azure Machine Learning Data Förbered SDK](https://aka.ms/data-prep-sdk)
