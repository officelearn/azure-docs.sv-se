---
title: Konfigurera utvecklings miljö | Python
titleSuffix: Azure Machine Learning
description: 'Lär dig hur du konfigurerar en python-utvecklings miljö för Azure Machine Learning. Använd Conda-miljöer, skapa konfigurationsfiler och konfigurera din egen molnbaserade Notebook-Server, Jupyter-anteckningsböcker, Azure Databricks, IDE: er, kod redigerare och Data Science Virtual Machine.'
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.date: 09/30/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperfq1, devx-track-azurecli
ms.openlocfilehash: 7e189885fbf7befcaea3f63148a42c81dc1da03e
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93320487"
---
# <a name="set-up-a-development-environment-for-azure-machine-learning"></a>Konfigurera en utvecklings miljö för Azure Machine Learning

Lär dig hur du konfigurerar en python-utvecklings miljö för Azure Machine Learning.

I följande tabell visas varje utvecklings miljö som beskrivs i den här artikeln, tillsammans med-och-och nack delar.

| Miljö | Fördelar | Nackdelar |
| --- | --- | --- |
| [Lokal miljö](#local) | Fullständig kontroll över utvecklings miljön och beroenden. Kör med valfri build-verktyg, miljö eller IDE som du väljer. | Det tar längre tid att komma igång. Nödvändiga SDK-paket måste vara installerade och en miljö måste också installeras om du inte redan har ett. |
| [Azure Machine Learning-beräkningsinstans](#compute-instance) | Enkelt sätt att komma igång. Hela SDK är redan installerat på den virtuella arbets ytan och själv studie kurser för bärbara datorer är förklonade och redo att köras. | Brist på kontroll över utvecklings miljön och beroenden. Ytterligare kostnader för virtuella Linux-datorer (VM kan stoppas när de inte används för att undvika avgifter). Se [pris information](https://azure.microsoft.com/pricing/details/virtual-machines/linux/). |
| [Azure Databricks](#aml-databricks) | Perfekt för att köra storskaliga arbets flöden för storskalig dator inlärning på den skalbara Apache Sparks plattformen. | Överkurs för experimentell maskin inlärning, eller mindre skalade experiment och arbets flöden. Ytterligare kostnader för Azure Databricks. Se [pris information](https://azure.microsoft.com/pricing/details/databricks/). |
| [Data Science Virtual Machine (DSVM)](#dsvm) | Precis som den molnbaserade Compute-instansen (python och SDK är förinstallerade), men med ytterligare populära data vetenskaps-och maskin inlärnings verktyg förinstallerade. Lätt att skala och kombinera med andra anpassade verktyg och arbets flöden. | En långsammare upplevelse som är igång jämfört med den molnbaserade beräknings instansen. |

Den här artikeln innehåller också ytterligare användnings tips för följande verktyg:

* Jupyter Notebooks: om du redan använder Jupyter notebook-datorer, har SDK en del tillägg som du bör installera.

* Visual Studio Code: om du använder Visual Studio Code innehåller [Azure Machine Learning tillägget](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai) omfattande språk stöd för python samt funktioner för att arbeta med Azure Machine Learning mycket bekvämare och produktivitet.

## <a name="prerequisites"></a>Förutsättningar

* Azure Machine Learning arbets yta. Om du inte har en kan du skapa en Azure Machine Learning arbets yta via [Azure Portal](how-to-manage-workspace.md), [Azure CLI](how-to-manage-workspace-cli.md#create-a-workspace)och [Azure Resource Manager mallar](how-to-create-workspace-template.md).

### <a name="local-and-dsvm-only-create-a-workspace-configuration-file"></a><a id="workspace"></a> (Endast lokal och DSVM) Skapa en konfigurations fil för arbets ytor

Konfigurations filen för arbets ytan är en JSON-fil som talar om för SDK hur du kommunicerar med din Azure Machine Learning-arbetsyta. Filen heter *config.jspå* och har följande format:

```json
{
    "subscription_id": "<subscription-id>",
    "resource_group": "<resource-group>",
    "workspace_name": "<workspace-name>"
}
```

Den här JSON-filen måste finnas i katalog strukturen som innehåller dina Python-skript eller Jupyter-anteckningsböcker. Det kan finnas i samma katalog, i en under katalog med namnet *. azureml* eller i en överordnad katalog.

Använd-metoden för att använda den här filen från din kod [`Workspace.from_config`](/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#from-config-path-none--auth-none---logger-none---file-name-none-&preserve-view=true) . Den här koden läser in informationen från filen och ansluter till din arbets yta.

Skapa en konfigurations fil för arbets ytor på något av följande sätt:

* Azure Portal

    **Hämta filen** : i [Azure Portal](https://ms.portal.azure.com)väljer du  **Hämta config.jspå** från **översikts** avsnittet på din arbets yta.

    ![Azure Portal](./media/how-to-configure-environment/configure.png)

* Azure Machine Learning python SDK

    Skapa ett skript för att ansluta till din Azure Machine Learning-arbetsyta och Använd [`write_config`](/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#write-config-path-none--file-name-none-&preserve-view=true) metoden för att generera filen och spara den som *. azureml/config.jspå*. Se till att ersätta `subscription_id` , `resource_group` och `workspace_name` med din egen.

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

## <a name="local-computer"></a><a id="local"></a>Lokal dator

Konfigurera en lokal utvecklings miljö (som också kan vara en virtuell fjärrdator, till exempel en Azure Machine Learning beräknings instans eller DSVM):

1. Skapa en virtuell python-miljö (virtuell miljö, Conda).

    > [!NOTE]
    > Även om det inte krävs rekommenderar vi att du använder [Anaconda](https://www.anaconda.com/download/) eller [Miniconda](https://www.anaconda.com/download/) för att hantera python-virtuella miljöer och installera paket.

    > [!IMPORTANT]
    > Om du använder Linux eller macOS och använder ett annat skal än bash (till exempel zsh) kan du få fel när du kör vissa kommandon. Undvik det här problemet genom `bash` att använda kommandot för att starta ett nytt bash-gränssnitt och köra kommandona där.

1. Aktivera din nyligen skapade python Virtual-miljö.
1. Installera [Azure Machine Learning python SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py).
1. Om du vill konfigurera din lokala miljö för att använda din Azure Machine Learning arbets yta [skapar du en konfigurations fil för arbets ytor](#workspace) eller använder en befintlig.

Nu när du har konfigurerat din lokala miljö är du redo att börja arbeta med Azure Machine Learning. Kom igång genom att gå till [Azure Machine Learning python-guiden](tutorial-1st-experiment-sdk-setup-local.md) för att komma igång.

### <a name="jupyter-notebooks"></a><a id="jupyter"></a>Jupyter Notebook

När du kör en lokal Jupyter Notebook server rekommenderar vi att du skapar en IPython-kernel för din virtuella python-miljö. Detta säkerställer det förväntade beteendet för kernel-och paket import.

1. Aktivera miljöbaserade IPython-kärnor

    ```bash
    conda install notebook ipykernel
    ```

1. Skapa en kernel för din virtuella python-miljö. Ersätt `<myenv>` med namnet på din virtuella python-miljö.

    ```bash
    ipython kernel install --user --name <myenv> --display-name "Python (myenv)"
    ```

1. Starta Jupyter Notebook-servern

Se [Azure Machine Learning Notebooks-lagringsplatsen](https://github.com/Azure/MachineLearningNotebooks) för att komma igång med Azure Machine Learning-och Jupyter-anteckningsböcker.

> [!NOTE]
> Du hittar en community-driven databas med exempel på https://github.com/Azure/azureml-examples .

### <a name="visual-studio-code"></a><a id="vscode"></a>Visual Studio-koden

Så här använder du Visual Studio Code för utveckling:

1. Installera [Visual Studio Code](https://code.visualstudio.com/Download).
1. Installera [Azure Machine Learning Visual Studio Code Extension](tutorial-setup-vscode-extension.md) (för hands version).

När du har installerat Visual Studio Code-tillägget kan du hantera dina [Azure Machine Learning-resurser](how-to-manage-resources-vscode.md), [köra och felsöka experiment](how-to-debug-visual-studio-code.md)och [distribuera utbildade modeller](tutorial-train-deploy-image-classification-model-vscode.md).

## <a name="azure-machine-learning-compute-instance"></a><a id="compute-instance"></a>Azure Machine Learning-beräkningsinstans

Azure Machine Learning [Compute-instansen](concept-compute-instance.md) är en säker, molnbaserad Azure-arbetsstation som tillhandahåller data experter med en Jupyter Notebook-Server, JupyterLab och en helt hanterad maskin inlärnings miljö.

Det finns inget att installera eller konfigurera för en beräknings instans.  

Skapa en när som helst i din Azure Machine Learning-arbetsyta. Ange ett namn och ange en typ av virtuell Azure-dator. Prova nu med den här [självstudien: installations miljö och arbets yta](tutorial-1st-experiment-sdk-setup.md).

Mer information om beräknings instanser, inklusive hur du installerar paket finns i [skapa och hantera en Azure Machine Learning beräknings instans](how-to-create-manage-compute-instance.md).

> [!TIP]
> [Stoppa beräknings instansen](how-to-create-manage-compute-instance.md#manage)för att förhindra kostnader för en oanvänd beräknings instans.

Förutom en Jupyter Notebook Server-och JupyterLab kan du använda beräknings instanser i den [integrerade Notebook-funktionen i Azure Machine Learning Studio](how-to-run-jupyter-notebooks.md).

Du kan också använda tillägget Azure Machine Learning Visual Studio Code för att [Konfigurera en Azure Machine Learning beräknings instans som en fjärran sluten Jupyter Notebook Server](how-to-set-up-vs-code-remote.md#configure-compute-instance-as-remote-notebook-server).

## <a name="data-science-virtual-machine"></a><a id="dsvm"></a>Virtuell dator för datavetenskap

DSVM är en anpassad avbildning av en virtuell dator (VM). Det är utformat för data vetenskaps arbete som är förkonfigurerade verktyg och program som:

  - Paket som TensorFlow, PyTorch, Scikit-lära, XGBoost och Azure Machine Learning SDK
  - Populära verktyg för data vetenskap som Spark standalone och detalj nivå
  - Azure-verktyg som Azure CLI, AzCopy och Storage Explorer
  - Integrerade utvecklings miljöer (IDE: er) som Visual Studio Code och pycharm med
  - Jupyter Notebook Server

En mer omfattande lista över verktygen finns i [hand boken för DSVM-verktyg](data-science-virtual-machine/tools-included.md).

> [!IMPORTANT]
> Om du planerar att använda DSVM som ett [beräknings mål](concept-compute-target.md) för dina utbildnings-eller inferencing-jobb stöds endast Ubuntu.

Använda DSVM som utvecklings miljö

1. Skapa en DSVM med någon av följande metoder:

    * Använd Azure Portal för att skapa en [Ubuntu](data-science-virtual-machine/dsvm-ubuntu-intro.md) -eller [Windows](data-science-virtual-machine/provision-vm.md) -DSVM.
    * [Skapa en DSVM med ARM-mallar](data-science-virtual-machine/dsvm-tutorial-resource-manager.md).
    * Använda Azure CLI

        Använd följande kommando för att skapa en Ubuntu-DSVM:

        ```azurecli-interactive
        # create a Ubuntu DSVM in your resource group
        # note you need to be at least a contributor to the resource group in order to execute this command successfully
        # If you need to create a new resource group use: "az group create --name YOUR-RESOURCE-GROUP-NAME --location YOUR-REGION (For example: westus2)"
        az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:linux-data-science-vm-ubuntu:linuxdsvmubuntu:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --generate-ssh-keys --authentication-type password
        ```

        Om du vill skapa en Windows-DSVM använder du följande kommando:

        ```azurecli-interactive
        # create a Windows Server 2016 DSVM in your resource group
        # note you need to be at least a contributor to the resource group in order to execute this command successfully
        az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:dsvm-windows:server-2016:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --authentication-type password
        ```

1. Aktivera Conda-miljön som innehåller Azure Machine Learning SDK.

    * För Ubuntu-DSVM:

        ```bash
        conda activate py36
        ```

    * För Windows-DSVM:

        ```bash
        conda activate AzureML
        ```

1. Om du vill konfigurera DSVM till att använda din Azure Machine Learning arbets yta [skapar du en konfigurations fil för arbets ytor](#workspace) eller använder en befintlig.

Precis som i lokala miljöer kan du använda Visual Studio Code och [tillägget Azure Machine Learning Visual Studio Code](#vscode) för att interagera med Azure Machine Learning.

Mer information finns i [Virtual Machines för data vetenskap](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/).

## <a name="azure-databricks"></a><a name="aml-databricks"></a> Azure Databricks

Azure Databricks är en Apache Spark-baserad miljö i Azure-molnet. Den ger en samarbets Notebook-baserad miljö med ett processor-eller GPU-baserat beräknings kluster.

Hur Azure Databricks fungerar med Azure Machine Learning:

+ Du kan träna en modell med Spark-MLlib och distribuera modellen till ACI/AKS inifrån Azure Databricks.
+ Du kan också använda [automatiska maskin inlärnings](concept-automated-ml.md) funktioner i en särskild Azure ml SDK med Azure Databricks.
+ Du kan använda Azure Databricks som ett beräknings mål från en [Azure Machine Learning pipeline](concept-ml-pipelines.md).

### <a name="set-up-your-databricks-cluster"></a>Konfigurera ditt Databricks-kluster

Skapa ett [Databricks-kluster](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal). Vissa inställningar gäller endast om du installerar SDK för automatisk maskin inlärning på Databricks.
**Det tar några minuter att skapa klustret.**

Använd de här inställningarna:

| Inställningen |Gäller för| Värde |
|----|---|---|
| Klusternamn |alltid| yourclustername |
| Databricks Runtime |alltid|Non-ML runtime 7,1 (Scala 2,21, Spark 3.0.0) |
| Python-version |alltid| 3 |
| Arbetare |alltid| 2 eller senare |
| VM-typer för arbetsnoder <br>(fastställer Max antal samtidiga iterationer) |Automatisk ML<br>Only| Minnesoptimerade VM-prioritet |
| Aktivera automatisk skalning |Automatisk ML<br>Only| Avmarkera |

Vänta tills klustret körs innan du fortsätter.

### <a name="install-the-correct-sdk-into-a-databricks-library"></a>Installera rätt SDK i ett Databricks-bibliotek

När klustret har körts skapar du [ett bibliotek](https://docs.databricks.com/user-guide/libraries.html#create-a-library) för att bifoga lämpligt Azure Machine Learning SDK-paket till klustret. För automatisk ML hoppar du till [avsnittet SDK för Databricks med automatiserad maskin inlärning](#sdk-for-databricks-with-automated-machine-learning).

1. Högerklicka på den aktuella arbetsyte-mappen där du vill lagra biblioteket. Välj **skapa**  >  **bibliotek**.

1. Välj följande alternativ (ingen annan SDK-installation stöds)

   |Tillägg för SDK- &nbsp; paket &nbsp;|Källa|PyPi &nbsp; namn&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
   |----|---|---|
   |För Databricks| Överför python-ägg eller PyPI | azureml – SDK [databricks]|

   > [!Warning]
   > Inga andra SDK-tillägg kan installeras. Välj bara alternativet [ `databricks` ].

   * Välj inte **Anslut automatiskt till alla kluster**.
   * Välj  **Anslut** bredvid klustrets namn.

1. Övervaka fel tills status ändras till **kopplat** , vilket kan ta flera minuter.  Om det här steget Miss lyckas:

   Försök att starta om klustret genom att:
   1. Välj **kluster** i den vänstra rutan.
   1. I tabellen väljer du ditt kluster namn.
   1. På fliken **bibliotek** väljer du **starta om**.

   Tänk också på följande:
   + När du använder Azure Databricks lägger du till följande parametrar i AutoML config:
       1. ```max_concurrent_iterations``` baseras på antalet arbetsnoder i klustret.
        2. ```spark_context=sc``` baseras på standard-Spark-kontexten.
   + Om du har en gammal SDK-version avmarkerar du den från klustrets installerade libs och flyttar till pappers korgen. Installera den nya SDK-versionen och starta om klustret. Om det uppstår ett problem efter omstarten, tar du bort och återansluter klustret.

Om installationen lyckades bör det importerade biblioteket se ut ungefär så här:

#### <a name="sdk-for-databricks"></a>SDK för Databricks
![Azure Machine Learning SDK för Databricks](./media/how-to-configure-environment/amlsdk-withoutautoml.jpg)

#### <a name="sdk-for-databricks-with-automated-machine-learning"></a>SDK för Databricks med automatisk maskin inlärning
Om klustret skapades med Databricks utan ML runtime 7,1 eller senare kör du följande kommando i den första cellen i antecknings boken för att installera AML SDK.

```
%pip install --upgrade --force-reinstall -r https://aka.ms/automl_linux_requirements.txt
```
För Databricks utan ML runtime 7,0 och lägre installerar du AML SDK med hjälp av [init-skriptet](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks/automl/README.md).


### <a name="start-exploring"></a>Börja utforska

Prova:
+ Även om många exempel antecknings böcker är tillgängliga **fungerar bara [dessa exempel antecknings böcker](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks) med Azure Databricks.**

+ Importera de här exemplen direkt från din arbets yta. Se nedan: ![ Välj import ](./media/how-to-configure-environment/azure-db-screenshot.png)
 ![ import panel](./media/how-to-configure-environment/azure-db-import.png)

+ Lär dig hur du [skapar en pipeline med Databricks som inlärnings beräkning](how-to-create-your-first-pipeline.md).

## <a name="next-steps"></a>Nästa steg

- [Träna en modell](tutorial-train-models-with-aml.md) på Azure Machine Learning med MNIST-datauppsättningen
- Visa [Azure Machine Learning SDK för python](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py) -referens