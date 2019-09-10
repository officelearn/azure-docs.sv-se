---
title: Konfigurera en python-utvecklings miljö
titleSuffix: Azure Machine Learning service
description: 'Lär dig hur du konfigurerar en utvecklings miljö när du arbetar med tjänsten Azure Machine Learning. I den här artikeln får du lära dig hur du använder Conda-miljöer, skapar konfigurationsfiler och konfigurerar din egen molnbaserade Notebook-Server, Jupyter-anteckningsböcker, Azure Databricks, IDE: er, kod redigerare och Data Science Virtual Machine.'
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.topic: conceptual
ms.date: 07/31/2019
ms.custom: seodec18
ms.openlocfilehash: 39daff8be5ac072479463dc10c9041cda6b7b628
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2019
ms.locfileid: "70860571"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>Konfigurera en utvecklingsmiljö för Azure Machine Learning

I den här artikeln får du lära dig hur du konfigurerar en utvecklings miljö så att den fungerar med Azure Machine Learning-tjänsten. Azure Machine Learning-tjänsten är plattforms oberoende. Det enda hårda kravet för utvecklings miljön är python 3. En isolerad miljö som Anaconda eller virtuell miljö rekommenderas också.

I följande tabell visas varje utvecklings miljö som beskrivs i den här artikeln, tillsammans med-och-och nack delar.

