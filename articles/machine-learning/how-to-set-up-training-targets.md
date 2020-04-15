---
title: Använd beräkningsmål för modellutbildning
titleSuffix: Azure Machine Learning
description: Konfigurera utbildningsmiljöer (beräkningsmål) för maskininlärningsmodellutbildning. Du kan enkelt växla mellan träningsmiljöer. Börja träna lokalt. Om du behöver skala ut växlar du till ett molnbaserat beräkningsmål.
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/13/2020
ms.custom: seodec18
ms.openlocfilehash: 7fcfac923da1c0daee58b10d92cbc6a6ad5e7910
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383410"
---
# <a name="set-up-and-use-compute-targets-for-model-training"></a>Ställa in och använda beräkningsmål för modellutbildning 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Med Azure Machine Learning kan du träna din modell på en mängd olika resurser eller miljöer, gemensamt kallade [__beräkningsmål__](concept-azure-machine-learning-architecture.md#compute-targets). Beräkningsmål kan vara en lokal dator eller en molnresurs, till exempel en Azure Machine Learning-beräkning, Azure HDInsight eller en fjärransluten virtuell dator.  Du kan också skapa beräkningsmål för modelldistribution enligt beskrivningen i ["Var och hur du distribuerar dina modeller".](how-to-deploy-and-where.md)

Du kan skapa och hantera ett beräkningsmål med azure machine learning SDK, Azure Machine Learning studio, Azure CLI eller Azure Machine Learning VS Code-tillägget. Om du har beräkningsmål som har skapats via en annan tjänst (till exempel ett HDInsight-kluster) kan du använda dem genom att koppla dem till din Azure Machine Learning-arbetsyta.
 
I den här artikeln får du lära dig hur du använder olika beräkningsmål för modellutbildning.  Stegen för alla beräkningsmål följer samma arbetsflöde:
1. __Skapa__ ett beräkningsmål om du inte redan har ett.
2. __Koppla__ beräkningsmålet till arbetsytan.
3. __Konfigurera__ beräkningsmålet så att det innehåller Python-miljön och paketberoenden som behövs av skriptet.


>[!NOTE]
> Koden i den här artikeln testades med Azure Machine Learning SDK version 1.0.74.

## <a name="compute-targets-for-training"></a>Beräkna mål för utbildning

Azure Machine Learning har varierande stöd för olika beräkningsmål. En typisk modellutvecklingslivscykel börjar med utveckling/experiment på en liten mängd data. I det här skedet rekommenderar vi att du använder en lokal miljö. Till exempel din lokala dator eller en molnbaserad virtuell dator. När du skalar upp din utbildning på större datauppsättningar eller gör distribuerad utbildning rekommenderar vi att du använder Azure Machine Learning Compute för att skapa ett kluster med en eller flera noder som automatiskt skalas varje gång du skickar en körning. Du kan också bifoga din egen beräkningsresurs, även om stödet för olika scenarier kan variera enligt beskrivningen nedan:

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]


> [!NOTE]
> Azure Machine Learning Compute kan skapas som en beständig resurs eller skapas dynamiskt när du begär en körning. Körningsbaserad skapande tar bort beräkningsmålet när träningskörningen är klar, så du kan inte återanvända beräkningsmål som skapats på det här sättet.

## <a name="whats-a-run-configuration"></a>Vad är en körningskonfiguration?

När du tränar är det vanligt att starta på den lokala datorn och senare köra det utbildningsskriptet på ett annat beräkningsmål. Med Azure Machine Learning kan du köra skriptet på olika beräkningsmål utan att behöva ändra skriptet.

Allt du behöver göra är att definiera miljön för varje beräkningsmål i en **körningskonfiguration**.  När du sedan vill köra träningsexperimentet på ett annat beräkningsmål anger du körningskonfigurationen för den beräkningen. Mer information om hur du anger en miljö och binder den till att köra konfiguration finns i [Skapa och hantera miljöer för utbildning och distribution](how-to-use-environments.md).

