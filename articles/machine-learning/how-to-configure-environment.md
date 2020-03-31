---
title: Konfigurera en Python-utvecklingsmiljö
titleSuffix: Azure Machine Learning
description: Lär dig att konfigurera din utvecklingsmiljö för Azure Machine Learning. Använd Conda-miljöer, skapa konfigurationsfiler och konfigurera din egen molnbaserade anteckningsboksserver, Jupyter-anteckningsböcker, Azure Databricks, IDEs, kodredigerare och virtual machine för datavetenskap.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.topic: conceptual
ms.date: 12/27/2019
ms.custom: seodec18
ms.openlocfilehash: a5f46f5af723e1245afbc6bca90d25ae9036d646
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472434"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>Konfigurera en utvecklingsmiljö för Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här artikeln får du lära dig hur du konfigurerar en utvecklingsmiljö för att arbeta med Azure Machine Learning. Azure Machine Learning är plattformsoberoende. Det enda svåra kravet för din utvecklingsmiljö är Python 3. En isolerad miljö som Anaconda eller Virtualenv rekommenderas också.

I följande tabell visas varje utvecklingsmiljö som beskrivs i den här artikeln, tillsammans med för- och nackdelar.

| Miljö | Fördelar | Nackdelar |
| --- | --- | --- |
| [Molnbaserad Azure Machine Learning-beräkningsinstans (förhandsversion)](#compute-instance) | Enklaste sättet att komma igång. Hela SDK är redan installerat i din virtuella arbetsyta och självstudier för bärbara datorer är för klonade och redo att köras. | Brist på kontroll över din utvecklingsmiljö och beroenden. Ytterligare kostnader för Linux VM (VM kan stoppas när den inte används för att undvika avgifter). Se [prisinformation](https://azure.microsoft.com/pricing/details/virtual-machines/linux/). |
| [Lokal miljö](#local) | Full kontroll över din utvecklingsmiljö och ditt beroende. Kör med valfri byggverktyg, miljö eller IDE. | Det tar längre tid att komma igång. Nödvändiga SDK-paket måste installeras och en miljö måste också installeras om du inte redan har en. |
| [Azure Databricks](#aml-databricks) | Perfekt för att köra storskaliga intensiva maskininlärningsarbetsflöden på den skalbara Apache Spark-plattformen. | Overkill för experimentell maskininlärning, eller mindre experiment och arbetsflöden. Extra kostnad för Azure Databricks. Se [prisinformation](https://azure.microsoft.com/pricing/details/databricks/). |
| [Den virtuella datorn för datavetenskap (DSVM)](#dsvm) | I likhet med den molnbaserade beräkningsinstansen (Python och SDK är förinstallerade), men med ytterligare populära datavetenskap och maskininlärningsverktyg förinstallerade. Lätt att skala och kombinera med andra anpassade verktyg och arbetsflöden. | En långsammare komma igång-upplevelse jämfört med den molnbaserade beräkningsinstansen. |

Den här artikeln innehåller också ytterligare användningstips för följande verktyg:

* [Jupyter bärbara datorer:](#jupyter)Om du redan använder Jupyter Notebook, har SDK några extrafunktioner som du bör installera.

* [Visual Studio-kod:](#vscode)Om du använder Visual Studio-kod innehåller [Azure Machine Learning-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai) omfattande språkstöd för Python samt funktioner för att göra det mycket bekvämare och mer produktivt att arbeta med Azure Machine Learning.

## <a name="prerequisites"></a>Krav

En Azure Machine Learning-arbetsyta. Information om hur du skapar arbetsytan finns i [Skapa en Azure Machine Learning-arbetsyta](how-to-manage-workspace.md). En arbetsyta är allt du behöver för att komma igång med din egen [molnbaserade anteckningsboksserver](#compute-instance), en [DSVM](#dsvm)eller [Azure Databricks](#aml-databricks).

Om du vill installera SDK-miljön för din [lokala dator,](#local) [Jupyter Notebook server](#jupyter) eller Visual Studio [Code](#vscode) behöver du också:

- Antingen [Anaconda](https://www.anaconda.com/download/) eller [Miniconda](https://conda.io/miniconda.html) pakethanterare.

- På Linux eller macOS behöver du bash-skalet.

    > [!TIP]
    > Om du är på Linux eller macOS och använder ett annat skal än bash (till exempel zsh) kan du få fel när du kör vissa kommandon. För att komma runt `bash` det här problemet, använd kommandot för att starta en ny bash skal och köra kommandon där.

- I Windows behöver du kommandotolken eller Anaconda-prompten (installerad av Anaconda och Miniconda).

## <a name="your-own-cloud-based-compute-instance"></a><a id="compute-instance"></a>Din egen molnbaserade beräkningsinstans

Azure Machine Learning [compute instance (preview)](concept-compute-instance.md) är en säker, molnbaserad Azure-arbetsstation som ger dataforskare med en Jupyter notebook server, JupyterLab och en fullt förberedd ML-miljö.

Det finns inget att installera eller konfigurera för en beräkningsinstans.  Skapa en när som helst inifrån din Azure Machine Learning-arbetsyta. Ange bara ett namn och ange en Azure VM-typ. Prova nu med den här [självstudien: Installationsmiljö och arbetsyta](tutorial-1st-experiment-sdk-setup.md).

Läs mer om [beräkningsinstanser](concept-compute-instance.md).

Om du vill sluta ådra dig beräkningsavgifter [stoppar du beräkningsinstansen](tutorial-1st-experiment-sdk-train.md#clean-up-resources).

## <a name="data-science-virtual-machine"></a><a id="dsvm"></a>Virtuell dator för datavetenskap

DSVM är en anpassad virtuell datoravbildning (VM). Den är utformad för datavetenskapsarbete som är förkonfigurerad med:

  - Paket som TensorFlow, PyTorch, Scikit-learn, XGBoost och Azure Machine Learning SDK
  - Populära datavetenskapsverktyg som Spark Standalone och Drill
  - Azure-verktyg som Azure CLI, AzCopy och Storage Explorer
  - Integrerade utvecklingsmiljöer (IDEs) som Visual Studio Code och PyCharm
  - Jupyter anteckningsboksserver

Azure Machine Learning SDK fungerar på antingen Ubuntu- eller Windows-versionen av DSVM. Men om du planerar att använda DSVM som ett beräkningsmål också, stöds bara Ubuntu.

Så här använder du DSVM som utvecklingsmiljö:

1. Skapa en DSVM i någon av följande miljöer:

    * Azure-portalen:

        * [Skapa en Data Science Virtual Machine med Ubuntu](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)

        * [Skapa en Data Science Virtual Machine med Windows](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/provision-vm)

    * The Azure CLI:

        > [!IMPORTANT]
        > * När du använder Azure CLI måste du först logga in `az login` på din Azure-prenumeration med kommandot.
        >
        > * När du använder kommandona i det här steget måste du ange ett resursgruppnamn, ett namn på den virtuella datorn, ett användarnamn och ett lösenord.

        * Om du vill skapa en virtuell dator med Ubuntu Data Science använder du följande kommando:

            ```azurecli-interactive
            # create a Ubuntu DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            # If you need to create a new resource group use: "az group create --name YOUR-RESOURCE-GROUP-NAME --location YOUR-REGION (For example: westus2)"
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:linux-data-science-vm-ubuntu:linuxdsvmubuntu:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --generate-ssh-keys --authentication-type password
            ```

        * Så här skapar du en virtuell dator för Windows Data Science:

            ```azurecli-interactive
            # create a Windows Server 2016 DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:dsvm-windows:server-2016:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --authentication-type password
            ```

2. Azure Machine Learning SDK är redan installerat på DSVM. Om du vill använda Conda-miljön som innehåller SDK använder du något av följande kommandon:

    * För Ubuntu DSVM:

        ```bash
        conda activate py36
        ```

    * För Windows DSVM:

        ```bash
        conda activate AzureML
        ```

1. Om du vill kontrollera att du kan komma åt SDK och kontrollera versionen använder du följande Python-kod:

    ```python
    import azureml.core
    print(azureml.core.VERSION)
    ```

1. Information om hur du konfigurerar DSVM så att den använder arbetsytan Azure Machine Learning finns i avsnittet [Skapa en konfigurationsfil](#workspace) för arbetsytan.

Mer information finns i [Virtuella datavetenskapsdatorer](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/).

## <a name="local-computer"></a><a id="local"></a>Lokal dator

När du använder en lokal dator (som också kan vara en virtuell fjärrdator) skapar du en Anaconda-miljö och installerar SDK. Här är ett exempel:

1. Ladda ned och installera [Anaconda](https://www.anaconda.com/distribution/#download-section) (Python 3.7-versionen) om du inte redan har den.

1. Öppna en Anaconda-fråga och skapa en miljö med följande kommandon:

    Kör följande kommando för att skapa miljön.

    ```bash
    conda create -n myenv python=3.6.5
    ```

    Aktivera sedan miljön.

    ```bash
    conda activate myenv
    ```

    Det här exemplet skapar en miljö med python 3.6.5, men alla specifika delversioner kan väljas. SDK-kompatibilitet kanske inte garanteras med vissa huvudversioner (3.5+ rekommenderas), och det rekommenderas att prova en annan version/subversion i Anaconda-miljön om du stöter på fel. Det tar flera minuter att skapa miljön medan komponenter och paket laddas ned.

1. Kör följande kommandon i din nya miljö för att aktivera miljöspecifika IPython-kärnor. Detta säkerställer förväntat kernel- och paketimportbeteende när du arbetar med Jupyter-anteckningsböcker i Anaconda-miljöer:

    ```bash
    conda install notebook ipykernel
    ```

    Kör sedan följande kommando för att skapa kärnan:

    ```bash
    ipython kernel install --user --name myenv --display-name "Python (myenv)"
    ```

1. Använd följande kommandon för att installera paket:

    Det här kommandot installerar basen Azure Machine Learning `automl` SDK med anteckningsbok och extrafunktioner. Den `automl` extra är en stor installation och kan tas bort från parenteserna om du inte tänker köra automatiserade maskininlärningsexperiment. Den `automl` extra innehåller också Azure Machine Learning Data Prep SDK som standard som ett beroende.

    ```bash
    pip install azureml-sdk[notebooks,automl]
    ```

   > [!NOTE]
   > * Om du får ett meddelande om att PyYAML inte kan avinstalleras använder du följande kommando i stället:
   >
   >   `pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML`
   >
   > * Från och med macOS Catalina, zsh (Z skal) är standard inloggningsskal och interaktiva skal. I zsh använder du följande kommando som\\undgår hakparenteser med " " (omvänt snedstreck):
   >
   >   `pip install --upgrade azureml-sdk\[notebooks,automl\]`

   Det tar flera minuter att installera SDK. Mer information om installationsalternativ finns i [installationsguiden](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

1. Installera andra paket för maskininlärningsexperiment.

    Använd något av följande kommandon * \<* och ersätt nytt paket>med det paket som du vill installera. Installera paket `conda install` via kräver att paketet är en del av de aktuella kanalerna (nya kanaler kan läggas till i Anaconda Cloud).

    ```bash
    conda install <new package>
    ```

    Alternativt kan du installera paket `pip`via .

    ```bash
    pip install <new package>
    ```

### <a name="jupyter-notebooks"></a><a id="jupyter"></a>Jupyter bärbara datorer

Jupyter bärbara datorer är en del av [Jupyter Project](https://jupyter.org/). De ger en interaktiv kodningsupplevelse där du skapar dokument som blandar livekod med berättande text och grafik. Jupyter bärbara datorer är också ett bra sätt att dela dina resultat med andra, eftersom du kan spara utdata från dina kodavsnitt i dokumentet. Du kan installera Jupyter bärbara datorer på en mängd olika plattformar.

Proceduren i avsnittet [Lokal dator](#local) installerar nödvändiga komponenter för att köra Jupyter-anteckningsböcker i en Anaconda-miljö.

Så här aktiverar du dessa komponenter i jupyterns anteckningsboksmiljö:

1. Öppna en Anaconda-prompt och aktivera din miljö.

    ```bash
    conda activate myenv
    ```

1. Klona [GitHub-databasen](https://aka.ms/aml-notebooks) för en uppsättning exempelanteckningsböcker.

    ```bash
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Starta Jupyter Notebook-servern med följande kommando:

    ```bash
    jupyter notebook
    ```

1. Om du vill kontrollera att Jupyter Notebook kan använda SDK skapar du en **ny** anteckningsbok, väljer **Python 3** som kärna och kör sedan följande kommando i en anteckningsbokscell:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. Om du stöter på problem `ModuleNotFoundError`med att importera moduler och ta emot en kontrollerar du att Jupyter-kärnan är ansluten till rätt sökväg för din miljö genom att köra följande kod i en anteckningsbokscell.

    ```python
    import sys
    sys.path
    ```

1. Om du vill konfigurera Jupyter-anteckningsboken så att den använder arbetsytan Azure Machine Learning går du till avsnittet [Skapa en arbetsytekonfigurationsfil.](#workspace)

### <a name="visual-studio-code"></a><a id="vscode"></a>Visual Studio-kod

Visual Studio Code är en mycket populär plattformsoberoende kodredigerare som stöder en omfattande uppsättning programmeringsspråk och verktyg genom tillägg som finns på [Visual Studio-marknadsplatsen.](https://marketplace.visualstudio.com/vscode) [Azure Machine Learning-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai) installerar [Python-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-python.python) för kodning i alla typer av Python-miljöer (virtuella, Anaconda osv.). Dessutom ger det bekvämlighetsfunktioner för att arbeta med Azure Machine Learning-resurser och köra Azure Machine Learning-experiment utan att lämna Visual Studio Code.

Så här använder du Visual Studio Code för utveckling:

1. Installera Azure Machine Learning-tillägget för Visual Studio-kod, se [Azure Machine Learning](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai).

    Mer information finns i [Använda Azure Machine Learning for Visual Studio Code](tutorial-setup-vscode-extension.md).

1. Lär dig hur du använder Visual Studio-kod för alla typer av Python-utveckling, se [Kom igång med Python i VSCode](https://code.visualstudio.com/docs/python/python-tutorial).

    - Om du vill välja SDK Python-miljön som innehåller SDK öppnar du VS-kod och väljer sedan Ctrl+Skift+P (Linux och Windows) eller Kommando+Skift+P (Mac).
        - __Kommandopaletten__ öppnas.

    - Ange __Python: Välj tolk__och välj sedan rätt miljö

1. Om du vill verifiera att du kan använda SDK skapar du en ny Python-fil (.py) som innehåller följande kod:

    ```python
    #%%
    import azureml.core
    azureml.core.VERSION
    ```
    Kör den här koden genom att klicka på "Kör cell" CodeLens eller tryck helt enkelt på skift-enter.
<a name="aml-databricks"></a>

## <a name="azure-databricks"></a>Azure Databricks
Azure Databricks är en Apache Spark-baserad miljö i Azure-molnet. Det ger en gemensam notebook-baserad miljö med CPU eller GPU-baserade beräkningskluster.

Så här fungerar Azure Databricks med Azure Machine Learning:
+ Du kan träna en modell med Spark MLlib och distribuera modellen till ACI/AKS inifrån Azure Databricks.
+ Du kan också använda [automatiserade maskininlärningsfunktioner](concept-automated-ml.md) i en särskild Azure ML SDK med Azure Databricks.
+ Du kan använda Azure Databricks som ett beräkningsmål från en [Azure Machine Learning pipeline](concept-ml-pipelines.md).

### <a name="set-up-your-databricks-cluster"></a>Konfigurera ditt Databricks-kluster

Skapa ett [Databricks-kluster](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal). Vissa inställningar gäller endast om du installerar SDK för automatiserad maskininlärning på Databricks.
**Det tar några minuter att skapa klustret.**

Använd de här inställningarna:

| Inställning |Gäller| Värde |
|----|---|---|
| Klusternamn |alltid| dittklusternamn |
| Databricks Runtime |alltid|Icke-ML Runtime 6.0 (scala 2.11, spark 2.4.3) |
| Python-version |alltid| 3 |
| Arbetstagare |alltid| 2 eller högre |
| VM-typer för arbetarenod <br>(bestämmer max # av samtidiga iterationer) |Automatisk ML<br>Bara| Minnesoptimerad virtuell dator föredras |
| Aktivera automatisk skalning |Automatisk ML<br>Bara| Avmarkera |

Vänta tills klustret körs innan du fortsätter vidare.

### <a name="install-the-correct-sdk-into-a-databricks-library"></a>Installera rätt SDK i ett Databricks-bibliotek
När klustret körs [skapar du ett bibliotek](https://docs.databricks.com/user-guide/libraries.html#create-a-library) för att koppla er till lämpligt Azure Machine Learning SDK-paket till klustret.

1. Högerklicka på den aktuella arbetsytemappen där du vill lagra biblioteket. Välj **Skapa** > **bibliotek**.

1. Välj **bara ett** alternativ (ingen annan SDK-installation stöds)

   |SDK-paket&nbsp;&nbsp;extra|Källa|PyPi-namn&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
   |----|---|---|
   |För Databricks| Ladda upp Python Egg eller PyPI | azureml-sdk[databricks]|
   |För Databricks - med-<br> automatiserade ML-funktioner| Ladda upp Python Egg eller PyPI | azureml-sdk[automl]|

   > [!Warning]
   > Inga andra SDK-extrafunktioner kan installeras. Välj bara ett av föregående alternativ [databricks] eller [automl].

   * Välj inte **Bifoga automatiskt till alla kluster**.
   * Välj **Bifoga bredvid** klusternamnet.

1. Övervaka för fel tills status ändras till **Bifogat**, vilket kan ta flera minuter.  Om det här steget misslyckas:

   Prova att starta om klustret genom att:
   1. Välj **Kluster**i den vänstra rutan .
   1. Markera klusternamnet i tabellen.
   1. Välj **Starta om**på fliken **Bibliotek** .

   Tänk också på följande:
   + I AutoML config lägger du till följande parametrar när du använder Azure Databricks:
       1. ```max_concurrent_iterations```baseras på antalet arbetsnoder i klustret.
        2. ```spark_context=sc```baseras på standardgnisskontexten.
   + Om du har en gammal SDK-version avmarkerar du den från klustrets installerade libs och flyttar till papperskorgen. Installera den nya SDK-versionen och starta om klustret. Om det uppstår ett problem efter omstarten kopplar du från och sätter tillbaka klustret.

Om installationen lyckades bör det importerade biblioteket se ut som ett av följande:

SDK för Databricks **_utan_** automatiserad maskininlärning ![Azure Machine Learning SDK för Databricks](./media/how-to-configure-environment/amlsdk-withoutautoml.jpg)

SDK för Databricks **MED** automatiserad maskininlärning ![SDK med automatiserad maskininlärning installerad på Databricks](./media/how-to-configure-environment/automlonadb.png)

### <a name="start-exploring"></a>Börja utforska

Prova det:
+ Många exempeldatorböcker är tillgängliga, **men endast [dessa exempeldatorböcker](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks) fungerar med Azure Databricks.**

+ Importera dessa exempel direkt från arbetsytan. Se nedan: ![](./media/how-to-configure-environment/azure-db-screenshot.png)
![Välj importpanel](./media/how-to-configure-environment/azure-db-import.png)

+ Lär dig hur du [skapar en pipeline med Databricks som träningsberäkning](how-to-create-your-first-pipeline.md).

## <a name="create-a-workspace-configuration-file"></a><a id="workspace"></a>Skapa en konfigurationsfil för arbetsytan

Konfigurationsfilen för arbetsytan är en JSON-fil som talar om för SDK hur du kommunicerar med din Azure Machine Learning-arbetsyta. Filen heter *config.json*och har följande format:

```json
{
    "subscription_id": "<subscription-id>",
    "resource_group": "<resource-group>",
    "workspace_name": "<workspace-name>"
}
```

Den här JSON-filen måste finnas i katalogstrukturen som innehåller dina Python-skript eller Jupyter-anteckningsböcker. Den kan finnas i samma katalog, en underkatalog med namnet *.azureml*eller i en överordnad katalog.

Om du vill använda den `ws=Workspace.from_config()`här filen från koden använder du . Den här koden läser in informationen från filen och ansluter till arbetsytan.

Du kan skapa konfigurationsfilen på tre sätt:

* **Använd [ws.write_config](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)**: för att skriva en *config.json-fil.* Filen innehåller konfigurationsinformationen för arbetsytan. Du kan ladda ner eller kopiera *config.json* till andra utvecklingsmiljöer.

* **Hämta filen**: Välj Hämta **config.json** i avsnittet **Översikt** på arbetsytan i [Azure-portalen.](https://ms.portal.azure.com)

     ![Azure Portal](./media/how-to-configure-environment/configure.png)

* **Skapa filen programmässigt:** I följande kodavsnitt ansluter du till en arbetsyta genom att ange prenumerations-ID, resursgrupp och arbetsytans namn. Sedan sparas arbetsytans konfiguration i filen:

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

    Den här koden skriver konfigurationsfilen till *filen .azureml/config.json.*

## <a name="next-steps"></a>Nästa steg

- [Träna en modell](tutorial-train-models-with-aml.md) på Azure Machine Learning med MNIST-datauppsättningen
- Visa azure [machine learning SDK för Python-referens](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