| Miljö | Experter | Nackdelar |
| --- | --- | --- |
| [Molnbaserad VM-baserad virtuell dator](#notebookvm) | Enkelt sätt att komma igång. Hela SDK är redan installerat på den virtuella arbets ytan och själv studie kurser för bärbara datorer är förklonade och redo att köras. | Brist på kontroll över utvecklings miljön och beroenden. Ytterligare kostnader för virtuella Linux-datorer (VM kan stoppas när de inte används för att undvika avgifter). Se [pris information](https://azure.microsoft.com/pricing/details/virtual-machines/linux/). |
| [Lokal miljö](#local) | Fullständig kontroll över utvecklings miljön och beroenden. Kör med valfri build-verktyg, miljö eller IDE som du väljer. | Det tar längre tid att komma igång. Nödvändiga SDK-paket måste vara installerade och en miljö måste också installeras om du inte redan har ett. |
| [Azure Databricks](#aml-databricks) | Perfekt för att köra storskaliga arbets flöden för storskalig dator inlärning på den skalbara Apache Sparks plattformen. | Överkurs för experimentell maskin inlärning, eller mindre skalade experiment och arbets flöden. Ytterligare kostnader för Azure Databricks. Se [pris information](https://azure.microsoft.com/pricing/details/databricks/). |
| [Data Science Virtual Machine (DSVM)](#dsvm) | Precis som den molnbaserade Notebook VM (python och SDK är förinstallerade), men med ytterligare populära data vetenskaps-och maskin inlärnings verktyg förinstallerade. Lätt att skala och kombinera med andra anpassade verktyg och arbets flöden. | En långsammare upplevelse jämfört med den molnbaserade Notebook VM. |


Den här artikeln innehåller också ytterligare användnings tips för följande verktyg:

* [Jupyter-anteckningsböcker](#jupyter): Om du redan använder Jupyter Notebook har SDK viss extra utrustning som du bör installera.

* [Visual Studio Code](#vscode): Om du använder Visual Studio Code finns det några användbara tillägg som du kan installera.

## <a name="prerequisites"></a>Förutsättningar

En arbetsyta för Azure Machine Learning-tjänsten. Information om hur du skapar arbets ytan finns i [skapa en Azure Machine Learning service-arbetsyta](how-to-manage-workspace.md). En arbets yta är allt du behöver för att komma igång med din egen [molnbaserade Notebook-Server](#notebookvm), en [DSVM](#dsvm)eller [Azure Databricks](#aml-databricks).

Om du vill installera SDK-miljön för den [lokala datorn](#local), [Jupyter Notebook Server](#jupyter) eller [Visual Studio-kod](#vscode) som du också behöver:

- Antingen [Anaconda](https://www.anaconda.com/download/) -eller [Miniconda](https://conda.io/miniconda.html) -paket hanteraren.

- I Linux eller macOS behöver du bash-gränssnittet.

    > [!TIP]
    > Om du använder Linux eller macOS och använder ett annat skal än bash (till exempel zsh) kan du få fel när du kör vissa kommandon. Undvik problemet genom att använda den `bash` kommando för att starta en ny bash-gränssnitt och kör kommandon det.

- På Windows måste du Kommandotolken eller Anaconda kommandotolk (installerad som Anaconda och Miniconda).

## <a id="notebookvm"></a>Din egen molnbaserade Notebook VM

Den virtuella Notebook-datorn (för hands version) är en säker, molnbaserad Azure-arbetsstation som tillhandahåller data experter med en Jupyter Notebook-Server, JupyterLab och en helt för beredd ML-miljö.

Den virtuella Notebook-datorn är:

+ **Säker**. Eftersom VM-och Notebook-åtkomst skyddas med HTTPS och Azure Active Directory som standard kan IT-proffs enkelt använda enkel inloggning och andra säkerhetsfunktioner, till exempel Multi-Factor Authentication.

+ Förkonfigurerad. Den här fullständigt beredda python ML-miljön ritar sin härstamning från populära IaaS-Data Science VM och inkluderar:
  + Azure ML python SDK (senaste)
  + Automatisk konfiguration som fungerar med din arbets yta
  + En Jupyter Notebook-Server
  + JupyterLab Notebook IDE
  + Förkonfigurerade GPU-drivrutiner
  + Ett urval av ramverk för djup inlärning


  Om du är i kod innehåller den virtuella datorn självstudier och exempel som hjälper dig att utforska och lära dig hur du använder Azure Machine Learning-tjänsten. Exempel antecknings böckerna lagras i Azure Blob Storage-kontot på arbets ytan som gör att de kan delas mellan virtuella datorer. När det körs har de också åtkomst till data lager och beräknings resurser för din arbets yta.

+ **Enkel installation**: Skapa en när som helst i din Azure Machine Learning-arbetsyta. Ange ett namn och ange en typ av virtuell Azure-dator. Prova nu med den här [självstudien: Installations miljö och arbets](tutorial-1st-experiment-sdk-setup.md)yta.

+ **Anpassningsbar**. Även om det finns ett hanterat och säkert VM-erbjudande behåller du full till gång till maskin varu funktionerna och anpassar dem till ditt hjärta. Du kan till exempel snabbt skapa den senaste NVidia V100-baserade virtuella datorn för att utföra steg-för-steg-felsökning av den nya neurala-nätverks arkitekturen.

[Stoppa den virtuella Notebook](tutorial-1st-experiment-sdk-train.md#clean-up-resources)-datorn om du vill sluta debitera VM-kostnader för bärbara datorer. 

## <a id="dsvm"></a>Virtuell dator för datavetenskap

DSVM är en anpassad avbildning av en virtuell dator (VM). Det är utformat för data vetenskaps arbete som är förkonfigurerat med:

  - Paket som TensorFlow, PyTorch, Scikit-lära, XGBoost och Azure Machine Learning SDK
  - Populära verktyg för data vetenskap som Spark standalone och detalj nivå
  - Azure-verktyg som Azure CLI, AzCopy och Storage Explorer
  - Integrerade utvecklings miljöer (IDE: er) som Visual Studio Code och pycharm med
  - Jupyter Notebook-Server

Azure Machine Learning SDK fungerar antingen i Ubuntu-eller Windows-versionen av DSVM. Men om du endast planerar att använda DSVM som ett beräknings mål stöds endast Ubuntu.

Om du vill använda DSVM som utvecklings miljö gör du följande:

1. Skapa en DSVM i någon av följande miljöer:

    * Azure Portal:

        * [Skapa en Ubuntu-Data Science Virtual Machine](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)

        * [Skapa en Windows-Data Science Virtual Machine](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/provision-vm)

    * Azure CLI:

        > [!IMPORTANT]
        > * När du använder Azure CLI måste du först logga in på din Azure-prenumeration med hjälp `az login` av kommandot.
        >
        > * När du använder kommandona i det här steget måste du ange ett resurs grupp namn, ett namn för den virtuella datorn, ett användar namn och ett lösen ord.

        * Om du vill skapa en Ubuntu Data Science Virtual Machine använder du följande kommando:

            ```azurecli
            # create a Ubuntu DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            # If you need to create a new resource group use: "az group create --name YOUR-RESOURCE-GROUP-NAME --location YOUR-REGION (For example: westus2)"
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:linux-data-science-vm-ubuntu:linuxdsvmubuntu:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --generate-ssh-keys --authentication-type password
            ```

        * Om du vill skapa en Windows-Data Science Virtual Machine använder du följande kommando:

            ```azurecli
            # create a Windows Server 2016 DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:dsvm-windows:server-2016:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --authentication-type password
            ```

2. Azure Machine Learning SDK har redan installerats på DSVM. Använd någon av följande kommandon om du vill använda den Conda-miljö som innehåller SDK: N:

    * För Ubuntu-DSVM:

        ```shell
        conda activate py36
        ```

    * För Windows-DSVM:

        ```shell
        conda activate AzureML
        ```

1. Använd följande Python-kod för att kontrollera att du kan använda SDK: N och kontrollera vilken version:

    ```python
    import azureml.core
    print(azureml.core.VERSION)
    ```

1. Information om hur du konfigurerar DSVM till att använda din Azure Machine Learning service-arbetsyta finns i avsnittet [skapa en konfigurations fil för arbets yta](#workspace) .

Mer information finns i [Virtual Machines för data vetenskap](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/).

## <a id="local"></a>Lokal dator

När du använder en lokal dator (som också kan vara en virtuell fjärrdator) skapar du en Anaconda-miljö och installerar SDK: n genom att göra följande:

1. Hämta och installera [Anaconda](https://www.anaconda.com/distribution/#download-section) (python 3,7-versionen) om du inte redan har den.

1. Öppna en Anaconda-prompt och skapa en miljö med följande kommandon:

    Kör följande kommando för att skapa miljön.

    ```shell
    conda create -n myenv python=3.6.5
    ```

    Aktivera sedan miljön.

    ```shell
    conda activate myenv
    ```

    I det här exemplet skapas en miljö med python-3.6.5, men eventuella speciella under versioner kan väljas. SDK-kompatibiliteten kanske inte garanteras med vissa huvud versioner (3,5 + rekommenderas) och vi rekommenderar att du testar en annan version/under version i din Anaconda-miljö om du stöter på fel. Det tar flera minuter att skapa miljön medan komponenter och paket laddas ned.

1. Kör följande kommandon i din nya miljö för att aktivera miljödefinierade ipython-kärnor. Detta säkerställer förväntat beteende för kernel-och paket import när du arbetar med Jupyter-anteckningsböcker i Anaconda-miljöer:

    ```shell
    conda install notebook ipykernel
    ```

    Kör sedan följande kommando för att skapa kärnan:

    ```shell
    ipython kernel install --user
    ```

1. Använd följande kommandon för att installera paket:

    Det här kommandot installerar bas Azure Machine Learning SDK med Notebook och automl extra. Det `automl` extra är en stor installation och kan tas bort från hakparenteserna om du inte tänker köra automatiserade maskin inlärnings experiment. Det `automl` extra inkluderar även Azure Machine Learning data prep SDK som standard som ett beroende.

    ```shell
    pip install azureml-sdk[notebooks,automl]
    ```

   > [!NOTE]
   > * Om du får ett meddelande om att PyYAML inte kan avinstalleras använder du följande kommando i stället:
   >
   >   `pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML`
   >
   > * Från och med macOS Catalina är zsh (Z Shell) standard gränssnittet för inloggning och det interaktiva gränssnittet. I zsh använder du följande kommando som utrymningr hakparenteser med "\\" (omvänt snedstreck):
   >
   >   `pip install --upgrade azureml-sdk\[notebooks,automl\]`


   Det tar flera minuter att installera SDK: n. Mer information om installations alternativ finns i [installations guiden](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)för.

1. Installera andra paket för Machine Learning-experimentering.

    Använd något av följande kommandon och Ersätt  *\<nytt paket >* med det paket som du vill installera. Att installera paket `conda install` via kräver att paketet är en del av de aktuella kanalerna (nya kanaler kan läggas till i Anaconda-molnet).

    ```shell
    conda install <new package>
    ```

    Du kan också installera paket via `pip`.

    ```shell
    pip install <new package>
    ```

### <a id="jupyter"></a>Jupyter-anteckningsböcker

Jupyter Notebooks är en del av den [Jupyter projekt](https://jupyter.org/). De ger en interaktiv kodningserfarenheter där du skapar dokument som blandar live-koden med löpande text och grafik. Jupyter-anteckningsböcker är också ett bra sätt att dela dina resultat med andra, eftersom du kan spara utdata från kod avsnitten i dokumentet. Du kan installera Jupyter Notebooks på en rad olika plattformar.

Proceduren i avsnittet [lokal dator](#local) installerar nödvändiga komponenter för att köra Jupyter-anteckningsböcker i en Anaconda-miljö. Gör så här om du vill aktivera dessa komponenter i Jupyter Notebooks miljön:

1. Öppna en Anaconda-prompt och aktivera din miljö.

    ```shell
    conda activate myenv
    ```
    
1. Klona [GitHub](https://aka.ms/aml-notebooks) -lagringsplatsen för en uppsättning exempel antecknings böcker.

    ```CLI
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Starta Jupyter Notebook-servern med följande kommando:

    ```shell
    jupyter notebook
    ```

1. Verifiera att Jupyter Notebook kan använda SDK: n, skapa en **ny** antecknings bok, välja **python 3** som kernel och kör sedan följande kommando i en Notebook-cell:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. Om du får problem med att importera moduler och `ModuleNotFoundError`ta emot en kontrollerar du att Jupyter-kärnan är ansluten till rätt sökväg för din miljö genom att köra följande kod i en Notebook-cell.

    ```python
    import sys
    sys.path
    ```
    
1. Om du vill konfigurera Jupyter Notebook att använda arbets ytan Azure Machine Learnings tjänst, går du till avsnittet [skapa en konfigurations fil för arbets yta](#workspace) .


### <a id="vscode"></a>Visual Studio Code

Visual Studio Code är en plattformsoberoende Kodredigerare. Den förlitar sig på en lokal installation av Python 3 och Conda för Python-support, men ger ytterligare verktyg för att arbeta med AI. Den innehåller också stöd för att välja Conda-miljö från Kodredigeraren.

Om du vill använda Visual Studio Code för utveckling gör du följande:

1. Information om hur du använder Visual Studio Code för python-utveckling finns i [Kom igång med python i VSCode](https://code.visualstudio.com/docs/python/python-tutorial).

1. Om du vill välja Conda-miljön öppnar du VS Code och väljer sedan Ctrl + Shift + P (Linux och Windows) eller Kommando + Skift + P (Mac).
    __Kommandots lastpall__ öppnas.

1. Ange __python: Välj tolken__och välj sedan Conda-miljön.

1. Om du vill verifiera att du kan använda SDK skapar du och kör sedan en ny python-fil (. py) som innehåller följande kod:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. Information om hur du installerar Azure Machine Learning-tillägget för Visual Studio Code finns i [Tools för AI](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai).

    Mer information finns i [använda Azure Machine Learning för Visual Studio Code](how-to-vscode-tools.md).

<a name="aml-databricks"></a>

## <a name="azure-databricks"></a>Azure Databricks
Azure Databricks är en Apache Spark-baserad miljö i Azure-molnet. Den ger en samarbets Notebook-baserad miljö med processor-eller GPU-baserade beräknings kluster.

Hur Azure Databricks fungerar med Azure Machine Learning-tjänsten:
+ Du kan träna en modell med Spark-MLlib och distribuera modellen till ACI/AKS inifrån Azure Databricks.
+ Du kan också använda [automatiska maskin inlärnings](concept-automated-ml.md) funktioner i en särskild Azure ml SDK med Azure Databricks.
+ Du kan använda Azure Databricks som ett beräknings mål från en [Azure Machine Learning pipeline](concept-ml-pipelines.md).

### <a name="set-up-your-databricks-cluster"></a>Konfigurera ditt Databricks-kluster

Skapa ett [Databricks-kluster](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal). Vissa inställningar gäller endast om du installerar SDK för automatisk maskin inlärning på Databricks.
**Det tar några minuter att skapa klustret.**

Använd de här inställningarna:

| Inställning |Gäller| Value |
|----|---|---|
| Klusternamn |alltid| yourclustername |
| Databricks Runtime |alltid| En icke ML-körning (icke-ML 4. x, 5. x) |
| Python-version |alltid| 3 |
| Arbetare |alltid| 2 eller senare |
| VM-typer för arbetsnoder <br>(fastställer Max antal samtidiga iterationer) |Automatisk ML<br>Only| Minnesoptimerade VM-prioritet |
| Aktivera automatisk skalning |Automatisk ML<br>Only| Avmarkera |

Vänta tills klustret körs innan du fortsätter.

### <a name="install-the-correct-sdk-into-a-databricks-library"></a>Installera rätt SDK i ett Databricks-bibliotek
När klustret har körts skapar du [ett bibliotek](https://docs.databricks.com/user-guide/libraries.html#create-a-library) för att bifoga lämpligt Azure Machine Learning SDK-paket till klustret.

1. Välj **endast ett** alternativ (ingen annan SDK-installation stöds)

   |Tillägg&nbsp;för&nbsp;SDK-paket|Source|PyPi&nbsp;Name&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
   |----|---|---|
   |För Databricks| Överför python-ägg eller PyPI | azureml-sdk[databricks]|
   |För Databricks – med-<br> automatiserade ML-funktioner| Överför python-ägg eller PyPI | azureml-sdk[automl_databricks]|

   > [!Warning]
   > Inga andra SDK-tillägg kan installeras. Välj endast ett av de föregående alternativen [databricks] eller [automl_databricks].

   * Välj inte **Anslut automatiskt till alla kluster**.
   * Välj **Anslut** bredvid klustrets namn.

1. Övervaka fel tills status ändras till **kopplat**, vilket kan ta flera minuter.  Om det här steget Miss lyckas kontrollerar du följande:

   Försök att starta om klustret genom att:
   1. Välj **kluster**i den vänstra rutan.
   1. I tabellen väljer du ditt kluster namn.
   1. På fliken **bibliotek** väljer du **starta om**.

   Tänk också på följande:
   + När du använder Azure Databricks lägger du till följande parametrar i AutoML config:
       1. ```max_concurrent_iterations```baseras på antalet arbetsnoder i klustret.
        2. ```spark_context=sc```baseras på standard-Spark-kontexten.
   + Om du har en gammal SDK-version avmarkerar du den från klustrets installerade libs och flyttar till pappers korgen. Installera den nya SDK-versionen och starta om klustret. Om det uppstår ett problem efter omstarten, tar du bort och återansluter klustret.

Om installationen lyckades bör det importerade biblioteket se ut ungefär så här:

SDK för Databricks **_utan_** automatiserad Machine ![Learning Azure Machine Learning SDK för Databricks](./media/how-to-configure-environment/amlsdk-withoutautoml.jpg)

SDK för Databricks **med** automatiserad ![Machine Learning SDK med automatisk maskin inlärning installerad på Databricks](./media/how-to-configure-environment/automlonadb.jpg)

### <a name="start-exploring"></a>Börja utforska

Prova:
+ Hämta [Notebook-arkivmappen](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks/Databricks_AMLSDK_1-4_6.dbc) för Azure Databricks/Azure Machine Learning SDK och [Importera Arkiv filen](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-an-archive) till ditt Databricks-kluster.
  Även om många exempel antecknings böcker är tillgängliga **fungerar bara [dessa exempel antecknings böcker](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks) med Azure Databricks.**

+ Lär dig hur du [skapar en pipeline med Databricks som inlärnings beräkning](how-to-create-your-first-pipeline.md).

## <a id="workspace"></a>Skapa en konfigurationsfil för arbetsyta

Konfigurations filen för arbets ytan är en JSON-fil som talar om för SDK hur du kommunicerar med din Azure Machine Learning-arbetsyta. Filen heter *config. JSON*och har följande format:

```json
{
    "subscription_id": "<subscription-id>",
    "resource_group": "<resource-group>",
    "workspace_name": "<workspace-name>"
}
```

Den här JSON-filen måste finnas i katalog strukturen som innehåller dina Python-skript eller Jupyter-anteckningsböcker. Det kan finnas i samma katalog, i en under katalog med namnet *. azureml*eller i en överordnad katalog.

Om du vill använda den här filen från din kod använder `ws=Workspace.from_config()`. Den här koden läser in informationen från filen och ansluter till din arbetsyta.

Du kan skapa konfigurations filen på tre sätt:

* **Använd [ws. write_config](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)** : för att skriva en *config. JSON* -fil. Filen innehåller konfigurations informationen för din arbets yta. Du kan ladda ned eller kopiera *config. JSON* till andra utvecklings miljöer.

* **Hämta filen**: I [Azure Portal](https://ms.portal.azure.com)väljer du **Hämta config. JSON** från översikts avsnittet på arbets ytan.

     ![Azure Portal](./media/how-to-configure-environment/configure.png)

* **Skapa filen program mässigt**: I följande kodfragment ansluter du till en arbets yta genom att ange prenumerations-ID, resurs grupp och arbets ytans namn. Sedan sparas arbets ytans konfiguration till filen:

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

    Den här koden skriver konfigurations filen till *. azureml-/config. JSON-* filen.


## <a name="next-steps"></a>Nästa steg

- [Träna en modell](tutorial-train-models-with-aml.md) på Azure Machine Learning med MNIST-datauppsättningen
- Visa [Azure Machine Learning SDK för python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) -referens