Läs mer om [hur du skickar in experiment](#submit) i slutet av den här artikeln.

## <a name="whats-an-estimator"></a>Vad är en skattningsgivare?

Azure Machine Learning Python SDK ger en alternativ abstraktion på högre nivå, uppskattningsklassen för att underlätta modellutbildning med populära ramverk.  Med den här klassen kan du enkelt konstruera körningskonfigurationer. Du kan skapa och använda en allmän [Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) för att skicka in utbildningsskript som använder alla inlärningsramverk som du väljer (till exempel scikit-learn). Vi rekommenderar att du använder en uppskattning för utbildning eftersom det automatiskt konstruerar inbäddade objekt som en miljö eller RunConfiguration-objekt åt dig. Om du vill ha mer kontroll över hur dessa objekt skapas och ange vilka paket som ska installeras för experimentkörningen följer du [dessa steg](#amlcompute) för att skicka in dina utbildningsexperiment med hjälp av ett RunConfiguration-objekt på en Azure Machine Learning Compute.

För uppgifter i PyTorch, TensorFlow och Chainer tillhandahåller Azure Machine Learning även respektive [PyTorch-,](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) [TensorFlow-](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)och [Chainer-skattuppskattningar](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) för att förenkla med hjälp av dessa ramverk.

Mer information finns i [Träna ML-modeller med skattningsmätare](how-to-train-ml-models.md).

## <a name="whats-an-ml-pipeline"></a>Vad är en ML Pipeline?

Med ML-pipelines kan du optimera arbetsflödet med enkelhet, hastighet, portabilitet och återanvändning. När du skapar pipelines med Azure Machine Learning kan du fokusera på din expertis, maskininlärning i stället för på infrastruktur och automatisering.

ML rörledningar är konstruerade från flera **steg**, som är olika beräkningsenheter i rörledningen. Varje steg kan köras oberoende av varandra och använda isolerade beräkningsresurser. Detta gör det möjligt för flera dataforskare att arbeta på samma pipeline samtidigt utan att överbeskatta beräkningsresurser, och gör det också enkelt att använda olika beräkningstyper/storlekar för varje steg.

> [!TIP]
> ML Pipelines kan använda körningskonfiguration eller estimatorer när träningsmodeller.

Ml-rörledningar kan träna modeller, men de kan också förbereda data innan de tränar och distribuerar modeller efter träningen. Ett av de primära användningsfallen för pipelines är batchbedömning. Mer information finns i [Pipelines: Optimera arbetsflöden för maskininlärning](concept-ml-pipelines.md).

## <a name="set-up-in-python"></a>Konfigurera i Python

Använd avsnitten nedan för att konfigurera dessa beräkningsmål:

* [Lokal dator](#local)
* [Azure Machine Learning-beräkning](#amlcompute)
* [Virtuella fjärrdatorer](#vm)
* [Azure HDInsight](#hdinsight)


### <a name="local-computer"></a><a id="local"></a>Lokal dator

1. **Skapa och bifoga**: Du behöver inte skapa eller koppla ett beräkningsmål för att använda den lokala datorn som träningsmiljö.  

1. **Konfigurera**: När du använder den lokala datorn som ett beräkningsmål körs träningskoden i [utvecklingsmiljön](how-to-configure-environment.md).  Om den miljön redan har de Python-paket du behöver använder du den användarhanterade miljön.

 [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=run_local)]

Nu när du har bifogat beräkningen och konfigurerat körningen är nästa steg att [skicka träningskörningen](#submit).

### <a name="azure-machine-learning-compute"></a><a id="amlcompute"></a>Azure Machine Learning-beräkning

Azure Machine Learning Compute är en infrastruktur för hanterad beräkning som gör det möjligt för användaren att enkelt skapa en beräkning av en eller flera nod. Beräkningen skapas inom arbetsytan som en resurs som kan delas med andra användare på arbetsytan. Beräkningen skalas upp automatiskt när ett jobb skickas och kan placeras i ett Virtuellt Azure-nätverk. Beräkningen körs i en containermiljö och paketerar dina modellberoenden i en [Docker-behållare](https://www.docker.com/why-docker).

Du kan använda Azure Machine Learning Compute för att distribuera utbildningsprocessen över ett kluster av CPU- eller GPU-beräkningsnoder i molnet. Mer information om den virtuella datorns storlekar som innehåller GPU:er finns i [GPU-optimerade storlekar för virtuella datorer](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu).

Azure Machine Learning Compute har standardgränser, till exempel antalet kärnor som kan allokeras. Mer information finns i [Hantera och begära kvoter för Azure-resurser](https://docs.microsoft.com/azure/machine-learning/how-to-manage-quotas).


Du kan skapa en Azure Machine Learning-beräkningsmiljö på begäran när du schemalägger en körning eller som en beständig resurs.

#### <a name="run-based-creation"></a>Körbaserad skapande

Du kan skapa Azure Machine Learning Compute som ett beräkningsmål vid körning. Beräkningen skapas automatiskt för körningen. Beräkningen tas bort automatiskt när körningen är klar. 

> [!IMPORTANT]
> Körbaserad skapande av Azure Machine Learning-beräkning finns för närvarande i förhandsversion. Använd inte körningsbaserat skapande om du använder automatisk hyperparameterjustering eller automatiserad maskininlärning. Om du vill använda hyperparameterjustering eller automatiserad maskininlärning skapar du i stället ett [beständigt beräkningsmål.](#persistent)

1.  **Skapa, bifoga och konfigurera**: Den körningsbaserade skapelsen utför alla nödvändiga steg för att skapa, bifoga och konfigurera beräkningsmålet med körningskonfigurationen.  

  [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute.py?name=run_temp_compute)]


Nu när du har bifogat beräkningen och konfigurerat körningen är nästa steg att [skicka träningskörningen](#submit).

#### <a name="persistent-compute"></a><a id="persistent"></a>Beständig beräkning

En beständig Azure Machine Learning Compute kan återanvändas mellan jobb. Beräkningen kan delas med andra användare på arbetsytan och hålls mellan jobb.

1. **Skapa och bifoga**: Om du vill skapa en beständig Azure Machine Learning Compute-resurs i Python anger du **egenskaperna vm_size** och **max_nodes.** Azure Machine Learning använder sedan smarta standardvärden för de andra egenskaperna. Beräkna automatiskt skalas ner till noll noder när den inte används.   Dedikerade virtuella datorer skapas för att köra dina jobb efter behov.
    
    * **vm_size:** Den virtuella datorn för noderna som skapats av Azure Machine Learning Compute.
    * **max_nodes**: Det maximala antalet noder som du vill skala upp till automatiskt när du kör ett jobb på Azure Machine Learning Compute.
    
   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=cpu_cluster)]

   Du kan också konfigurera flera avancerade egenskaper när du skapar Azure Machine Learning Compute. Med egenskaperna kan du skapa ett beständigt kluster av fast storlek eller inom ett befintligt Virtuellt Azure-nätverk i din prenumeration.  Mer information finns i [klassen AmlCompute.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py
    )
    
   Du kan också skapa och bifoga en beständig Azure Machine Learning Compute-resurs i [Azure Machine Learning studio](#portal-create).

1. **Konfigurera**: Skapa en körningskonfiguration för det beständiga beräkningsmålet.

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=run_amlcompute)]

Nu när du har bifogat beräkningen och konfigurerat körningen är nästa steg att [skicka träningskörningen](#submit).


### <a name="remote-virtual-machines"></a><a id="vm"></a>Virtuella fjärrdatorer

Azure Machine Learning stöder också att din egen beräkningsresurs och bifoga den till din arbetsyta. En sådan resurstyp är en godtycklig fjärrdator, så länge den är tillgänglig från Azure Machine Learning. Resursen kan vara en virtuell Azure-dator, en fjärrserver i din organisation eller lokalt. Med tanke på IP-adressen och autentiseringsuppgifterna (användarnamn och lösenord eller SSH-nyckel) kan du använda valfri tillgänglig virtuell dator för fjärrkörningar.

Du kan använda en systembyggd conda-miljö, en redan befintlig Python-miljö eller en Docker-behållare. Om du vill köra på en Docker-behållare måste du ha en Docker-motor som körs på den virtuella datorn. Den här funktionen är särskilt användbar när du vill ha en mer flexibel, molnbaserad utvecklings-/experimentmiljö än den lokala datorn.

Använd Virtual Machine (Azure Data Science Machine) som den Azure VM som du väljer för det här scenariot. Den här virtuella datorn är en förkonfigurerad datavetenskap och AI-utvecklingsmiljö i Azure. Den virtuella datorn erbjuder ett kurerat urval av verktyg och ramverk för utveckling av maskininlärning i full livscykel. Mer information om hur du använder DSVM med Azure Machine Learning finns i [Konfigurera en utvecklingsmiljö](https://docs.microsoft.com/azure/machine-learning/how-to-configure-environment#dsvm).

1. **Skapa:** Skapa en DSVM innan du använder den för att träna din modell. Information om hur du skapar den här resursen finns [i Etablera virtuell datavetenskapdator för Linux (Ubuntu)](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro).

    > [!WARNING]
    > Azure Machine Learning stöder endast virtuella datorer som kör Ubuntu. När du skapar en virtuell dator eller väljer en befintlig virtuell dator måste du välja en virtuell dator som använder Ubuntu.

1. **Bifoga:** Om du vill koppla en befintlig virtuell dator som ett beräkningsmål måste du ange resurs-ID, användarnamn och lösenord för den virtuella datorn. Resurs-ID för den virtuella datorn kan konstrueras med prenumerations-ID, resursgruppsnamn och VM-namn med hjälp av följande strängformat:`/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Compute/virtualMachines/<vm_name>`

 
   ```python
   from azureml.core.compute import RemoteCompute, ComputeTarget

   # Create the compute config 
   compute_target_name = "attach-dsvm"
   
   attach_config = RemoteCompute.attach_configuration(resource_id='<resource_id>',
                                                   ssh_port=22,
                                                   username='<username>',
                                                   password="<password>")

   # If you authenticate with SSH keys instead, use this code:
   #                                                  ssh_port=22,
   #                                                  username='<username>',
   #                                                  password=None,
   #                                                  private_key_file="<path-to-file>",
   #                                                  private_key_passphrase="<passphrase>")

   # Attach the compute
   compute = ComputeTarget.attach(ws, compute_target_name, attach_config)

   compute.wait_for_completion(show_output=True)
   ```

   Du kan också koppla DSVM till din arbetsyta [med Azure Machine Learning Studio](#portal-reuse).

1. **Konfigurera**: Skapa en körningskonfiguration för DSVM-beräkningsmålet. Docker och conda används för att skapa och konfigurera träningsmiljön på DSVM.

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/dsvm.py?name=run_dsvm)]


Nu när du har bifogat beräkningen och konfigurerat körningen är nästa steg att [skicka träningskörningen](#submit).

### <a name="azure-hdinsight"></a><a id="hdinsight"></a>Azure HDInsight 

Azure HDInsight är en populär plattform för stordataanalys. Plattformen ger Apache Spark, som kan användas för att träna din modell.

1. **Skapa:** Skapa HDInsight-klustret innan du använder det för att träna din modell. Om du vill skapa ett Spark-kluster på HDInsight finns [i Skapa ett spark-kluster i HDInsight](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql). 

    När du skapar klustret måste du ange ett SSH-användarnamn och lösenord. Notera dessa värden, eftersom du behöver dem för att använda HDInsight som ett beräkningsmål.
    
    När klustret har skapats ansluter \<du till det med värdnamnet klusternamn \<>-ssh.azurehdinsight.net, där klusternamn> är det namn som du angav för klustret. 

1. **Bifoga:** Om du vill koppla ett HDInsight-kluster som ett beräkningsmål måste du ange resurs-ID, användarnamn och lösenord för HDInsight-klustret. Resurs-ID:t för HDInsight-klustret kan konstrueras med hjälp av prenumerations-ID,resursgruppsnamn och HDInsight-klusternamn med hjälp av följande strängformat:`/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.HDInsight/clusters/<cluster_name>`

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

   Du kan också koppla HDInsight-klustret till din arbetsyta [med Azure Machine Learning Studio](#portal-reuse).

1. **Konfigurera**: Skapa en körningskonfiguration för HDI-beräkningsmålet. 

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/hdi.py?name=run_hdi)]


Nu när du har bifogat beräkningen och konfigurerat körningen är nästa steg att [skicka träningskörningen](#submit).


### <a name="azure-batch"></a><a id="azbatch"></a>Azure Batch 

Azure Batch används för att köra storskaliga parallella och högpresterande datorprogram (HPC) effektivt i molnet. AzureBatchStep kan användas i en Azure Machine Learning Pipeline för att skicka jobb till en Azure Batch-pool av datorer.

Om du vill bifoga Azure Batch som ett beräkningsmål måste du använda Azure Machine Learning SDK och ange följande information:

-    **Azure Batch-beräkningsnamn:** Ett eget namn som ska användas för beräkningen inom arbetsytan
-    **Namn på Azure Batch-konto**: Namnet på Azure Batch-kontot
-    **Resursgrupp**: Resursgruppen som innehåller Azure Batch-kontot.

Följande kod visar hur du bifogar Azure Batch som ett beräkningsmål:

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

## <a name="set-up-in-azure-machine-learning-studio"></a>Konfigurera i Azure Machine Learning studio

Du kan komma åt beräkningsmålen som är associerade med din arbetsyta i Azure Machine Learning-studion.  Du kan använda studion för att:

* [Visa beräkningsmål](#portal-view) som är kopplade till arbetsytan
* [Skapa ett beräkningsmål](#portal-create) på arbetsytan
* [Koppla ett beräkningsmål](#portal-reuse) som skapades utanför arbetsytan


När ett mål har skapats och kopplats till arbetsytan använder `ComputeTarget` du det i körningskonfigurationen med ett objekt: 

```python
from azureml.core.compute import ComputeTarget
myvm = ComputeTarget(workspace=ws, name='my-vm-name')
```

### <a name="view-compute-targets"></a><a id="portal-view"></a>Visa beräkningsmål


Så här ser du beräkningsmålen för arbetsytan:

1. Navigera till [Azure Machine Learning studio](https://ml.azure.com).
 
1. Under __Program__väljer du __Beräkna__.

    [![Fliken Visa beräkning](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace.png)](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace-expanded.png)

### <a name="create-a-compute-target"></a><a id="portal-create"></a>Skapa ett beräkningsmål

Följ föregående steg för att visa listan över beräkningsmål. Använd sedan de här stegen för att skapa ett beräkningsmål: 

1. Välj plustecknet (+) om du vill lägga till ett beräkningsmål.

    ![Lägga till ett beräkningsmål](./media/how-to-set-up-training-targets/add-compute-target.png) 

1. Ange ett namn för beräkningsmålet. 

1. Välj **Machine Learning Compute** som den typ av beräkning som ska användas för __utbildning__. 

    >[!NOTE]
    >Azure Machine Learning Compute är den enda hanterade beräkningsresurs som du kan skapa i Azure Machine Learning Studio.  Alla andra beräkningsresurser kan kopplas när de har skapats.

1. Fyll i formuläret. Ange värden för de egenskaper som krävs, särskilt **VM-familjen,** och de **maximala noderna** som ska användas för att snurra upp beräkningen.  

1. Välj __Skapa__.


1. Visa status för åtgärden skapa genom att välja beräkningsmålet i listan:

    ![Välj ett beräkningsmål om du vill visa statusen skapa åtgärd](./media/how-to-set-up-training-targets/View_list.png)

1. Du ser sedan information om beräkningsmålet: 

    ![Visa information om datorns mål](./media/how-to-set-up-training-targets/compute-target-details.png) 

### <a name="attach-compute-targets"></a><a id="portal-reuse"></a>Koppla beräkningsmål

Om du vill använda beräkningsmål som skapats utanför arbetsytan Azure Machine Learning måste du bifoga dem. Om du ansluter ett beräkningsmål blir det tillgängligt för arbetsytan.

Följ stegen som beskrivits tidigare för att visa listan över beräkningsmål. Använd sedan följande steg för att koppla ett beräkningsmål: 

1. Välj plustecknet (+) om du vill lägga till ett beräkningsmål. 
1. Ange ett namn för beräkningsmålet. 
1. Välj vilken typ av beräkning som ska bifogas för __utbildning:__

    > [!IMPORTANT]
    > Alla beräkningstyper kan inte kopplas från Azure Machine Learning Studio. De beräkningstyper som för närvarande kan kopplas till utbildning är:
    >
    > * En fjärr-VM
    > * Azure Databricks (för användning i pipelines för maskininlärning)
    > * Azure Data Lake Analytics (för användning i pipelines för maskininlärning)
    > * Azure HDInsight

1. Fyll i formuläret och ange värden för de önskade egenskaperna.

    > [!NOTE]
    > Microsoft rekommenderar att du använder SSH-nycklar som är säkrare än lösenord. Lösenord är sårbara för brute force-attacker. SSH-nycklar förlitar sig på kryptografiska signaturer. Information om hur du skapar SSH-nycklar för användning med virtuella Azure-datorer finns i följande dokument:
    >
    > * [Skapa och använd SSH-nycklar på Linux eller macOS](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)
    > * [Skapa och använda SSH-nycklar i Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)

1. Välj __Bifoga__. 
1. Visa status för den bifogade åtgärden genom att välja beräkningsmålet i listan.

## <a name="set-up-with-cli"></a>Konfigurera med CLI

Du kan komma åt beräkningsmålen som är associerade med din arbetsyta med [CLI-tillägget](reference-azure-machine-learning-cli.md) för Azure Machine Learning.  Du kan använda CLI för att:

* Skapa ett hanterat beräkningsmål
* Uppdatera ett hanterat beräkningsmål
* Koppla ett ohanterat beräkningsmål

Mer information finns i [Resurshantering](reference-azure-machine-learning-cli.md#resource-management).

## <a name="set-up-with-vs-code"></a>Konfigurera med VS-kod

Du kan komma åt, skapa och hantera beräkningsmål som är associerade med din arbetsyta med hjälp av [VS-kodtillägget](tutorial-train-deploy-image-classification-model-vscode.md#configure-compute-targets) för Azure Machine Learning.

## <a name="submit-training-run-using-azure-machine-learning-sdk"></a><a id="submit"></a>Skicka träningskörning med Azure Machine Learning SDK

När du har skapat en körningskonfiguration använder du den för att köra experimentet.  Kodmönstret för att skicka en träningskörning är detsamma för alla typer av beräkningsmål:

1. Skapa ett experiment som ska köras
1. Skicka körningen.
1. Vänta tills körningen är klar.

> [!IMPORTANT]
> När du skickar träningskörningen skapas en ögonblicksbild av katalogen som innehåller dina träningsskript och skickas till beräkningsmålet. Det lagras också som en del av experimentet på arbetsytan. Om du ändrar filer och skickar körningen igen överförs bara de ändrade filerna.
>
> Om du vill förhindra att filer inkluderas i ögonblicksbilden skapar du en [.gitignore](https://git-scm.com/docs/gitignore) eller-fil `.amlignore` i katalogen och lägger till filerna i den. Filen `.amlignore` använder samma syntax och mönster som [gitignore-filen.](https://git-scm.com/docs/gitignore) Om det finns `.amlignore` båda filerna har filen företräde.
> 
> Mer information finns i [Ögonblicksbilder](concept-azure-machine-learning-architecture.md#snapshots).

### <a name="create-an-experiment"></a>Skapa ett experiment

Skapa först ett experiment på arbetsytan.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=experiment)]

### <a name="submit-the-experiment"></a>Skicka experimentet

Skicka experimentet `ScriptRunConfig` med ett objekt.  Det här objektet innehåller:

* **source_directory**: Källkatalogen som innehåller ditt träningsskript
* **skript:** Identifiera utbildningsskriptet
* **run_config**: Körningskonfigurationen, som i sin tur definierar var utbildningen ska ske.

Om du till exempel vill använda [den lokala målkonfigurationen:](#local)

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=local_submit)]

Växla samma experiment för att köras i ett annat beräkningsmål med hjälp av en annan körningskonfiguration, till exempel [amlcompute-målet:](#amlcompute)

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=amlcompute_submit)]

> [!TIP]
> Det här exemplet är standard bara med en nod i beräkningsmålet för utbildning. Om du vill använda mer `node_count` än en nod anger du körningskonfigurationen till önskat antal noder. I följande kod anges till exempel antalet noder som används för utbildning till fyra:
>
> ```python
> src.run_config.node_count = 4
> ```

Eller så kan du:

* Skicka experimentet `Estimator` med ett objekt som visas i [Train ML-modeller med kalkylatorer](how-to-train-ml-models.md).
* Skicka en HyperDrive-körning för [hyperparameterjustering](how-to-tune-hyperparameters.md).
* Skicka ett experiment via [vs-kodtillägget](tutorial-train-deploy-image-classification-model-vscode.md#train-the-model).

Mer information finns i dokumentationen [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) och [RunConfiguration.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py)

## <a name="create-run-configuration-and-submit-run-using-azure-machine-learning-cli"></a>Skapa körningskonfiguration och skicka körning med Azure Machine Learning CLI

Du kan använda [Azure CLI-](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) och [Machine Learning CLI-tillägg](reference-azure-machine-learning-cli.md) för att skapa körningskonfigurationer och skicka körs på olika beräkningsmål. Följande exempel förutsätter att du har en befintlig Azure Machine Learning `az login` Workspace och du har loggat in på Azure med CLI-kommandot. 

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

### <a name="create-run-configuration"></a>Skapa körningskonfiguration

Det enklaste sättet att skapa körningskonfiguration är att navigera i mappen som innehåller dina Machine Learning Python-skript och använda CLI-kommandot

```azurecli
az ml folder attach
```

Med det här kommandot `.azureml` skapas en undermapp som innehåller mallkörningskonfigurationsfiler för olika beräkningsmål. Du kan kopiera och redigera dessa filer för att anpassa konfigurationen, till exempel för att lägga till Python-paket eller ändra Docker-inställningar.  

### <a name="structure-of-run-configuration-file"></a>Konfigurationsfilens struktur

Körningskonfigurationsfilen är YAML-formaterad, med följande avsnitt
 * Skriptet som ska köras och dess argument
 * Beräkna målnamnet, antingen "lokalt" eller namnet på en beräkning under arbetsytan.
 * Parametrar för körning: ramverk, kommunikatör för distribuerade körningar, maximal varaktighet och antal beräkningsnoder.
 * Miljöavsnitt. Se [Skapa och hantera miljöer för utbildning och distribution](how-to-use-environments.md) för information om fälten i det här avsnittet.
   * Om du vill ange Python-paket som ska installeras för körningen skapar du [conda environment-filen](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#create-env-file-manually)och anger fältet __condaDependenciesFile.__
 * Kör historikinformation för att ange loggfilsmapp och för att aktivera eller inaktivera ögonblicksbilder av utdatasamling och körningshistorik.
 * Konfigurationsinformation som är specifik för det valda ramverket.
 * Information om datareferens och datalager.
 * Konfigurationsinformation som är specifik för Machine Learning Compute för att skapa ett nytt kluster.

Se exemplet [JSON-filen](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json) för ett fullständigt runconfig-schema.

### <a name="create-an-experiment"></a>Skapa ett experiment

Skapa först ett experiment för dina körningar

```azurecli
az ml experiment create -n <experiment>
```

### <a name="script-run"></a>Skriptkörning

Om du vill skicka en skriptkörning kör du ett kommando

```azurecli
az ml run submit-script -e <experiment> -c <runconfig> my_train.py
```

### <a name="hyperdrive-run"></a>HyperDrive-körning

Du kan använda HyperDrive med Azure CLI för att utföra parameterjusteringskörningar. Skapa först en HyperDrive-konfigurationsfil i följande format. Mer information om parametrar för justering av hyperparameter finns i [Tune hyperparametrar](how-to-tune-hyperparameters.md) för din modellartikel.

```yml
# hdconfig.yml
sampling: 
    type: random # Supported options: Random, Grid, Bayesian
    parameter_space: # specify a name|expression|values tuple for each parameter.
    - name: --penalty # The name of a script parameter to generate values for.
      expression: choice # supported options: choice, randint, uniform, quniform, loguniform, qloguniform, normal, qnormal, lognormal, qlognormal
      values: [0.5, 1, 1.5] # The list of values, the number of values is dependent on the expression specified.
policy: 
    type: BanditPolicy # Supported options: BanditPolicy, MedianStoppingPolicy, TruncationSelectionPolicy, NoTerminationPolicy
    evaluation_interval: 1 # Policy properties are policy specific. See the above link for policy specific parameter details.
    slack_factor: 0.2
primary_metric_name: Accuracy # The metric used when evaluating the policy
primary_metric_goal: Maximize # Maximize|Minimize
max_total_runs: 8 # The maximum number of runs to generate
max_concurrent_runs: 2 # The number of runs that can run concurrently.
max_duration_minutes: 100 # The maximum length of time to run the experiment before cancelling.
```

Lägg till den här filen tillsammans med konfigurationsfilerna för körning. Skicka sedan en HyperDrive-körning med:
```azurecli
az ml run submit-hyperdrive -e <experiment> -c <runconfig> --hyperdrive-configuration-name <hdconfig> my_train.py
```

Observera *argumentavsnittet* i runconfig och *parameterutrymme* i HyperDrive config. De innehåller kommandoradsargumenten som ska skickas till utbildningsskriptet. Värdet i runconfig förblir detsamma för varje iteration, medan intervallet i HyperDrive-config itereras över. Ange inte samma argument i båda filerna.

Mer information om ```az ml``` dessa CLI-kommandon och fullständiga argument finns [i referensdokumentationen](reference-azure-machine-learning-cli.md).

<a id="gitintegration"></a>

## <a name="git-tracking-and-integration"></a>Git spårning och integration

När du startar en utbildningskörning där källkatalogen är en lokal Git-databas lagras information om databasen i körningshistoriken. Mer information finns i [Git-integrering för Azure Machine Learning](concept-train-model-git-integration.md).

## <a name="notebook-examples"></a>Exempel på anteckningsbok

Se de här anteckningsböckerna om du vill ha exempel på utbildning med olika beräkningsmål:
* [how-to-use-azureml/training](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Nästa steg

* [Självstudiekurs: Träna en modell](tutorial-train-models-with-aml.md) använder ett hanterat beräkningsmål för att träna en modell.
* Lär dig hur du [effektivt justerar hyperparametrar](how-to-tune-hyperparameters.md) för att skapa bättre modeller.
* När du har en tränad modell kan du lära dig [hur och var du kan distribuera modeller](how-to-deploy-and-where.md).
* Visa [SDK-referens för klassen RunConfiguration.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py)
* [Använda Azure Machine Learning med Virtuella Azure-nätverk](how-to-enable-virtual-network.md)
