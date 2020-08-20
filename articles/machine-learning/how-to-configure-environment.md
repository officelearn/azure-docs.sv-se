---
title: Konfigurera en python-utvecklings miljö
titleSuffix: Azure Machine Learning
description: 'Lär dig att konfigurera utvecklings miljön för Azure Machine Learning. Använd Conda-miljöer, skapa konfigurationsfiler och konfigurera din egen molnbaserade Notebook-Server, Jupyter-anteckningsböcker, Azure Databricks, IDE: er, kod redigerare och Data Science Virtual Machine.'
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.date: 12/27/2019
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 1753373b360a78918682b5f6102dcc896e2d90c3
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2020
ms.locfileid: "88652652"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>Konfigurera en utvecklings miljö för Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här artikeln får du lära dig hur du konfigurerar en utvecklings miljö så att den fungerar med Azure Machine Learning. Azure Machine Learning är plattforms oberoende. Det enda hårda kravet för utvecklings miljön är python 3. En isolerad miljö som Anaconda eller virtuell miljö rekommenderas också.

I följande tabell visas varje utvecklings miljö som beskrivs i den här artikeln, tillsammans med-och-och nack delar.

| Miljö | Fördelar | Nackdelar |
| --- | --- | --- |
| [Molnbaserad Azure Machine Learning beräknings instans (för hands version)](#compute-instance) | Enkelt sätt att komma igång. Hela SDK är redan installerat på den virtuella arbets ytan och själv studie kurser för bärbara datorer är förklonade och redo att köras. | Brist på kontroll över utvecklings miljön och beroenden. Ytterligare kostnader för virtuella Linux-datorer (VM kan stoppas när de inte används för att undvika avgifter). Se [pris information](https://azure.microsoft.com/pricing/details/virtual-machines/linux/). |
| [Lokal miljö](#local) | Fullständig kontroll över utvecklings miljön och beroenden. Kör med valfri build-verktyg, miljö eller IDE som du väljer. | Det tar längre tid att komma igång. Nödvändiga SDK-paket måste vara installerade och en miljö måste också installeras om du inte redan har ett. |
| [Azure Databricks](#aml-databricks) | Perfekt för att köra storskaliga arbets flöden för storskalig dator inlärning på den skalbara Apache Sparks plattformen. | Överkurs för experimentell maskin inlärning, eller mindre skalade experiment och arbets flöden. Ytterligare kostnader för Azure Databricks. Se [pris information](https://azure.microsoft.com/pricing/details/databricks/). |
| [Data Science Virtual Machine (DSVM)](#dsvm) | Precis som den molnbaserade Compute-instansen (python och SDK är förinstallerade), men med ytterligare populära data vetenskaps-och maskin inlärnings verktyg förinstallerade. Lätt att skala och kombinera med andra anpassade verktyg och arbets flöden. | En långsammare upplevelse som är igång jämfört med den molnbaserade beräknings instansen. |

Den här artikeln innehåller också ytterligare användnings tips för följande verktyg:

* [Jupyter-anteckningsböcker](#jupyter): om du redan använder den Jupyter Notebook, har SDK några extra tillägg som du bör installera.

* [Visual Studio Code](#vscode): om du använder Visual Studio code innehåller [Azure Machine Learning tillägget](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai) omfattande språk stöd för python samt funktioner för att arbeta med Azure Machine Learning mycket bekvämare och produktivitet.

## <a name="prerequisites"></a>Förutsättningar

En Azure Machine Learning-arbetsyta. Information om hur du skapar arbets ytan finns i [skapa en Azure Machine Learning arbets yta](how-to-manage-workspace.md). En arbets yta är allt du behöver för att komma igång med din egen [molnbaserade Notebook-Server](#compute-instance), en [DSVM](#dsvm)eller [Azure Databricks](#aml-databricks).

Om du vill installera SDK-miljön för den [lokala datorn](#local), [Jupyter Notebook Server](#jupyter) eller [Visual Studio-kod](#vscode) som du också behöver:

- Antingen [Anaconda](https://www.anaconda.com/download/) -eller [Miniconda](https://conda.io/miniconda.html) -paket hanteraren.

- I Linux eller macOS behöver du bash-gränssnittet.

    > [!TIP]
    > Om du använder Linux eller macOS och använder ett annat skal än bash (till exempel zsh) kan du få fel när du kör vissa kommandon. Undvik det här problemet genom `bash` att använda kommandot för att starta ett nytt bash-gränssnitt och köra kommandona där.

- I Windows behöver du kommando tolken eller Anaconda-prompten (installeras av AnaConDa och Miniconda).

## <a name="your-own-cloud-based-compute-instance"></a><a id="compute-instance"></a>Din egen molnbaserade beräknings instans

Azure Machine Learning [Compute-instansen (förhands granskning)](concept-compute-instance.md) är en säker, molnbaserad Azure-arbetsstation som tillhandahåller data experter med en Jupyter Notebook-Server, JupyterLab och en helt för beredd ml-miljö.

Det finns inget att installera eller konfigurera för en beräknings instans.  Skapa en när som helst i din Azure Machine Learning-arbetsyta. Ange ett namn och ange en typ av virtuell Azure-dator. Prova nu med den här [självstudien: installations miljö och arbets yta](tutorial-1st-experiment-sdk-setup.md).

Om du vill veta mer om beräknings instanser, inklusive hur du installerar paket, se [Compute instances](concept-compute-instance.md).

[Stoppa beräknings instansen](tutorial-1st-experiment-sdk-train.md#clean-up-resources)om du vill sluta att debiteras för beräknings kostnader.

## <a name="data-science-virtual-machine"></a><a id="dsvm"></a>Virtuell dator för datavetenskap

DSVM är en anpassad avbildning av en virtuell dator (VM). Det är utformat för data vetenskaps arbete som är förkonfigurerat med:

  - Paket som TensorFlow, PyTorch, Scikit-lära, XGBoost och Azure Machine Learning SDK
  - Populära verktyg för data vetenskap som Spark standalone och detalj nivå
  - Azure-verktyg som Azure CLI, AzCopy och Storage Explorer
  - Integrerade utvecklings miljöer (IDE: er) som Visual Studio Code och pycharm med
  - Jupyter Notebook Server

Azure Machine Learning SDK fungerar antingen i Ubuntu-eller Windows-versionen av DSVM. Men om du endast planerar att använda DSVM som ett beräknings mål stöds endast Ubuntu.

Så här använder du DSVM som utvecklings miljö:

1. Skapa en DSVM i någon av följande miljöer:

    * Azure Portal:

        * [Skapa en Data Science Virtual Machine med Ubuntu](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)

        * [Skapa en Data Science Virtual Machine med Windows](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/provision-vm)

    * Azure CLI:

        > [!IMPORTANT]
        > * När du använder Azure CLI måste du först logga in på din Azure-prenumeration med hjälp av `az login` kommandot.
        >
        > * När du använder kommandona i det här steget måste du ange ett resurs grupp namn, ett namn för den virtuella datorn, ett användar namn och ett lösen ord.

        * Om du vill skapa en Ubuntu Data Science Virtual Machine använder du följande kommando:

            ```azurecli-interactive
            # create a Ubuntu DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            # If you need to create a new resource group use: "az group create --name YOUR-RESOURCE-GROUP-NAME --location YOUR-REGION (For example: westus2)"
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:linux-data-science-vm-ubuntu:linuxdsvmubuntu:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --generate-ssh-keys --authentication-type password
            ```

        * Om du vill skapa en Windows-Data Science Virtual Machine använder du följande kommando:

            ```azurecli-interactive
            # create a Windows Server 2016 DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:dsvm-windows:server-2016:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --authentication-type password
            ```

2. Azure Machine Learning SDK har redan installerats på DSVM. Använd något av följande kommandon om du vill använda Conda-miljön som innehåller SDK:

    * För Ubuntu-DSVM:

        ```bash
        conda activate py36
        ```

    * För Windows-DSVM:

        ```bash
        conda activate AzureML
        ```

1. Kontrol lera att du har åtkomst till SDK och kontrol lera versionen med följande python-kod:

    ```python
    import azureml.core
    print(azureml.core.VERSION)
    ```

1. Information om hur du konfigurerar DSVM så att den använder din Azure Machine Learning arbets yta finns i avsnittet [skapa en konfigurations fil för arbets ytor](#workspace) .

Mer information finns i [Virtual Machines för data vetenskap](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/).

## <a name="local-computer"></a><a id="local"></a>Lokal dator

När du använder en lokal dator (som också kan vara en virtuell fjärrdator) skapar du en Anaconda-miljö och installerar SDK. Här är ett exempel:

1. Hämta och installera [Anaconda](https://www.anaconda.com/distribution/#download-section) (python 3,7-versionen) om du inte redan har den.

1. Öppna en Anaconda-prompt och skapa en miljö med följande kommandon:

    Kör följande kommando för att skapa miljön.

    ```bash
    conda create -n myenv python=3.7.7
    ```

    Aktivera sedan miljön.

    ```bash
    conda activate myenv
    ```

    I det här exemplet skapas en miljö med python-3.7.7, men eventuella speciella under versioner kan väljas. SDK-kompatibiliteten kanske inte garanteras med vissa huvud versioner (3,5 + rekommenderas) och vi rekommenderar att du testar en annan version/under version i din Anaconda-miljö om du stöter på fel. Det tar flera minuter att skapa miljön medan komponenter och paket laddas ned.

1. Kör följande kommandon i din nya miljö för att aktivera miljödefinierade IPython-kärnor. Detta säkerställer förväntat beteende för kernel-och paket import när du arbetar med Jupyter-anteckningsböcker i Anaconda-miljöer:

    ```bash
    conda install notebook ipykernel
    ```

    Kör sedan följande kommando för att skapa kärnan:

    ```bash
    ipython kernel install --user --name myenv --display-name "Python (myenv)"
    ```

1. Använd följande kommandon för att installera paket:

    Det här kommandot installerar bas Azure Machine Learning SDK med antecknings boken och `automl` tillägg. Det `automl` extra är en stor installation och kan tas bort från hakparenteserna om du inte tänker köra automatiserade maskin inlärnings experiment. Det `automl` extra inkluderar även Azure Machine Learning data prep SDK som standard som ett beroende.

    ```bash
    pip install azureml-sdk[notebooks,automl]
    ```

   > [!NOTE]
   > * Om du får ett meddelande om att PyYAML inte kan avinstalleras använder du följande kommando i stället:
   >
   >   `pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML`
   >
   > * Från och med macOS Catalina är zsh (Z Shell) standard gränssnittet för inloggning och det interaktiva gränssnittet. I zsh använder du följande kommando som utrymningr hakparenteser med " \\ " (omvänt snedstreck):
   >
   >   `pip install --upgrade azureml-sdk\[notebooks,automl\]`

   Det tar flera minuter att installera SDK: n. Mer information om installations alternativ finns i [installations guiden](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)för.

1. Installera andra paket för Machine Learning-experimentering.

    Använd något av följande kommandon och Ersätt *\<new package>* med det paket som du vill installera. Att installera paket via `conda install` kräver att paketet är en del av de aktuella kanalerna (nya kanaler kan läggas till i Anaconda-molnet).

    ```bash
    conda install <new package>
    ```

    Du kan också installera paket via `pip` .

    ```bash
    pip install <new package>
    ```

### <a name="jupyter-notebooks"></a><a id="jupyter"></a>Jupyter Notebook

Jupyter-anteckningsböcker är en del av [Jupyter-projektet](https://jupyter.org/). De ger en interaktiv kodnings upplevelse där du skapar dokument som blandar direktsänd kod med text och grafik. Jupyter-anteckningsböcker är också ett bra sätt att dela dina resultat med andra, eftersom du kan spara utdata från kod avsnitten i dokumentet. Du kan installera Jupyter-anteckningsböcker på olika plattformar.

Proceduren i avsnittet [lokal dator](#local) installerar nödvändiga komponenter för att köra Jupyter-anteckningsböcker i en Anaconda-miljö.

Så här aktiverar du dessa komponenter i Jupyter Notebooks miljön:

1. Öppna en Anaconda-prompt och aktivera din miljö.

    ```bash
    conda activate myenv
    ```

1. Klona [GitHub-lagringsplatsen](https://github.com/Azure/MachineLearningNotebooks) för en uppsättning exempel antecknings böcker.

    ```bash
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Starta Jupyter Notebook-servern med följande kommando:

    ```bash
    jupyter notebook
    ```

1. Verifiera att Jupyter Notebook kan använda SDK: n, skapa en **ny** antecknings bok, välja **python 3** som kernel och kör sedan följande kommando i en Notebook-cell:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. Om du får problem med att importera moduler och ta emot en kontrollerar du att `ModuleNotFoundError` Jupyter-kärnan är ansluten till rätt sökväg för din miljö genom att köra följande kod i en Notebook-cell.

    ```python
    import sys
    sys.path
    ```

1. Om du vill konfigurera Jupyter Notebook att använda arbets ytan Azure Machine Learning går du till avsnittet [skapa en konfigurations fil för arbets yta](#workspace) .

### <a name="visual-studio-code"></a><a id="vscode"></a>Visual Studio-koden

Visual Studio Code är en mycket populär kod redigerare för olika plattformar som stöder en omfattande uppsättning programmeringsspråk och verktyg via tillägg som är tillgängliga i [Visual Studio Marketplace](https://marketplace.visualstudio.com/vscode). [Azure Machine Learning-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai) installerar [python-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-python.python) för kodning i alla typer av python-miljöer (Virtual, Anaconda osv.). Dessutom innehåller den praktiska funktioner för att arbeta med Azure Machine Learning resurser och köra Azure Machine Learning experiment utan att lämna Visual Studio Code.

Så här använder du Visual Studio Code för utveckling:

1. Installera Azure Machine Learning-tillägget för Visual Studio Code finns i [Azure Machine Learning](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai).

    Mer information finns i [använda Azure Machine Learning för Visual Studio Code](tutorial-setup-vscode-extension.md).

1. Lär dig hur du använder Visual Studio Code för alla typer av python-utveckling, se [Kom igång med python i VSCode](https://code.visualstudio.com/docs/python/python-tutorial).

    - Om du vill välja SDK python-miljön som innehåller SDK öppnar du VS Code och väljer sedan Ctrl + Shift + P (Linux och Windows) eller Kommando + Skift + P (Mac).
        - __Kommando paletten__ öppnas.

    - Ange __python: Välj tolken__och välj sedan lämplig miljö

1. Du kan kontrol lera att du kan använda SDK genom att skapa en ny python-fil (. py) som innehåller följande kod:

    ```python
    #%%
    import azureml.core
    azureml.core.VERSION
    ```
    Kör den här koden genom att klicka på "kör cell" CodeLens eller tryck på Skift-Enter.
<a name="aml-databricks"></a>

## <a name="azure-databricks"></a>Azure Databricks
Azure Databricks är en Apache Spark-baserad miljö i Azure-molnet. Den ger en samarbets Notebook-baserad miljö med processor-eller GPU-baserade beräknings kluster.

Hur Azure Databricks fungerar med Azure Machine Learning:
+ Du kan träna en modell med Spark-MLlib och distribuera modellen till ACI/AKS inifrån Azure Databricks.
+ Du kan också använda [automatiska maskin inlärnings](concept-automated-ml.md) funktioner i en särskild Azure ml SDK med Azure Databricks.
+ Du kan använda Azure Databricks som ett beräknings mål från en [Azure Machine Learning pipeline](concept-ml-pipelines.md).

### <a name="set-up-your-databricks-cluster"></a>Konfigurera ditt Databricks-kluster

Skapa ett [Databricks-kluster](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal). Vissa inställningar gäller endast om du installerar SDK för automatisk maskin inlärning på Databricks.
**Det tar några minuter att skapa klustret.**

Använd de här inställningarna:

| Inställning |Gäller för| Värde |
|----|---|---|
| Klusternamn |alltid| yourclustername |
| Databricks Runtime |alltid|Non-ML runtime 6,5 (Scala 2,11, Spark 2.4.3) |
| Python-version |alltid| 3 |
| Arbetare |alltid| 2 eller senare |
| VM-typer för arbetsnoder <br>(fastställer Max antal samtidiga iterationer) |Automatisk ML<br>Only| Minnesoptimerade VM-prioritet |
| Aktivera automatisk skalning |Automatisk ML<br>Only| Avmarkera |

Vänta tills klustret körs innan du fortsätter.

### <a name="install-the-correct-sdk-into-a-databricks-library"></a>Installera rätt SDK i ett Databricks-bibliotek
När klustret har körts skapar du [ett bibliotek](https://docs.databricks.com/user-guide/libraries.html#create-a-library) för att bifoga lämpligt Azure Machine Learning SDK-paket till klustret.

1. Högerklicka på den aktuella arbetsyte-mappen där du vill lagra biblioteket. Välj **skapa**  >  **bibliotek**.

1. Välj **endast ett** alternativ (ingen annan SDK-installation stöds)

   |Tillägg för SDK- &nbsp; paket &nbsp;|Källa|PyPi &nbsp; namn&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
   |----|---|---|
   |För Databricks| Överför python-ägg eller PyPI | azureml – SDK [databricks]|
   |För Databricks – med-<br> automatiserade ML-funktioner| Överför python-ägg eller PyPI | azureml – SDK [automl]|

   > [!Warning]
   > Inga andra SDK-tillägg kan installeras. Välj endast ett av de föregående alternativen [databricks] eller [automl].

   * Välj inte **Anslut automatiskt till alla kluster**.
   * Välj  **Anslut** bredvid klustrets namn.

1. Övervaka fel tills status ändras till **kopplat**, vilket kan ta flera minuter.  Om det här steget Miss lyckas:

   Försök att starta om klustret genom att:
   1. Välj **kluster**i den vänstra rutan.
   1. I tabellen väljer du ditt kluster namn.
   1. På fliken **bibliotek** väljer du **starta om**.

   Tänk också på följande:
   + När du använder Azure Databricks lägger du till följande parametrar i AutoML config:
       1. ```max_concurrent_iterations``` baseras på antalet arbetsnoder i klustret.
        2. ```spark_context=sc``` baseras på standard-Spark-kontexten.
   + Om du har en gammal SDK-version avmarkerar du den från klustrets installerade libs och flyttar till pappers korgen. Installera den nya SDK-versionen och starta om klustret. Om det uppstår ett problem efter omstarten, tar du bort och återansluter klustret.

Om installationen lyckades bör det importerade biblioteket se ut ungefär så här:

SDK för Databricks **_utan_** automatiserad machine Learning ![ Azure Machine Learning SDK för Databricks](./media/how-to-configure-environment/amlsdk-withoutautoml.jpg)

SDK för Databricks **med** automatiserad Machine Learning ![ SDK med automatisk maskin inlärning installerad på Databricks](./media/how-to-configure-environment/automlonadb.png)

### <a name="start-exploring"></a>Börja utforska

Prova:
+ Även om många exempel antecknings böcker är tillgängliga **fungerar bara [dessa exempel antecknings böcker](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks) med Azure Databricks.**

+ Importera de här exemplen direkt från din arbets yta. Se nedan: ![ Välj import ](./media/how-to-configure-environment/azure-db-screenshot.png)
 ![ import panel](./media/how-to-configure-environment/azure-db-import.png)

+ Lär dig hur du [skapar en pipeline med Databricks som inlärnings beräkning](how-to-create-your-first-pipeline.md).

## <a name="create-a-workspace-configuration-file"></a><a id="workspace"></a>Skapa en konfigurations fil för arbets ytor

Konfigurations filen för arbets ytan är en JSON-fil som talar om för SDK hur du kommunicerar med din Azure Machine Learning-arbetsyta. Filen heter *config.jspå*och har följande format:

```json
{
    "subscription_id": "<subscription-id>",
    "resource_group": "<resource-group>",
    "workspace_name": "<workspace-name>"
}
```

Den här JSON-filen måste finnas i katalog strukturen som innehåller dina Python-skript eller Jupyter-anteckningsböcker. Det kan finnas i samma katalog, i en under katalog med namnet *. azureml*eller i en överordnad katalog.

Använd om du vill använda den här filen från din kod `ws=Workspace.from_config()` . Den här koden läser in informationen från filen och ansluter till din arbets yta.

Du kan skapa konfigurations filen på tre sätt:

* **Använd  [ws. write_config](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)**: för att skriva en *config.jspå* en fil. Filen innehåller konfigurations informationen för din arbets yta. Du kan ladda ned eller kopiera *config.js* till andra utvecklings miljöer.

* **Hämta filen**: i [Azure Portal](https://ms.portal.azure.com)väljer du  **Hämta config.jspå** från **översikts** avsnittet på din arbets yta.

     ![Azure Portal](./media/how-to-configure-environment/configure.png)

* **Skapa filen program mässigt**: i följande kodfragment ansluter du till en arbets yta genom att ange PRENUMERATIONS-ID, resurs grupp och arbets ytans namn. Sedan sparas arbets ytans konfiguration till filen:

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

    Den här koden skriver konfigurations filen till filen *. azureml/config.jspå* filen.

## <a name="next-steps"></a>Nästa steg

- [Träna en modell](tutorial-train-models-with-aml.md) på Azure Machine Learning med MNIST-datauppsättningen
- Visa [Azure Machine Learning SDK för python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) -referens
