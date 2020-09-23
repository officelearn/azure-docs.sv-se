---
title: Skapa utbildning & distribuera computes (python)
titleSuffix: Azure Machine Learning
description: Använd Azure Machine Learning python SDK för att skapa utbildnings resurser för utbildning och distribution (beräknings mål) för maskin inlärning
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 07/08/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperfq1
ms.openlocfilehash: ac440db4c1dbddd317743e2d681a62251624d9bd
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90898127"
---
# <a name="create-compute-targets-for-model-training-and-deployment-with-python-sdk"></a>Skapa beräknings mål för modell utbildning och distribution med python SDK

I den här artikeln använder du Azure Machine Learning python SDK för att skapa och hantera beräknings mål. Du kan också skapa och hantera beräknings mål med:
* [Azure Machine Learning Studio](how-to-create-attach-compute-studio.md), 
* [CLI-tillägget](reference-azure-machine-learning-cli.md#resource-management) för Azure Machine Learning
* [Vs Code-tillägget](how-to-manage-resources-vscode.md#compute-clusters) för Azure Machine Learning.


## <a name="prerequisites"></a>Förutsättningar

* Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree) idag
* [Azure Machine Learning SDK för python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true)
* En [Azure Machine Learning arbets yta](how-to-manage-workspace.md)

## <a name="limitations"></a>Begränsningar

* **Skapa inte flera, samtidiga bilagor till samma beräkning** från din arbets yta. Du kan till exempel ansluta ett Azure Kubernetes service-kluster till en arbets yta med två olika namn. Varje ny bilaga kommer att dela upp de tidigare befintliga bifogade filerna.

    Om du vill koppla ett beräknings mål på nytt, t. ex. ändra TLS eller en annan kluster konfigurations inställning, måste du först ta bort den befintliga bilagan.

* Några av de scenarier som anges i det här dokumentet är markerade som för __hands version__. För hands versions funktionerna tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="whats-a-compute-target"></a>Vad är ett beräknings mål?

Med Azure Machine Learning kan du träna din modell på en mängd olika resurser eller miljöer, som sammankallas för [__beräknings mål__](concept-azure-machine-learning-architecture.md#compute-targets). Ett beräknings mål kan vara en lokal dator eller en moln resurs, t. ex. en Azure Machine Learning Compute, Azure HDInsight eller en virtuell dator.  Du kan också skapa beräknings mål för modell distribution enligt beskrivningen i ["var och hur du distribuerar dina modeller"](how-to-deploy-and-where.md).

## <a name="compute-targets-for-training"></a>Compute-mål för utbildning

Azure Machine Learning har varierande stöd för olika beräknings mål. En typisk modell utvecklings livs cykel börjar med utveckling/experiment på en liten mängd data. I det här skedet rekommenderar vi att du använder en lokal miljö. Till exempel din lokala dator eller en molnbaserad virtuell dator. När du skalar din utbildning på större data uppsättningar eller utför distribuerad träning rekommenderar vi att du använder Azure Machine Learning Compute för att skapa ett kluster med en eller flera noder som autoskalar varje gång du skickar en körning. Du kan också koppla din egen beräknings resurs, även om stöd för olika scenarier kan variera enligt beskrivningen nedan:

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]

> [!NOTE]
> Azure Machine Learning beräknings kluster kan skapas som en permanent resurs eller skapas dynamiskt när du begär en körning. Med körnings-baserad skapande tas beräknings målet bort när övnings körningen är klar, så du kan inte återanvända beräknings målen som skapats på det här sättet.

Använd avsnitten nedan för att konfigurera dessa beräknings mål:

* [Lokal dator](#local)
* [Azure Machine Learning beräknings kluster](#amlcompute)
* [Azure Machine Learning-beräkningsinstans](#instance)
* [Virtuella fjärrdatorer](#vm)
* [Azure HDInsight](#hdinsight)

## <a name="compute-targets-for-inference"></a>Beräknings mål för härledning

När du utför en härledning skapar Azure Machine Learning en Docker-behållare som är värd för modellen och tillhör ande resurser som krävs för att använda den. Den här behållaren används sedan i något av följande distributions scenarier:

* Som en __webb tjänst__ som används för real tids härledning. Webb tjänst distributioner använder något av följande beräknings mål:

    * [Lokal dator](#local)
    * [Azure Machine Learning-beräkningsinstans](#instance)
    * [Azure Container Instances](#aci)
    * [Azure Kubernetes Services](how-to-create-attach-kubernetes.md)
    * Azure Functions (för hands version). Distribution till Azure Functions baseras bara på Azure Machine Learning för att bygga Docker-behållaren. Därifrån distribueras den med hjälp av Azure Functions. Mer information finns i [distribuera en maskin inlärnings modell till Azure Functions (för hands version)](how-to-deploy-functions.md).

* Som en slut punkt för __batch-härledning__ som används för att regelbundet bearbeta batchar med data. Batch-inferences använder [Azure Machine Learning beräknings kluster](#amlcompute).

* Till en __IoT-enhet__ (för hands version). Distribution till en IoT-enhet förlitar sig bara på Azure Machine Learning för att bygga Docker-behållaren. Därifrån distribueras den med hjälp av Azure IoT Edge. Mer information finns i [distribuera som en IoT Edge modul (för hands version)](/azure/iot-edge/tutorial-deploy-machine-learning).

## <a name="local-computer"></a><a id="local"></a>Lokal dator

När du använder den lokala datorn för **utbildning**behöver du inte skapa något beräknings mål.  [Skicka bara utbildnings körningen](how-to-set-up-training-targets.md) från den lokala datorn.

När du använder den lokala datorn för att få en **härledning**måste du ha Docker installerat. Utför distributionen genom att använda [LocalWebservice. deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#deploy-configuration-port-none-) för att definiera den port som webb tjänsten ska använda. Använd sedan den normala distributions processen enligt beskrivningen i [Distribuera modeller med Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="azure-machine-learning-compute-cluster"></a><a id="amlcompute"></a>Azure Machine Learning beräknings kluster

Azure Machine Learning Compute Cluster är en hanterad beräknings infrastruktur som gör att du enkelt kan skapa en beräkning med en enda eller flera noder. Beräkningen skapas i arbets ytans region som en resurs som kan delas med andra användare i din arbets yta. Beräkningen skalas upp automatiskt när ett jobb skickas och kan placeras i ett Azure-Virtual Network. Beräkningen körs i en behållare miljö och paketerar dina modell beroenden i en [Docker-behållare](https://www.docker.com/why-docker).

Du kan använda Azure Machine Learning Compute för att distribuera en Training-eller batch-härledning i ett kluster av processor-eller GPU-datornoder i molnet. Mer information om de VM-storlekar som innehåller GPU: er finns i [GPU-optimerade storlekar för virtuella datorer](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu). 

Azure Machine Learning Compute har standard gränser, till exempel antalet kärnor som kan allokeras. Mer information finns i [Hantera och begära kvoter för Azure-resurser](how-to-manage-quotas.md).

> [!TIP]
> Kluster kan i allmänhet skala upp till 100 noder så länge som du har tillräckligt med kvot för antalet kärnor som krävs. Som standard konfigureras kluster för kommunikation mellan noder mellan noderna i klustret som stöd för MPI-jobb till exempel. Du kan dock skala dina kluster till tusentals noder genom att bara [höja ett support ärende](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)och begära att tillåta att en lista över din prenumeration, arbets yta eller ett särskilt kluster inaktive ras i kommunikation mellan noder. 

Azure Machine Learning Compute kan återanvändas över körningar. Beräkningen kan delas med andra användare på arbets ytan och bevaras mellan körningar och automatiskt skalar noderna uppåt eller nedåt baserat på antalet körningar som skickats och max_nodes som angetts i klustret. Inställningen min_nodes styr de minsta tillgängliga noderna.

[!INCLUDE [min-nodes-note](../../includes/machine-learning-min-nodes.md)]

1. **Skapa och koppla**: om du vill skapa en beständig Azure Machine Learning beräknings resurs i python anger du **vm_size** och **max_nodes** egenskaper. Azure Machine Learning använder sedan smarta standardinställningar för de andra egenskaperna. Beräkningen skalas ned till noll noder när den inte används.   Dedikerade virtuella datorer skapas för att köra jobben efter behov.
    
    * **vm_size**: VM-serien för noderna som skapats av Azure Machine Learning Compute.
    * **max_nodes**: det högsta antalet noder som autoskalar upp till när du kör ett jobb på Azure Machine Learning beräkning.
    
   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=cpu_cluster)]

   Du kan också konfigurera flera avancerade egenskaper när du skapar Azure Machine Learning Compute. Med egenskaperna kan du skapa ett beständigt kluster med fast storlek eller inom en befintlig Azure-Virtual Network i din prenumeration.  Mer information finns i [AmlCompute-klassen](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py
    ) .

    Eller så kan du skapa och koppla en beständig Azure Machine Learning beräknings resurs i [Azure Machine Learning Studio](how-to-create-attach-compute-studio.md#portal-create).

Nu när du har kopplat beräkningen är nästa steg att [skicka in utbildnings körningen](how-to-set-up-training-targets.md) eller [köra batch-härledning](how-to-use-parallel-run-step.md).

 ### <a name="lower-your-compute-cluster-cost"></a><a id="low-pri-vm"></a> Sänk din beräknings kluster kostnad

Du kan också välja att använda [virtuella datorer med låg prioritet](concept-plan-manage-cost.md#low-pri-vm) för att köra vissa eller alla arbets belastningar. De här virtuella datorerna har inte garanterad tillgänglighet och kan komma att blockeras när den används. Ett väntande jobb startas om, inte återupptas. 

Använd något av följande sätt för att ange en virtuell dator med låg prioritet:
    
* I Studio väljer du **låg prioritet** när du skapar en virtuell dator.
    
* Med python SDK anger du `vm_priority` attributet i etablerings konfigurationen.  
    
    ```python
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                                vm_priority='lowpriority',
                                                                max_nodes=4)
    ```
    
* Använd CLI och ange `vm-priority` :
    
    ```azurecli-interactive
    az ml computetarget create amlcompute --name lowpriocluster --vm-size Standard_NC6 --max-nodes 5 --vm-priority lowpriority
    ```

 ### <a name="set-up-managed-identity"></a><a id="managed-identity"></a> Konfigurera hanterad identitet

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-intro.md)]

Konfigurera hanterad identitet i etablerings konfigurationen:  
    
* Systemtilldelad hanterad identitet:
    ```python
    # configure cluster with a system-assigned managed identity
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                            max_nodes=5,
                                                            identity_type="SystemAssigned",
                                                            )
    ```

* Användardefinierad hanterad identitet: 

    ```python
    # configure cluster with a user-assigned managed identity
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                            max_nodes=5,
                                                            identity_type="UserAssigned",
                                                            identity_id=['/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'])

    cpu_cluster_name = "cpu-cluster"
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
    ```

Lägg till hanterad identitet i ett befintligt beräknings kluster:  
    
* Systemtilldelad hanterad identitet:

    ```python
    # add a system-assigned managed identity
    cpu_cluster.add_identity(identity_type="SystemAssigned")
    ````

* Användardefinierad hanterad identitet:

    ```python
    # add a user-assigned managed identity
    cpu_cluster.add_identity(identity_type="UserAssigned", 
                                identity_id=['/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'])
    ```

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-note.md)]

#### <a name="managed-identity-usage"></a>Hanterad identitets användning

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-default.md)]


## <a name="azure-machine-learning-compute-instance"></a><a id="instance"></a>Azure Machine Learning-beräkningsinstans

[Azure Machine Learning beräknings instans](concept-compute-instance.md) är en hanterad beräknings infrastruktur som gör att du enkelt kan skapa en enda virtuell dator. Beräkningen skapas i din region i arbets ytan, men till skillnad från ett beräknings kluster kan en instans inte delas med andra användare i din arbets yta. Även instansen skalas inte automatiskt ned.  Du måste stoppa resursen för att förhindra pågående kostnader.

En beräknings instans kan köra flera jobb parallellt och har en jobbkö. 

Beräknings instanser kan köra jobb på ett säkert sätt i en [virtuell nätverks miljö](how-to-enable-virtual-network.md#compute-instance), utan att företag behöver öppna SSH-portar. Jobbet körs i en behållare miljö och paketerar dina modell beroenden i en Docker-behållare. 

1. **Skapa och koppla**: 
    
    ```python
    import datetime
    import time
    
    from azureml.core.compute import ComputeTarget, ComputeInstance
    from azureml.core.compute_target import ComputeTargetException
    
    # Choose a name for your instance
    # Compute instance name should be unique across the azure region
    compute_name = "ci{}".format(ws._workspace_id)[:10]
    
    # Verify that instance does not exist already
    try:
        instance = ComputeInstance(workspace=ws, name=compute_name)
        print('Found existing instance, use it.')
    except ComputeTargetException:
        compute_config = ComputeInstance.provisioning_configuration(
            vm_size='STANDARD_D3_V2',
            ssh_public_access=False,
            # vnet_resourcegroup_name='<my-resource-group>',
            # vnet_name='<my-vnet-name>',
            # subnet_name='default',
            # admin_user_ssh_public_key='<my-sshkey>'
        )
        instance = ComputeInstance.create(ws, compute_name, compute_config)
        instance.wait_for_completion(show_output=True)
    ```

Nu när du har kopplat beräkningen och konfigurerat din körning är nästa steg att [Skicka utbildningen](how-to-set-up-training-targets.md) eller [distribuera en modell för härledning](how-to-deploy-local-container-notebook-vm.md).

## <a name="azure-container-instance"></a><a id="aci"></a>Azure Container-instans

Azure Container Instances (ACI) skapas dynamiskt när du distribuerar en modell. Du kan inte skapa eller koppla ACI till din arbets yta på något annat sätt. Mer information finns i [distribuera en modell till Azure Container instances](how-to-deploy-azure-container-instance.md).

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Med Azure Kubernetes service (AKS) kan du välja mellan olika konfigurations alternativ när de används med Azure Machine Learning. Mer information finns i [så här skapar och ansluter du Azure Kubernetes-tjänsten](how-to-create-attach-kubernetes.md).

## <a name="remote-virtual-machines"></a><a id="vm"></a>Virtuella fjärrdatorer

Azure Machine Learning också stöd för att ta med din egen beräknings resurs och koppla den till din arbets yta. En sådan resurs typ är en godtycklig virtuell fjärrdator, så länge den är tillgänglig från Azure Machine Learning. Resursen kan vara en virtuell Azure-dator, en fjärrserver i din organisation eller lokalt. Mer specifikt, baserat på IP-adress och autentiseringsuppgifter (användar namn och lösen ord eller SSH-nyckel), kan du använda valfri tillgänglig virtuell dator för fjärrkörningar.

Du kan använda en systemskapad Conda-miljö, en redan befintlig python-miljö eller en Docker-behållare. Om du vill köra på en Docker-behållare måste du ha en Docker-motor som körs på den virtuella datorn. Den här funktionen är särskilt användbar när du vill ha en mer flexibel, molnbaserad utvecklings-eller experiment miljö än din lokala dator.

Använd Azure-Data Science Virtual Machine (DSVM) som den virtuella Azure-dator som du väljer för det här scenariot. Den här virtuella datorn är en förkonfigurerad miljö för data vetenskap och AI-utveckling i Azure. Den virtuella datorn innehåller ett granskat val av verktyg och ramverk för hela livs cykeln för Machine Learning-utveckling. Mer information om hur du använder DSVM med Azure Machine Learning finns i [Konfigurera en utvecklings miljö](https://docs.microsoft.com/azure/machine-learning/how-to-configure-environment#dsvm).

1. **Skapa**: skapa en DSVM innan du använder den för att träna din modell. Information om hur du skapar den här resursen finns i [etablera data science Virtual Machine för Linux (Ubuntu)](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro).

    > [!WARNING]
    > Azure Machine Learning stöder bara virtuella datorer som kör **Ubuntu**. När du skapar en virtuell dator eller väljer en befintlig virtuell dator måste du välja en virtuell dator som använder Ubuntu.
    > 
    > Azure Machine Learning kräver också att den virtuella datorn har en __offentlig IP-adress__.

1. **Bifoga**: om du vill koppla en befintlig virtuell dator som ett beräknings mål måste du ange resurs-ID, användar namn och lösen ord för den virtuella datorn. Resurs-ID: t för den virtuella datorn kan konstrueras med prenumerations-ID, resurs grupp namn och namn på virtuell dator med följande sträng format: `/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Compute/virtualMachines/<vm_name>`

 
   ```python
   from azureml.core.compute import RemoteCompute, ComputeTarget

   # Create the compute config 
   compute_target_name = "attach-dsvm"
   
   attach_config = RemoteCompute.attach_configuration(resource_id='<resource_id>',
                                                   ssh_port=22,
                                                   username='<username>',
                                                   password="<password>")

   # Attach the compute
   compute = ComputeTarget.attach(ws, compute_target_name, attach_config)

   compute.wait_for_completion(show_output=True)
   ```

   Eller så kan du koppla DSVM till din arbets yta [med Azure Machine Learning Studio](how-to-create-attach-compute-studio.md#attached-compute).

    > [!WARNING]
    > Skapa inte flera, samtidiga bilagor till samma DSVM från din arbets yta. Varje ny bilaga kommer att dela upp de tidigare befintliga bifogade filerna.

1. **Konfigurera**: skapa en körnings konfiguration för DSVM Compute Target. Docker och Conda används för att skapa och konfigurera utbildnings miljön på DSVM.

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/dsvm.py?name=run_dsvm)]


Nu när du har kopplat beräkningen och konfigurerat din körning är nästa steg att [Skicka utbildningen](how-to-set-up-training-targets.md).

## <a name="azure-hdinsight"></a><a id="hdinsight"></a>Azure HDInsight 

Azure HDInsight är en populär plattform för stor data analys. Plattformen ger Apache Spark som kan användas för att träna din modell.

1. **Skapa**: skapa HDInsight-klustret innan du använder det för att träna din modell. Information om hur du skapar ett spark på HDInsight-kluster finns i [skapa ett Spark-kluster i HDInsight](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql). 

    > [!WARNING]
    > Azure Machine Learning kräver att HDInsight-klustret har en __offentlig IP-adress__.

    När du skapar klustret måste du ange ett SSH-användarnamn och-lösen ord. Anteckna dessa värden eftersom du behöver dem för att använda HDInsight som ett beräknings mål.
    
    När klustret har skapats ansluter du till det med hostname \<clustername> -SSH.azurehdinsight.net, där \<clustername> är det namn som du har angett för klustret. 

1. **Bifoga**: om du vill ansluta ett HDInsight-kluster som ett beräknings mål måste du ange resurs-ID, användar namn och lösen ord för HDInsight-klustret. Resurs-ID för HDInsight-klustret kan konstrueras med prenumerations-ID, resurs grupp namn och HDInsight-kluster namn med följande sträng format: `/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.HDInsight/clusters/<cluster_name>`

    ```python
   from azureml.core.compute import ComputeTarget, HDInsightCompute
   from azureml.exceptions import ComputeTargetException

   try:
    # if you want to connect using SSH key instead of username/password you can provide parameters private_key_file and private_key_passphrase

    attach_config = HDInsightCompute.attach_configuration(resource_id='<resource_id>',
                                                          ssh_port=22, 
                                                          username='<ssh-username>', 
                                                          password='<ssh-pwd>')
    hdi_compute = ComputeTarget.attach(workspace=ws, 
                                       name='myhdi', 
                                       attach_configuration=attach_config)

   except ComputeTargetException as e:
    print("Caught = {}".format(e.message))

   hdi_compute.wait_for_completion(show_output=True)
   ```

   Eller så kan du ansluta HDInsight-klustret till din arbets yta [med Azure Machine Learning Studio](how-to-create-attach-compute-studio.md#attached-compute).

    > [!WARNING]
    > Skapa inte flera, samtidiga bilagor till samma HDInsight från din arbets yta. Varje ny bilaga kommer att dela upp de tidigare befintliga bifogade filerna.

1. **Konfigurera**: skapa en körnings konfiguration för HDI Compute Target. 

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/hdi.py?name=run_hdi)]


Nu när du har kopplat beräkningen och konfigurerat din körning är nästa steg att [Skicka utbildningen](how-to-set-up-training-targets.md).

## <a name="azure-batch"></a><a id="azbatch"></a>Azure Batch 

Azure Batch används för att köra storskaliga parallella program och HPC-program (data behandling med höga prestanda) i molnet. AzureBatchStep kan användas i en Azure Machine Learning pipeline för att skicka jobb till en Azure Batch pool med datorer.

Om du vill bifoga Azure Batch som ett beräknings mål måste du använda Azure Machine Learning SDK och ange följande information:

-    **Azure Batch Compute Name**: ett eget namn som ska användas för data bearbetningen inom arbets ytan
-    **Azure Batch konto namn**: namnet på det Azure Batch kontot
-    **Resurs grupp**: den resurs grupp som innehåller det Azure Batch kontot.

Följande kod visar hur du kopplar Azure Batch som ett beräknings mål:

```python
from azureml.core.compute import ComputeTarget, BatchCompute
from azureml.exceptions import ComputeTargetException

# Name to associate with new compute in workspace
batch_compute_name = 'mybatchcompute'

# Batch account details needed to attach as compute to workspace
batch_account_name = "<batch_account_name>"  # Name of the Batch account
# Name of the resource group which contains this account
batch_resource_group = "<batch_resource_group>"

try:
    # check if the compute is already attached
    batch_compute = BatchCompute(ws, batch_compute_name)
except ComputeTargetException:
    print('Attaching Batch compute...')
    provisioning_config = BatchCompute.attach_configuration(
        resource_group=batch_resource_group, account_name=batch_account_name)
    batch_compute = ComputeTarget.attach(
        ws, batch_compute_name, provisioning_config)
    batch_compute.wait_for_completion()
    print("Provisioning state:{}".format(batch_compute.provisioning_state))
    print("Provisioning errors:{}".format(batch_compute.provisioning_errors))

print("Using Batch compute:{}".format(batch_compute.cluster_resource_id))
```

> [!WARNING]
> Skapa inte flera, samtidiga bilagor till samma Azure Batch från din arbets yta. Varje ny bilaga kommer att dela upp de tidigare befintliga bifogade filerna.

### <a name="azure-databricks"></a><a id="databricks"></a>Azure Databricks

Azure Databricks är en Apache Spark-baserad miljö i Azure-molnet. Den kan användas som ett beräknings mål med en Azure Machine Learning pipeline.

Skapa en Azure Databricks arbets yta innan du använder den. Information om hur du skapar en arbets ytas resurs finns i [köra ett Spark-jobb på Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) -dokument.

Om du vill bifoga Azure Databricks som ett beräknings mål anger du följande information:

* __Databricks Compute Name__: det namn som du vill tilldela till den här beräknings resursen.
* __Databricks namn på arbets yta__: namnet på arbets ytan Azure Databricks.
* __Databricks__: åtkomst-token som används för att autentisera till Azure Databricks. Om du vill generera en åtkomsttoken, se [Authentication](https://docs.azuredatabricks.net/dev-tools/api/latest/authentication.html) -dokumentet.

Följande kod visar hur du kopplar Azure Databricks som ett beräknings mål med Azure Machine Learning SDK (Databricks-__arbetsytan måste finnas i samma prenumeration som din AML-arbetsyta__):

```python
import os
from azureml.core.compute import ComputeTarget, DatabricksCompute
from azureml.exceptions import ComputeTargetException

databricks_compute_name = os.environ.get(
    "AML_DATABRICKS_COMPUTE_NAME", "<databricks_compute_name>")
databricks_workspace_name = os.environ.get(
    "AML_DATABRICKS_WORKSPACE", "<databricks_workspace_name>")
databricks_resource_group = os.environ.get(
    "AML_DATABRICKS_RESOURCE_GROUP", "<databricks_resource_group>")
databricks_access_token = os.environ.get(
    "AML_DATABRICKS_ACCESS_TOKEN", "<databricks_access_token>")

try:
    databricks_compute = ComputeTarget(
        workspace=ws, name=databricks_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('databricks_compute_name {}'.format(databricks_compute_name))
    print('databricks_workspace_name {}'.format(databricks_workspace_name))
    print('databricks_access_token {}'.format(databricks_access_token))

    # Create attach config
    attach_config = DatabricksCompute.attach_configuration(resource_group=databricks_resource_group,
                                                           workspace_name=databricks_workspace_name,
                                                           access_token=databricks_access_token)
    databricks_compute = ComputeTarget.attach(
        ws,
        databricks_compute_name,
        attach_config
    )

    databricks_compute.wait_for_completion(True)
```

Ett mer detaljerat exempel finns i en [exempel antecknings bok](https://aka.ms/pl-databricks) på GitHub.

> [!WARNING]
> Skapa inte flera, samtidiga bilagor till samma Azure Databricks från din arbets yta. Varje ny bilaga kommer att dela upp de tidigare befintliga bifogade filerna.

### <a name="azure-data-lake-analytics"></a><a id="adla"></a>Azure Data Lake Analytics

Azure Data Lake Analytics är en stor data analys plattform i Azure-molnet. Den kan användas som ett beräknings mål med en Azure Machine Learning pipeline.

Skapa ett Azure Data Lake Analytics konto innan du använder det. Information om hur du skapar den här resursen finns i dokumentet [Kom igång med Azure Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal) .

Om du vill bifoga Data Lake Analytics som ett beräknings mål måste du använda Azure Machine Learning SDK och ange följande information:

* __Compute-namn__: det namn som du vill tilldela till den här beräknings resursen.
* __Resurs grupp__: den resurs grupp som innehåller det data Lake Analytics kontot.
* __Konto namn__: namnet på data Lake Analytics kontot.

Följande kod visar hur du kopplar Data Lake Analytics som ett beräknings mål:

```python
import os
from azureml.core.compute import ComputeTarget, AdlaCompute
from azureml.exceptions import ComputeTargetException


adla_compute_name = os.environ.get(
    "AML_ADLA_COMPUTE_NAME", "<adla_compute_name>")
adla_resource_group = os.environ.get(
    "AML_ADLA_RESOURCE_GROUP", "<adla_resource_group>")
adla_account_name = os.environ.get(
    "AML_ADLA_ACCOUNT_NAME", "<adla_account_name>")

try:
    adla_compute = ComputeTarget(workspace=ws, name=adla_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('adla_compute_name {}'.format(adla_compute_name))
    print('adla_resource_id {}'.format(adla_resource_group))
    print('adla_account_name {}'.format(adla_account_name))
    # create attach config
    attach_config = AdlaCompute.attach_configuration(resource_group=adla_resource_group,
                                                     account_name=adla_account_name)
    # Attach ADLA
    adla_compute = ComputeTarget.attach(
        ws,
        adla_compute_name,
        attach_config
    )

    adla_compute.wait_for_completion(True)
```

Ett mer detaljerat exempel finns i en [exempel antecknings bok](https://aka.ms/pl-adla) på GitHub.

> [!WARNING]
> Skapa inte flera, samtidiga bilagor till samma ADLA från din arbets yta. Varje ny bilaga kommer att dela upp de tidigare befintliga bifogade filerna.

> [!TIP]
> Azure Machine Learning pipelines kan bara arbeta med data som lagras i standard data lagret för det Data Lake Analytics kontot. Om de data du behöver arbeta med finns i ett lager som inte är standard kan du använda en [`DataTransferStep`](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py&preserve-view=true) för att kopiera data före träning.

## <a name="notebook-examples"></a>Exempel på bärbara datorer

Se dessa antecknings böcker för exempel på utbildning med olika beräknings mål:
* [instruktion för att använda – azureml/utbildning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [Självstudier/img-Classification-part1-Training. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Nästa steg

* Använd beräknings resursen för att [skicka en utbildnings körning](how-to-set-up-training-targets.md).
* [Självstudie: träna en modell](tutorial-train-models-with-aml.md) använder ett hanterat beräknings mål för att träna en modell.
* Lär dig hur du [effektivt justerar disponeringsparametrarna](how-to-tune-hyperparameters.md) för att bygga bättre modeller.
* När du har en tränad modell lär du dig [hur och var modeller ska distribueras](how-to-deploy-and-where.md).
* [Använda Azure Machine Learning med virtuella Azure-nätverk](how-to-enable-virtual-network.md)
